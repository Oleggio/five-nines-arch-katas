# Demand Forecasting

## Overview
To improve vehicle utilization, increase user satisfaction, and match actual demand,
MobiCorp needs to record vehicle usage patterns and predict where to expect the highest demand throughout the day.
There is historical data available that can be ingested into a custom ML model via Vertex AI Pipeline.
Daily data is stored throughout the day by the Ride Service, and later it is also ingested by the ML model.
To mitigate the risk of incorrect predictions, MLOps members can monitor the model in production using Vertex AI Model Monitoring.

**Architecture Decisions:** See [ADR-0022 — Demand Forecasting Model Selection and Architecture](../../../adrs/ADR-0022%20-%20Demand%20Forecasting%20Model%20Selection%20and%20Architecture.md) for model selection rationale, staged approach (Prophet → ML), and accuracy targets.

## Container View

| Container Name             | Functionality Overview                                                                                                                                                                                                                                                                 |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vertex AI with ML model    | Hosts an in-house trained ML model ([ADR-0003](../../../adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md)). This model is should predict vehicle demand in near parking bay at a certain time of day. Data can be ingested using Vertex AI Pipeline. |
| Vertex AI Model Monitoring | GCP-native tool to monitor model in production for data drift. It can alert MLOps team when there is data drift in the model.                                                                                                                                                          |
| Maintenance Service        | Aks model where to move vehicle in a given time of day. This information is provided to crew members, so they could move available vehicles to the location of the greatest demand.                                                                                                    |
| Ride Service               | Stores ride information in Cloud Storage [ADR-0006](../../../adrs/ADR-0006%20-%20Knowledge%20Management%20on%20GCP%20with%20Vertex%20AI%20Integration.md) for ingestion by ML model.                                                                                                   |

![Diagram](Demand%20Forecasting.drawio.png)
