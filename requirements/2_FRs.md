# Functional Requirements

This is an architectural document that contains requirement analysis and non-business related information can be added here for better architectural decision-making.
1. Keep core functionality of the rental business (see Apendix A)
2. Enable below scenarios of automating data processing and data exchange (AI-based):

|Area of application | Functional requirements| Tech Choice | Comments| 
|:--:|:--:|:--:|:--:|
|User Dialogue| user-personalized companionship(chatbot ui with guided chat flow)| Hybrid (GenAI, ML, Code)| Risk of overspending by using GenAI |
|User Dialogue| adaptive route/experience advice ( start-destination, weather, sightseeings, user patterns, duration, price) | MCP | | 
|User Dialogue| pricing advice | Code| |
|User Dialogue| charging advice | Code, ML| |
|User Dialogue| feedback loop dialogue & user support |
|Dynamic Pricing| real-time price calculation |
|Demand Forecasting| forecast demand per location calculation (weather, traffic, city events, driving limitations)|
|Maintenance Optimization| perational efficiency (location/fleet/route supply optimisation, transportation/charge task assignment)|
|Maintenance Optimization| load distribution (even rental/usage among vehicles)|
|Maintenance Optimization| maintenance cost/timing prediction (based on sensor data)|





