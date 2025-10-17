# Functional Requirements

This is an architectural document that contains requirement analysis and non-business related information can be added here for better architectural decision-making.
1. Keep core functionality of the rental business (see Apendix A)
2. Enable below scenarios of automating data processing and data exchange (AI-based):

|Area of application | Functional requirements| Data input| Data output | Tech Choice | Comments| 
|:--:|:--:|:--:|:--:|:--:|:--:|
|User Dialogue| user-personalized companionship(chatbot UI with guided chat flow)| Free-text user question in chat | Text/link answer in chat | Hybrid (GenAI, ML, Code) | Risk of overspending by using GenAI. We will only use for non-predefined questions. |
|User Dialogue| adaptive route/experience advice for route calculation, guiding during ride || start point, destination, weather, sightseeings, user patterns, duration, price) | MCP | Optional functionality, only if user wants a route. | 
|User Dialogue| pricing advice | || Code| Based on Dynamic Pricing functionality |
|User Dialogue| charging advice | || Code, ML| |
|User Dialogue| feedback loop dialogue & user support | |||
|Dynamic Pricing| real-time price calculation | |||
|Demand Forecasting| forecast demand per location calculation | area, time of the day, weather, driving/pedestrian traffic conditions, city events, driving/license limitations| area, vehicle type, count, from ts, to ts ||
|Maintenance Optimization| calculate supply (vehicle movement) route and timing, calculate charging tasks for the operational team in the night | location/fleet/route supply optimisation, transportation/charge task assignment|||
|Maintenance Optimization| calculate recommended load distribution (even rental/usage among vehicles), then suggest a vehicle to book from the location | (1) statistics on historical usage per vehicle type: mileage, duration, profit; (2) mileage and state parameters per each vehicle id | vehicle type, vehicle id, reservation id | ML or GenAI | The more own statistics we have, the more precise we can calculate per the business |
|Maintenance Optimization| forecast maintenance cost/timing |vehicle sensor data, mileage, age | vehicle type, vehicle id, date, maintenance service type, approximate cost | GenAI| We need recent maintenance standards/pricing data per each vehicle model as well as price for specific vehicle error codes|





