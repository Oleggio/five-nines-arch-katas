# MLOps on Vertex AI & Gemini

## Overview 

Enabling AI-driven architecture requires efficient ML Operations (MLOps), which refers to a set of practices and tools that enable automation of the entire lifecycle of ML models — from data preparation, training, and testing to monitoring and updating models in production.

It solves the following problems:
- Scalability for serving a large number of clients
- Standardization of processes across different teams
- Simplifies versioning and change tracking
- Automates training, deployment, and monitoring
- Ensures quality control of models over time
- Provides reproducibility of models
- Reduces the risk of production failures

This document explicates proposed MLOps end-to-end architecture for ML/AI enabled with Vertex AI and Gemini on GCP. 


## Major functional areas

| Area | Tool | Comment |
|--|--|--|
| AI service orchestration | Vertex AI Agent Builder | [ADR-0010](/adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md)|
| ML development env | Vertex AI Workbench | For own ML models only | 
| AI/ML workflow orchestration | Vertex AI Pipelines | Data preparation → training → evaluation → deployment |
| ML artifacts management | Vertex ML Metadata | Helps to analyze, debug, and audit the performance of own ML system |
| AI/ML model versioning | Vertex AI Model Registry | Tracking approved and skipped versions | 
| ML features | Vertex AI Feature Store | Enables re-using ML features at scale and increase the velocity of deploying new ML applications |
| ML monitoring | Vertex AI Model Monitoring | |
| Systematic ML evaluation | Gen AI Evaluation Service | Rubric-based (LLM-as-judge) checks, computation-based metrics (ROUGE/BLEU) and custom metrics (Python functions) for domain-specific scoring |
| Knowledge data management for ML training | BigQuery, Cloud Storage, and Dataflow | [ADR-0006](/adrs/ADR-0006%20-%20Knowledge%20Management%20on%20GCP%20with%20Vertex%20AI%20Integration.md)|


## ML Operations Flow

### High-level Architecture & Process Flow

Here’s a step-by-step view of how data flows through the system and how MLOps is structured:

1. **Data Ingestion & Storage**  
- Raw data (structured, semi-structured) is ingested into BigQuery (or Cloud Storage then staged into BigQuery).  
- Business-metric data (e.g., sales, cost, conversion logs) are loaded into BigQuery for downstream analytics.  
- Feature candidates and training-dataset sources are traced and stored.  

2. **Feature Engineering & Feature Store**  
- Using BigQuery SQL or other transformation engines, features are generated (e.g., aggregations, user behaviour metrics).  
- These features are written to the Vertex AI Feature Store (batch/historic) and optionally served online for real-time inference.  
- This ensures consistent features between training and serving.  

3. **Experimentation / Model Training**  
- A data-science environment (Vertex AI Workbench or notebooks) is used to explore data, build models.  
- Experiments are tracked using Vertex AI Experiments & TensorBoard.  
- When ready, a training pipeline is defined and executed via Vertex AI Pipelines.  
- The pipeline registers metadata via Vertex ML Metadata (parameters, artifacts, metrics).  
- Models (AutoML or custom training) are produced and stored in the Model Registry.  

4. **Model Evaluation & Selection**  
- Models are evaluated (accuracy, precision/recall, latency, cost) and compared.  
- The best model version is promoted from the registry for deployment.  

5. **Deployment**  
- Models are deployed via Vertex AI Model Registry to endpoints (online or batch).  
- Endpoints scale automatically; autoscaling, spot-VM options, etc. can be configured.  
- The serving infrastructure can reference the Feature Store for live features.  

6. **Inference & Business Operation**  
- Application layer (microservice, web app) calls the deployed endpoint for predictions.  
- The predictions (and inputs/outputs) are logged back into BigQuery (or Cloud Logging) with timestamps and identifiers.  

7. **Monitoring & Feedback**  
- Vertex AI Model Monitoring tracks data drift, feature skew, distribution changes, and alert triggers.  
- Business results (e.g., improved conversion, cost savings) are loaded into BigQuery and correlated with model predictions.  
- Dashboards display both model-health metrics (e.g., input distribution, error rate, latency) and business KPI metrics (e.g., lift, ROI, throughput).  

8. **Retraining / Continuous Improvement**  
- Based on triggers (e.g., model performance drop, feature drift, business metric degradation), a new pipeline run is initiated (via Vertex AI Pipelines).  
- New data is ingested, features refreshed, model retrained and redeployed, closing the loop.  


### Vertex AI Model Evaluation

Vertex AI provides integrated evaluation tools for both traditional ML and GenAI models. At its core, evaluation means:
- Measuring model performance metrics (accuracy, F1, AUC, precision/recall, etc.)
- Comparing predictions vs ground truth
- Logging explainability and bias indicators
We will run evaluation in **Vertex AI Pipelines** (automated CI/CD evaluation).

