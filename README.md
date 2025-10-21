# Oreilly' Architectural Katas Q4 2025: AI-Enabled Architecture
> This repository documents the end-to-end architecture thinking process for the given kata scenario prepared by **Five Nines Team**.

---

## 🧭 Table of Contents

- [Overview](#-overview)
  - [About the Project](#about-the-project)
  - [Team](#team)
- [Objectives](#-objectives)
- [Repository Structure](#-repository-structure)
- [Technology Stack Overview](#technology-stack-overview)

---

## Overview

### About the Project

This project is intended to help MobilityCorp achieve its business goals and address its biggest business challenges by offering the most optimal technology stack and architectural solution. The company's goals are: 
- increase sales and revenue
- expand market coverage
- improve user experience
- strengthen its market position.
**Objectives** of the current project will serve as key enablers for these goals. 
<p align="center">
  <img width="653" height="512" alt="Smart Mobility Ecosystem" src="https://github.com/user-attachments/assets/782d6c68-edf7-4071-a86c-b9467cf36390">
</p>

### Team

The project was prepared by the team called 'Five Nines' consisting of:

9 **[Oleh Yermilov](https://www.linkedin.com/in/oleg-yermilov-49a389113/)**

9 . **[Oleksandra Tytar](https://www.linkedin.com/in/otytar/)**

9 **[Dmitry Zinkevich](https://www.linkedin.com/in/zinkevich/)**
    
9 **[Piotr Zyskowski](https://www.linkedin.com/in/piotr-zyskowski-80588329/)**

9 **[Bo Connolly](https://www.linkedin.com/in/boconnolly/)**


## Objectives

Design from scratch (green field architecture) functionality for:
- **User Dialogue** - enhanced sales via user-personalized companionship, adaptive route/experience/pricing/charging advise, and driving compliance guidance
- **Dynamic Pricing** - sales support with more competitive pricing, addresses expansion ambitions and retention goals
- **Demand Forecasting** - uses internal and external data (weather, traffic, events) to forecast demand
- **Maintenance Optimization** (cost reduction) via:
  - Operational efficiency (location/fleet/route supply optimisation, transportation/charge task assignment)
  - Load distribution (even rental/usage among vehicles)
  - Maintenance prediction (sensor data - e.g. battery)
 
  , while ensuring continuous and smooth system operation of the existing core rental functionality.
---

## Repository Structure

- `adrs/` → Architecture Decision Records 
- `requirements/` → Business & technical requirements
- `hld/` → High Level Design artefacts (diagrams, docs)

---

## Technology Stack Overview
| Layer                           | Services / Components                                                                                                                    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Platform / Cloud Provider       | Google Cloud (GCP)                                                                                                                       |
| Edge & Connectivity             | MQTT Gateway (GKE), Device Firmware SDK                                                                                                  |
| Ingestion & Messaging           | Pub/Sub                                                                                                                                  |
| Processing & Integration        | Dataflow, Eventarc, Protocol Adapters / Enrichers                                                                                        |
| Storage & Analytics             | Cloud Bigtable, BigQuery, Cloud Storage                                                                                                  |
| AI & GenAI                      | Vertex AI (AutoML, Pipelines, Feature Store), Gemini, Vertex AI Matching Engine, Prompt Orchestration                                    |
| Application Layer               | Cloud Run, API Gateway, Identity Platform, Business Services (Pricing, Trips, Ops)                                                       |
| Geospatial & Mobility           | Google Maps Platform (Routes, Places, Geocoding, Traffic)                                                                                |
| Security & IAM                  | Cloud IAM, Secret Manager, Cloud Armor                                                                                                   |
| Deployment Pipelines            | GitHub, Cloud Build, Artifact Registry, Doker, Terraform on GCP                                                                          |
| Observability & Ops             | Cloud Monitoring, Cloud Logging, Runbooks, Alert Rules                                                                                   |
| Orchestration & Automation      | Cloud Composer, Vertex AI Pipelines                                                                                                      |
| MLOps                           | Vertex/Gen AI SDK, Vertex AI Model Registry, Vertex AI Feature Store, Vertex AI Model Monitoring, Vertex AI TensorBoard                  |
