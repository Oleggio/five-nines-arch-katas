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

### Vertex AI Model Evaluation

Vertex AI provides integrated evaluation tools for both traditional ML and GenAI models. At its core, evaluation means:
- Measuring model performance metrics (accuracy, F1, AUC, precision/recall, etc.)
- Comparing predictions vs ground truth
- Logging explainability and bias indicators
We will run evaluation in **Vertex AI Pipelines** (automated CI/CD evaluation).

Scenario #1. Evaluation for a single model (own ML scenarios)
1. Upload a dataset (labeled test data or prompt-response pairs)
2. Run evaluation job
3. **Vertex AI** calculates metrics depending on the model type: Classification, regression, tabular (for custom metrics)
4. Results stored in **Vertex AI Model Registry**

Scenario #2a. Evaluation for a model combination: Parallel models
1. 

Scenario #2b. Evaluation for a model combination: Sequential models

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