**Scenario #1**. Evaluation for a single model (own ML scenarios)
1. Upload a dataset (labeled test data or prompt-response pairs)
2. Run evaluation job
3. **Vertex AI** calculates metrics depending on the model type: Classification, regression, tabular (for custom metrics)
4. Results stored in **Vertex AI Model Registry** per model version.

**Scenario #2a**. Evaluation for a model combination: Parallel models
> Example: churn = weighted average of 3 models (tree-based, neural net, and rules).
1. The ensemble is deployed as a custom model container or pipeline component in Vertex AI Pipelines.
2. Evaluation component is used to (custom Python step) to: aggregate predictions, compute combined metrics, and log ensemble results back to Vertex ML Metadata or Model Registry
3. Metrics obtained: Weighted accuracy, confidence variance, disagreement ratio, etc.

**Scenario #2b**. Evaluation for a model combination: Sequential models
> Example: LLM (Gemini) → ML scorer → rule-based post-filter → final output
**Each model’s output** is logged in **Vertex AI Experiments**.  
2. The **evaluation pipeline** (in **Vertex AI Pipelines**) compares:
   - Input → intermediate outputs → final decision.  
   - Evaluates both **local model quality** (per stage) and **system-level KPIs** (e.g., user satisfaction, cost, latency).  
3. Use **Vertex AI Model Monitoring** to continuously capture live metrics for each stage.  
4. For **GenAI models** (Gemini, PaLM, or custom LLMs):  
   - Use **Vertex AI Evaluation Service** with *LLM-as-a-judge* or *ground-truth comparisons*.  
   - Evaluate **semantic similarity**, **toxicity**, **factual accuracy**, and **response helpfulness**.

**Scenario #3**. Evaluation for a Multi-Objective model
> Example: route optimization model combining comfort, time, and cost.
1. Define a custom evaluation metric function in your pipeline:
```
def multi_objective_score(route_time, comfort, cost):
    return 0.4 * normalize(time) + 0.3 * comfort + 0.3 * (1 - cost)
```
2. Metric becomes part of the existing Vertex AI Pipeline evaluation step.

   

### Continuous Evaluation and Drift Detection

Vertex AI supports ongoing evaluation through:
|Feature | Purpose |
|:--:|:--:|
|**Model Monitoring** | Detects data drift or prediction drift over time |
| **Explainable AI** | Shows feature importance per model in the ensemble |
| **Vertex AI Experiments** |Tracks performance over different pipeline versions |
|**Evaluation Jobs in Pipelines** | Automates re-evaluation after retraining or deployment |



## Architecture for Combined Model Evaluation

Each model logs metrics independently, while the **fusion evaluator** computes overall system metrics (accuracy, response quality, cost efficiency, etc.).
```
[Input Data] 
     ↓
[Preprocessor / Feature Store] 
     ↓
[Model 1] ─┬─\
            │  \
[Model 2] ──┤   → [Evaluator Component → Vertex AI Metrics]
            │
[Model 3] ──┘
     ↓
[Decision Fusion / Post-Processor]
     ↓
[Vertex AI Registry + Monitoring + Dashboard]

```

## Application Examples

**Example #1 of data flow preparation for ML**

1.	Devices (scooters, sensors) publish data via MQTT: location, riding routes, battery status, error codes, etc.
2.	The MQTT Gateway (GKE) receives the message, validates it, and enriches with metadata.
3.	The Gateway publishes the event to Pub/Sub, which triggers Dataflow for further processing.
4.	Processed data lands in BigQuery / Bigtable for analytics and in Vertex AI for predictive models.

**Example #2 of data flow preparation for ML**

1. Core functionality data is gathered in real time from user mobile applications: booking, 
2. This data is synchronized with BigQuery / Bigtable via pub/sub connectors and night job schedulers for analytics.
3. AI calculates user profile (age group, cost group, ride group) and user behavior patterns (booking, payments, riding, support, cancelations, complaints).
4. Feature store is fetched with user behavior patterns which are also shared with BigQuery / Bigtable for analytics.

**Examples of Data analytics reports related to ML evaluation**

1. Standard deviation of total mileage per vehicle inside of vehicle category (vans, cars, ebikes, skooters)
2. Operational cost dynamics: maintenance cost per vehicle/fleet on territory per period
3. Operational cost dynamics: transporting cost per vehicle/fleet on territory per period, standard deviation of distance per transportation task
4. Operational tasks dynamics: average count of tasks per ops team member per period, standard deviation of task distribution
5. Operational tasks dynamics: average duration of a task per period
6. Battery life statistics
7. Vehicle failure statistics
8. Statistics on unresolved support requests requiring a call with live operator

These statistics will demonstrate trends and can be used as indicators for associated AI/ML models correctness. Ideally, operational and transporting costs should go down, total mileage along with transporting and operational tasks should demonstrate even distribution, etc. If critical business KPIs will worsen, models should be revised/retrained. 

