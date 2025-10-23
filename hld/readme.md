Current folder presents high level design for
- [core functionality](/hld/core-func/), including detailed [car/van rental bounded context](/hld/core-func/car-van-rental/) with context diagrams, domain models, and sequence diagrams,
- [AI-based scenarios](/hld/scenarios/) described in FRs section (cross-cutting AI features like demand forecasting and route optimization),
- [MLOps](/hld/mlops/),
- and includes several [data structure samples](/hld/data-structure/) for those.

**Note on AI scenario placement:** AI features tightly coupled to specific business workflows (e.g., cleanliness verification at return, damage detection for fines) are documented within their bounded context ([car/van rental](/hld/core-func/car-van-rental/)). Cross-cutting AI scenarios applicable to all vehicle types (demand forecasting, route optimization) are in [/hld/scenarios/](/hld/scenarios/). This follows domain-driven design principles: context-specific AI logic stays with domain models, while shared AI services are extracted for reusability.
