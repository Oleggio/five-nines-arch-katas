# ADR-0004 - Use of Gemini for Maps & Search Scenarios
## Context
Our mobility platform requires:
- Contextual search (e.g., POIs, routes, addresses, geofences, stations).  
- Route-based recommendations (e.g., charging points, parking availability, nearby landmarks).  
- Geospatial Q&A (e.g., “Where is the nearest charging station on my way?”).  
- Integration with real-time map and traffic data for adaptive routing and service allocation.

While these capabilities can be implemented using traditional Maps APIs, they are often rigid and require complex orchestration logic on our side to deliver personalized and conversational results. The new Gemini APIs, specifically for Maps grounding, enable natural language queries directly combined with Maps and Search data.

## Decision
We will use **Google Gemini APIs with Maps grounding** to handle geospatial and search-related scenarios such as:
- Contextual location search
- Route and POI recommendations
- Conversational map queries
- Dynamic re-routing and assistance for operations

Gemini will be used as a reasoning and orchestration layer, while Maps Platform provides authoritative geospatial data and routing.

## Key Differentiators

- Native integration with Maps and Search, avoiding complex middleware.  
- Natural language queries allow end users to interact more intuitively.  
- Gemini can chain multiple Maps API calls and provide summarized, context-rich answers.  
- Reduces backend orchestration complexity.  
- Results rely on up-to-date Maps data.  
- Single provider simplifies security and scaling.

## Considerations
- **Architecture:** Gemini becomes part of the conversational and routing layer.  
- **Integration:** Gemini APIs are called from backend services; results are enriched with operational context (fleet, users, availability).  
- **Security:** IAM, service accounts, and quota controls required.  
- **Performance:** Low-latency expected; Maps API remains as fallback.  
- **Risks:** Dependency on vendor service evolution and availability.

## Alternatives Considered
| Option                              | Pros                                               | Cons                                                      |
|-------------------------------------|----------------------------------------------------|-----------------------------------------------------------|
| Traditional Maps API only           | Mature, deterministic                              | Requires more orchestration, limited conversational logic |
| External geospatial search         | Flexible, less vendor lock-in                      | More integration overhead, weaker routing integration     |
| In-house NER + search              | Full control                                       | High cost, limited coverage, lower accuracy               |
| Gemini + Maps grounding (Chosen)   | Native integration, minimal glue code, smart logic | Vendor lock-in, dependency on service availability        |

## Risks & Trade-offs
| Risk Area | Description | Mitigation |
|--|--|--|
| Vendor lock-in                   | Strong dependency on Gemini and Maps grounding may make migration difficult.                             | Use abstraction layers for geospatial and conversational APIs; document fallback to traditional Maps APIs.  |
| Service availability             | Outages or changes to Gemini APIs could impact search and routing features.                              | Implement fallback paths to traditional Maps APIs for critical operations.                                  |
| Cost management                  | High query volumes for conversational and geospatial search may lead to unpredictable API costs.          | Define usage quotas, implement caching for repeated queries, and monitor cost dashboards.                   |
| Latency sensitivity              | Conversational queries can introduce additional latency compared to direct Maps API calls.                | Use hybrid architecture: direct API for time-critical routing, Gemini for contextual enhancements.          |
| Feature dependency               | Heavy reliance on Gemini orchestration features may reduce flexibility if APIs change.                    | Keep orchestration logic modular; monitor vendor roadmap and prepare adaptation plans.                      |
| Data governance & compliance     | Using AI-assisted search introduces questions around data handling, logging, and auditability.            | Configure access controls, log usage, and enforce compliance with internal and external regulations.         |
| Skills gap                       | Teams may be less familiar with Gemini capabilities and grounding concepts.                              | Provide training, create internal documentation, and offer reference integrations.                          |
| Ecosystem integration            | Tightly coupling to Gemini may limit future integration with other mapping or search providers.           | Maintain clear separation between conversational layer and routing layer in architecture.                   |

## Conclusion
Gemini aligns best with our goals for conversational, context-aware, map-grounded interactions with minimal integration effort. Traditional Maps APIs will be retained as fallback.

## References

- [Grounding Google Maps in Gemini API](https://blog.google/technology/developers/grounding-google-maps-gemini-api/)  
- [Google Maps Platform Documentation](https://developers.google.com/maps)  
