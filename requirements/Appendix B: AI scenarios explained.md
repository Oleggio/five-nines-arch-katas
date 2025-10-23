# Appendix B: AI Scenarios Explanation

> Note: This document is supplementary to FRs part about AI/ML usage and explains functionality application scenarios in more detail.

| AI Functionality| Area | Scenario Explanation |
|:--:|:--:|:--:|
| User-personalized in-app companionship (chatbot UI with guided chat flow) | User | Chat-based navigation inside of mobile app to allow user ask questions or initiate any available function. Via free-text question or by pressing a predefined button: Ask about vehicles rental & pricing, search available ones nearby, initiate booking, calculate a route, request support, check parking nearby.|
| Adaptive route/experience advice | User | Before riding - Optional route calculation to user by various parameters. May switch on/off guiding tips during ride (turn left/right, battery is low - here are charging stations and parkings nearby. Informing about speed and territory limits.|
| Pricing advice to user | User| Based on dynamic pricing back-end functionality (see explained below), inform user about available pricing options in the chat and suggest optimization/choice between per ride vs subscription rent and advertise each to promote more sales.|
| Charging advice to user | User| Based on rented vehicle's battery state and its current location as well as map info, push-notification or voiced recommendation: change route, park or charge on a suggested location(s). Triggered during route calculation or just from battery level during riding. |
| Feedback loop dialogue & user support | User | Chat-based communication with user encouraging user feedback and ensuring most efficient user support at any given time/situation |
|Real-time price calculation | Back-end | Dynamically calculated price of vehicle rental for a certain user based on vehicle type, rental params, weather, user params, previous user rental patterns, **competitor prices**, location, etc. |
|Forecast demand per location calculation | Back-end | Internal system function that is used as input for supply calculation - where to put available vehicles and when to achieve maximal rental volumes. Dynamically calculated based on area, time of the day, weather, driving/pedestrian traffic conditions, city events, driving and company license limitations, etc. |
|Calculate supply | Ops | Automatically generated and preassigned tasks for ops team members about vehicle movement route and timing, charging and tasks. Even distribution between ops team members, optimal routes. ||
|Calculate recommended load distribution | Back-end | Calculate even rental/usage among vehicles. Apply when user initiates booking and suggest a vehicle to book from the location based on calculated parameters |
|Forecast maintenance cost/timing| Ops | Dashboard with predicted maintenance costs for the fleet per period, can be used for budgeting ||
|Automate ML model lifecycle and data management | MLOps | Internal system functionality that manages the complete lifecycle of all ML models used in the platform (pricing, vehicle condition assessment, demand forecasting, maintenance prediction). Automatically handles model versioning, A/B testing of model performance, deployment decisions based on performance metrics, and monitoring alerts for model drift or degradation. Includes continuous evaluation pipelines and automated retraining when performance drops below thresholds.|
