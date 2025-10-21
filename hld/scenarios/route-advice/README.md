
# AI-enhanced Route Advice System
## Overview
The goal of this module is to provide route/experience advice to user based on their needs, fleet availability and weather conditions.
This should help us achieve the following business goals
* Revenue growth 
* Increase in customer satisfaction

## Container View

| Container Name                 | Functionality Overview                                                                                                                                                                                                                                                                                                                                                                                                              |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fleet Service                  | Receives telemetry from vehicles. It is integrated with GCP IoT Platform via Pub/Sub. Vehicles on the other hand are integrated with GCP IoT platform via MQTT protocol ([ADR-0002](../../../adrs/ADR-0002%20-%20Vehicle%20telemetry%20&%20integration%20stack.md)). It tracks all vehicle battery levels and locations to quickly identify vehicles that meet user demand.                                                         |
| Gemini API with Maps grounding | Provides contextual location search and returns route and POI recommendations ([ADR-0004](../../../adrs/ADR-0004%20-%20Use%20of%20Gemini%20for%20Maps%20&%20Search%20Scenarios.md)). Determines the optimal route based on user prompt and current conditions - weather for bikes and scooters, traffic for cars and vans. Traffic can be obtained by defining a custom function that would call Google Routes API in Gemini model. |
| Weather MCP Server             | 3rd party Model Context Protocol implementation that would allow the model to know weather conditions in a certain location.                                                                                                                                                                                                                                                                                                        |
| Route Recommendation Service   | Receives route information from the user, including start and end location and a message describing what they would like to see along the route. The service is responsible for aggregating all information required to generate route recommendations. It also collects and stores user feedback to calculate satisfaction metrics.                                                                                                |

![Diagram](Scooter_Bike%20Route%20Recommendation.drawio.png)
