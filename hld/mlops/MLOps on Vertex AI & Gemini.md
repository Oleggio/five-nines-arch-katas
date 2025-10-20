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
