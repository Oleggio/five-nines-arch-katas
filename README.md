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

### Objectives

Design from scratch (green field architecture) functionality for:
- **User Dialogue** - enhanced sales via user-personalized companionship, adaptive route/experience/pricing/charging advise, and driving compliance guidance
- **Dynamic Pricing** - sales support with more competitive pricing, addresses expansion ambitions and retention goals
- **Demand Forecasting** - uses internal and external data (weather, traffic, events) to forecast demand
- **Maintenance Optimization** (cost reduction) via:
  - Operational efficiency (location/fleet/route supply optimisation, transportation/charge task assignment)
  - Load distribution (even rental/usage among vehicles)
  - Maintenance prediction (sensor data - e.g. battery)
 
  , while ensuring continuous and smooth system operation of the existing core rental functionality.

### [Business Goals and Drivers](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_0_Business%20goals%20%26%20drivers.md)

### [Business Challenges](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/1_1_Business%20challenges.md)

### [Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)

### [Non-Functional Requirements](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/3_NFRs.md)

### [Assumptions and Constraints](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/4_Assumptions%20and%20constraints.md)

### Business Capabilities

| Business Capability                       | Related FRs         | Related ADR / HLD Document |
|-------------------------------------------|---------------------|----------------------------|
| **Core User Features**                    | | |
| User Registration and Login               | [1-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| Vehicle Search and Booking                | [1-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| Booking (short-term / long-term)          | [1-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| Lock/Unlock Vehicles via Mobile App       | [1-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| Real-Time GPS Tracking                    | [1-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md)|
| Secure Payment                            | [1-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Return Flow (designated parking, proof)   | [1-7](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| Fees and Charges (penalties, late fees)   | [1-8](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md)                             |
| **Operations Features**                   |                     |                            |
| Fleet Overview Dashboard                  | [2-1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)|
| Vehicle Details and History               | [2-2](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)|
| Operational Control (lock/unlock, stop)   | [2-3](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br>[Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)|
| Task Management (battery swap, relocation)| [2-4](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)|
| Fleet Monitoring (status, regulation data)| [2-5](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) <br> [Vehicle Connectivity HLD](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/hld-vehicle-connectivity.md) <br> [ADR-0002 Vehicle telemetry & integration stack](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0002%20-%20Vehicle%20telemetry%20%26%20integration%20stack.md)|
| Support and Communication (user ↔ ops)    | [2-6](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20A%3A%20Core%20functionality.md)                   |[Core Functionality](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/core-func/README.md) |
| **Advanced / AI-Enabled Capabilities**    | | |
| Vehicle Rental & Booking                  | [FR#1](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)| [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) |
| Conversational Assistance (chatbot)       | [FR#2A, FR#2E](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Trip Copilot](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/route-advice) <br> [ADR-0012 Hybrid AI for User-personalized in-app companionship](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0012%20-%20Hybrid%20AI%20for%20User-personalized%20in-app%20companionship.md) <br> [ADR-0010 Agent Builder](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md)|
| Adaptive Route & Ride Experience          | [FR#2B, FR#2D](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Feedback Analysis](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/feedback-analysis) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md) <br> [ADR-0004 Gemini for Maps & Search](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0004%20-%20Use%20of%20Gemini%20for%20Maps%20%26%20Search%20Scenarios.md)|
| Personalized Pricing Advice               | [FR#2C, FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)        |[Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md)|
| Dynamic Pricing Engine                    | [FR#2F](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Dynamic Pricing Calculation](https://github.com/Oleggio/five-nines-arch-katas/blob/main/hld/scenarios/dynamic-pricing/README.md) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md)|
| Demand Forecasting                        | [FR#2G](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               | [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0006 Knowledge Management](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0006%20-%20Knowledge%20Management%20on%20GCP%20with%20Vertex%20AI%20Integration.md)|
| Fleet Supply & Task Optimization          | [FR#2H](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md)n|
| Load Distribution Optimization            | [FR#2I](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Fleet Allocation](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md) <br> [ADR-0005 AI-Based Route Optimization](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0005%20-%20AI-Based%20Route%20Optimization%20for%20Rental%20Vehicles.md)|
| Predictive Maintenance & Cost Forecasting | [FR#2J](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/2_FRs.md)               |[Battery Replacement](https://github.com/Oleggio/five-nines-arch-katas/tree/main/hld/scenarios/battery-replacement-and-fleet-allocation) <br> [ADR-0003 Vertex AI as core AI/GenAI platform](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) <br> [ADR-0007 Fleet Service](https://github.com/Oleggio/five-nines-arch-katas/blob/main/adrs/ADR-0007%20-%20Fleet%20Service%20as%20the%20Single%20Source%20of%20Truth%20for%20Vehicle%20State.md)|

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

### [Risks and Mitigations](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/5_Risks%20and%20mitigation.md)
### [Future Scope](https://github.com/Oleggio/five-nines-arch-katas/blob/main/requirements/Appendix%20C%3A%20Future%20scope.md)
