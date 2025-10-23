# Oreilly' Architectural Katas Q4 2025: AI-Enabled Architecture
> This repository documents the end-to-end architecture thinking process for the given kata scenario prepared by **Five Nines Team**.

---

## Table of Contents

- [About the Project](#about-the-project)
  - [Team](#team)
  - [Repository Structure](#repository-structure)
- [Problem Statement](#problem-statement)
  - [Business Goals and Drivers](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_0_Business%20goals%20%26%20drivers.md)
  - [Business Challenges](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_1_Business%20challenges.md)
  - [Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)
  - [Non-Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/3_NFRs.md)
  - [Assumptions and Constraints](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/4_Assumptions%20and%20constraints.md)
- [Solution Overview](#solution-overview)
  - [Business Capabilities Mapping](#business-capabilities-mapping)
  - [Target Architecture at a Glance](#target-architecture-at-a-glance)
- [Risks and Mitigations](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/5_Risks%20and%20mitigation.md)
- [Future Scope](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20C%3A%20Future%20scope.md)

---
## About the Project

This project is intended to help MobilityCorp achieve its business goals and address its biggest business challenges by offering the most optimal technology stack and architectural solution. The company's goals are: 
- increase sales and revenue
- expand market coverage
- improve user experience
- strengthen its market position

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

### Repository Structure

- `adrs/` → Architecture Decision Records 
- `requirements/` → Business & technical requirements
- `hld/` → High Level Design artefacts (diagrams, docs)
---

## Problem Statement

Design from scratch (green field architecture) functionality for:
- **User Dialogue** - enhanced sales via user-personalized companionship, adaptive route/experience/pricing/charging advise, and driving compliance guidance
- **Dynamic Pricing** - sales support with more competitive pricing, addresses expansion ambitions and retention goals
- **Demand Forecasting** - uses internal and external data (weather, traffic, events) to forecast demand
- **Maintenance Optimization** (cost reduction) via:
  - Operational efficiency (location/fleet/route supply optimisation, transportation/charge task assignment)
  - Load distribution (even rental/usage among vehicles)
  - Maintenance prediction (sensor data - e.g. battery)
 
  Ensure continuous and smooth system operation of the existing core rental functionality.

#### Requirements Organization

This architecture uses a **two-tier requirements approach**:

**Tier 1: Cross-Cutting View** ([`2_FRs.md`](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md), [`3_NFRs.md`](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/3_NFRs.md))  
High-level overview of AI/ML scenarios across all vehicle types (cars, vans, bikes, scooters), highlighting commonalities and differences.

**Tier 2: Bounded Context Details** ([`core-functionality/car-van-rental/`](https://github.com/Oleggio/five-nines-arch-katas/tree/main/requirements/core-functionality/car-van-rental))  
Implementation-ready specifications for specific domains. Car/van rental includes detailed acceptance criteria, thresholds, and ADR references due to higher AI complexity (cleanliness detection, damage assessment).

This separation balances system-wide architectural thinking with deep domain expertise while respecting bounded context autonomy.

### [Business Goals and Drivers](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_0_Business%20goals%20%26%20drivers.md)

### [Business Challenges](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_1_Business%20challenges.md)

### [Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)
* [Data Model Samples](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/data-structure)

### [Non-Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/3_NFRs.md)

### [Assumptions and Constraints](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/4_Assumptions%20and%20constraints.md)

---

## Solution Overview
Our goal is to design a micro-mobility platform for connected scooters, e-bikes, cars, and vans.
The value proposition focuses on near-real-time telemetry, fleet state, pricing and demand intelligence, maintenance dispatch, and conversational assistants.

First, we integrated a [Trip Copilot](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/route-advice) AI companion into the MobilityCorp mobile app to recommend routes based on preferences, weather, and destination, enhancing customer experience and retention.

Second, we used the AI companion to [gather customer feedback](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/feedback-analysis), which is analyzed to identify improvements and optimal parking bay locations, supporting our expansion strategy.

Next, we focused on vehicle quality and customer satisfaction by ingesting telemetry data into a custom [ML model](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) to predict battery replacement timing and prevent ride failures.

Then, we designed an [ML-powered system](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md) to optimize pricing for customers based on multiple factors.

Lastly, we leveraged historical data and real-time route information to [forecast demand](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/demand-forecasting) and optimize fleet allocation.

### Business Capabilities Mapping

| Business Capability                        | Related FRs                                                                                                              | Related ADR / HLD Document                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|--|
| **Core User Features**                     |   |
| User Registration and Login                | [1-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)   |
| Vehicle Search and Booking                 | [1-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| Booking (short-term / long-term)           | [1-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| Lock/Unlock Vehicles via Mobile App        | [1-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| Real-Time GPS Tracking                     | [1-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md)  |
| Secure Payment                             | [1-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| Return Flow (designated parking, proof)    | [1-7](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| Fees and Charges (penalties, late fees)    | [1-8](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| **Core Operations Features**               |                                                                                                                          |  |
| Fleet Overview Dashboard                   | [2-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) |
| Vehicle Details and History                | [2-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)  |
| Operational Control (lock/unlock, stop)    | [2-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)  |
| Task Management (battery swap, relocation) | [2-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) |
| Fleet Monitoring (status, regulation data) | [2-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br> [Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md)  |
| Support and Communication (user ↔ ops)     | [2-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md) | [Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)  |
| **Advanced / AI-Enabled Capabilities**     |                                                                                                                          | |
| Vehicle Rental & Booking                   | [FR#1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                 | [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) |
| Conversational Assistance (chatbot)        | [FR#2A, FR#2E](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                         | [Trip Copilot](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/route-advice) <br> [ADR-0012 Hybrid AI for User-personalized in-app companionship](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0012%20-%20Hybrid%20AI%20for%20User-personalized%20in-app%20companionship.md) <br> [ADR-0010 Agent Builder](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md) <br>[ADR-0008 - Semantic Search on GCP](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0008%20-%20Semantic%20Search%20on%20GCP.md) |
| Adaptive Route & Ride Experience           | [FR#2B, FR#2D](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                         | [Feedback Analysis](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/feedback-analysis) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) <br> [ADR-0004 Gemini for Maps & Search](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0004%20-%20Use%20of%20Gemini%20for%20Maps%20%26%20Search%20Scenarios.md) <br> [ADR-0021 — Simple ML model for charging advice to user](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0021%20-%20Simple%20ML%20model%20for%20charging%20advice.md) |
| Personalized Pricing Advice                | [FR#2C, FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                         | [Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) |
| Dynamic Pricing Engine                     | [FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                | [Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) <br> [ADR-0018 — Hybrid AI for Dynamic Pricing calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0018%20-%20Hybrid%20AI%20for%20Dynamic%20Pricing%20calculation.md) |
| Demand Forecasting                         | [FR#2G](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                | [Demand Forecasting](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/demand-forecasting) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0006 Knowledge Management](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0006%20-%20Knowledge%20Management%20on%20GCP%20with%20Vertex%20AI%20Integration.md) <br> [ADR-0022 Demand Forecasting Model Selection and Architecture](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0022%20-%20Demand%20Forecasting%20Model%20Selection%20and%20Architecture.md) |
| Fleet Supply & Task Optimization           | [FR#2H](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                | [Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) |
| Load Distribution Optimization             | [FR#2I](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                | [Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) |
| Predictive Maintenance & Cost Forecasting  | [FR#2J](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                | [Battery Replacement](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) <br> [ADR-0019 - Hybrid AI for Maintenance Optimization: Supply Efficiency](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0019%20-%20Hybrid%20AI%20for%20Maintenance%20Optimization%3A%20Supply%20Efficiency.md)|
| **ML Operations**                          |                                                                                                                          |  |
| AI Knowledge Data Management               | [FR#3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)                                 | [ADR-0009 - GenAI Model Management on GCP](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0009%20-%20GenAI%20Model%20Management%20on%20GCP.md) <br> [ADR-0010 - Evaluation and Adoption of Vertex AI Agent Builder for AI Service and Orchestration](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md) <br> [ADR-0011 - Model Evaluation Flow using Vertex AI](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0011%20-%20Model%20Evaluation%20Flow%20using%20Vertex%20AI.md) <br> [ADR-0013 — Adoption of Managed SaaS Model Control Plane (MoCoP)](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0013%20-%20Adoption%20of%20Managed%20SaaS%20Model%20MoCoP.md) <br> [ADR-0014 — ML Model Selection for Cleanliness Assessment](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0014%20-%20ML%20Model%20Selection%20for%20Cleanliness%20Assessment.md) |
| MLOps                                      | [FR#3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)  | [MLOps on Vertex AI & Gemini](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/mlops/MLOps%20on%20Vertex%20AI%20%26%20Gemini.md) <br> [ADR-0015 — Training Data Strategy and Bias Mitigation for Vision Models](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0015%20-%20Training%20Data%20Strategy%20and%20Bias%20Mitigation%20for%20Vision%20Models.md) <br> [ADR-0016 — Human-in-the-Loop Workflow Design for AI Decisions](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0016%20-%20Human-in-the-Loop%20Workflow%20Design%20for%20AI%20Decisions.md) |
| **Analytics Capabilities**                 |  |   |
| Data And Analytics Architecture            |  | [Data and Analytics Technology Architecture](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-data-and-analytics.md)  |

### Technology Stack Overview
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

### Target Architecture at a Glance  

**Edge and connectivity**

- Vehicles use MQTT over cellular
- Clustered MQTT gateway on GKE behind external load balancer
- Gateway authenticates devices, buffers if offline, and publishes telemetry to Pub/Sub topics by region and vehicle type

**Streaming and integration**
- Pub/Sub as event backbone for telemetry and commands
- Dataflow pipelines for parsing, validation, dedupe, geofencing enrichment, road snapping, and fan-out to storage and APIs

**State and data**
- Fleet Service is the single source of truth for vehicle state
- BigQuery for analytical models, reporting, pricing inputs, and demand forecasting features.

**AI and GenAI**
- Vertex AI for model lifecycle and evaluation
- Gemini for map grounded conversational search and ops copilots
- Matching Engine and BigQuery vector search for retrieval and RAG
- Agent Builder for tool calling and orchestration of fleet, maps, and pricing tools
- [MLOps](/hld/mlops/MLOps%20on%20Vertex%20AI%20%26%20Gemini.md) for automating full ML model lifecycle

**Applications**
- Cloud Run microservices for booking, pricing, ops, maintenance
- API Gateway for external and mobile access
- Identity Platform for auth

**Geospatial and UX**
- Maps Platform for routing, POI, geocoding, traffic, and ETA
- Ops UI and mobile client consume APIs and AI assistants


## [Risks and Mitigations](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/5_Risks%20and%20mitigation.md)
## [Future Scope](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20C%3A%20Future%20scope.md)
