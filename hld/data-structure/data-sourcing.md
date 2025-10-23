> Note: This document is preliminary analysis output for data sourcing for AI/ML training and parameters used for code logic in AI-related scenarios. Subject to extension.


 # Input DATA required:

- In-app functions, FAQ and rental services options
- User support & feedback data
- Own license setup (territory limitations, rules & policies)
- Own fleet setup
- Own ops team setup (teams, transporting vehicle capacity, repair station capacity)
- Own historical data:
    - user profile
    - user rental behavior patterns
    - reservations, rides, cancellations, all with amounts and vehicle type
    - ride routes info, including preferred stops, speed, etc.
    - vehicle sensor data 
    - vehicle total mileage
    - vehicle charging statistics
    - vehicle maintenance and repair tasks statistics
    - ebike/scooter transporting routes/tasks history
- Local area-specific speed limits and driving/parking regulations
- Competitor pricing per vehicle type per location
- Traffic patterns and congestion
- External transport demand forecasting inputs
- Walking traffic and pedestrian activity
- Real-time and historical multimodal data

 # Potential data sources:
- Google Maps API: 
    - [weather](https://developers.google.com/maps/documentation/weather)
    - [place search](https://developers.google.com/maps/documentation/places/web-service)
    - [roads](https://developers.google.com/maps/documentation/roads)
    - [routes](https://developers.google.com/maps/documentation/routes)
- [Local traffic API sources](https://github.com/graphhopper/open-traffic-collection)
- Competitor websites (pricing sections): Bolt, Dott, GO sharing, SIXT, Cooltra, Europcar, Avis, Hertz
