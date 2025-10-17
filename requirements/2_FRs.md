# Functional Requirements

This is an architectural document that contains requirement analysis and non-business related information can be added here for better architectural decision-making.
1. Keep core functionality of the rental business (see Apendix A)
2. Enable below scenarios of automating data processing and data exchange (AI-based):

|Area of application | Functional requirements| Data input| Data output | Tech Choice | Comments| 
|:--:|:--:|:--:|:--:|:--:|:--:|
|User Dialogue| User-personalized in-app companionship (chatbot UI with guided chat flow)| Free text from user in chat or pressing a predefined chat button | Text/link answer in chat | Hybrid (GenAI, ML, Code) | Risk of overspending by using GenAI. We will only use for non-predefined questions. |
|User Dialogue| Adaptive route/experience advice for route calculation to user, guiding tips during ride | Free text from user in chat or pressing a predefined chat button| start point, destination, weather, sightseeings, user patterns, duration, price) | MCP | Optional functionality, only if user wants a route. | 
|User Dialogue| Pricing advice to user | Free text from user in chat or pressing a predefined chat button | Text/link answer in chat | Code| Based on Dynamic Pricing functionality |
|User Dialogue| Charging advice to user | (Triggered during route calculation or just from battery level during riding) GPS tracker info, battery sensor info, map | Text recommendation: change route, park or charge on a suggested location(s) | Code, ML| ML only for charging locations & their availability, and traffic conditions |
|User Dialogue| Feedback loop dialogue & user support | Free text from user in chat or pressing a predefined chat button | Text/link answer in chat | Code, ML| Many predefined F.A.Q.|
|Dynamic Pricing| Real-time price calculation (back end) | vehicle type, rental params, weather, user params, user rental patterns, competitor prices, location | pricing options | Hybrid (GenAI, ML, Code) | Dynamic input and changing user patterns and competitor prices|
|Demand Forecasting| Forecast demand per location calculation | area, time of the day, weather, driving/pedestrian traffic conditions, city events, driving/license limitations| area, vehicle type, count, from ts, to ts |ML||
|Maintenance Optimization| Calculate supply (vehicle movement) route and timing, calculate charging tasks for the operational team in the night | location/fleet/route supply optimisation, transportation/charge task assignment | vehicle id, location from, location to, route params, deadline ts, charging needed (Boolean), team id assigned |Hybrid||
|Maintenance Optimization| Calculate recommended load distribution (even rental/usage among vehicles), then suggest a vehicle to book from the location | (1) statistics on historical usage per vehicle type: mileage, duration, profit; (2) mileage and state parameters per each vehicle id | vehicle type, vehicle id, reservation id | ML or GenAI | The more own statistics we have, the more precise we can calculate per the business |
|Maintenance Optimization| forecast maintenance cost/timing |vehicle sensor data, mileage, age | vehicle type, vehicle id, date, maintenance service type, approximate cost | GenAI| We need recent maintenance standards/pricing data per each vehicle model as well as price for specific vehicle error codes|





