# Oreilly' Architectural Katas Q4 2025: AI-Enabled Architecture
> This repository documents the end-to-end architecture thinking process for the given kata scenario prepared by **Five Nines Team**.

---
## Table of Contents

- [About the Project](#about-the-project)
  - [Team](#team)
  - [Repository Structure](#repository-structure)
- [Solution Overview](#solution-overview)
  - [Objectives](#objectives)
  - [Business Capabilities](#business-capabilities)
  - [Target Architecture at a Glance](#target-architecture-at-a-glance)

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
## Solution Overview
Our goal is to design a micro-mobility platform for connected scooters, e-bikes, cars, and vans. The value proposition focuses on near-real-time telemetry, fleet state, pricing and demand intelligence, maintenance dispatch, and conversational assistants. 

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

### Business Capabilities

| Business Capability                       | Related FRs         | Related ADR / HLD Document |
|-------------------------------------------|---------------------|----------------------------|
| **Core User Features**                    |                     |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| User Registration and Login               | [1-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                            |
| Vehicle Search and Booking                | [1-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Booking (short-term / long-term)          | [1-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Lock/Unlock Vehicles via Mobile App       | [1-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Real-Time GPS Tracking                    | [1-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md)                            |
| Secure Payment                            | [1-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Return Flow (designated parking, proof)   | [1-7](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Fees and Charges (penalties, late fees)   | [1-8](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| **Operations Features**                   |                     |                            |
| Fleet Overview Dashboard                  | [2-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Vehicle Details and History               | [2-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Operational Control (lock/unlock, stop)   | [2-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) [Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) |
| Task Management (battery swap, relocation)| [2-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| Fleet Monitoring (status, regulation data)| [2-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) [Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) |
| Support and Communication (user ↔ ops)    | [2-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| **Advanced / AI-Enabled Capabilities**    | | |
| Vehicle Rental & Booking                  | [FR#1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)|                            |
| Conversational Assistance (chatbot)       | [FR#2A, FR#2E](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Trip Copilot](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/route-advice)                            |
| Adaptive Route & Ride Experience          | [FR#2B, FR#2D](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Feedback Analysis](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/feedback-analysis)                            |
| Personalized Pricing Advice               | [FR#2C, FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md)                            |
| Dynamic Pricing Engine                    | [FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md)                            |
| Demand Forecasting                        | [FR#2G](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |                            |
| Fleet Supply & Task Optimization          | [FR#2H](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation)                            |
| Load Distribution Optimization            | [FR#2I](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation)                                        |
| Predictive Maintenance & Cost Forecasting | [FR#2J](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Battery Replacement](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation)                            |

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

**Applications**
- Cloud Run microservices for booking, pricing, ops, maintenance
- API Gateway for external and mobile access
- Identity Platform for auth

**Geospatial and UX**
- Maps Platform for routing, POI, geocoding, traffic and ETA
- Ops UI and mobile client consume APIs and AI assistants
