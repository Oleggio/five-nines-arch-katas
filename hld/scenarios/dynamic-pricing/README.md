
# Dynamic Pricing Calculation
## Overview
To stay competitive, attract more users and use more vehicles,
MobiCorp applies machine learning to determine the current price based on multiple variables.

## Container View

| Container Name                | Functionality Overview                                                                                                                                                                                                                                                     |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pricing Service               | Aggregates pricing variables and calls Vertex AI to get optimal price.                                                                                                                                                                                                     |
| Vertex AI with ML model       | Hosts an in-house trained ML model ([ADR-0003](../../../adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md)). This model is responsible for providing optimal price based on multiple factors.                                             |
| Competitor Monitoring Service | Responsible for monitoring competitor prices. It can leverage AI agents to find and extract required information [ARD-0010](../../../adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md)). |

![Diagram](Pricing%20Module.drawio.png)
