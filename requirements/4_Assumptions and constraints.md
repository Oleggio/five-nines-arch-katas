# Assumptions and Constraints

## Assumptions
- **Vehicles/devices**: All vehicles offered for rental are electric ones, require regular recharging and corresponding maintenance. Their hardware functions include ride/stop/park, lock/unlock, switch on/off. All devices have a mobile phone holder for comfortable driver experience with the mobile app.
- **Fleet Size**: 200 cars/vans, 500 bikes, and 5,000 scooters is the average fleet size per city. Total company's fleet is increasing with every new location opened. 
- **Mobile Device**: All vehicles are locked/unlocked, reserved and paid for via mobile application (NFC function is required). There is no touch screen or other logical functionality on the device except for basic one: lock/unlock, start/stop
- **Pricing**: Rental pricing is calculated based on rental duration. Options available: **per-ride fixed price** (personalized discounts applied, route should be built and followed, at the destination point the rental is stopped, device is blocked when misused - changing rental model is needed); **duration-based price** (solely for time from rental start to rental end), and **subscription price** (per km or hours prebooked or full-month, cheaper for frequent riders).
- **GPS Tracking**: All vehicles have GPS trackers on them. GPS tracker shares information with the central or regional server every 30 min.
- **User Software**: Mobile application for user experience already exists, covering only core functionality (register, view policies & pricing, reserve, pay, ride, return, lock/unlock, view statistics). See [**Appendix A**](/Appendix-A-core-functionality) for details.  
- **Legacy Architecture**: For functionality covered by the project, there is no legacy software system to migrate from. The solution will be built from scratch (greenfield architecture).


## Constraints
- **Market/Coverage**: EU-only operations (urban and suburban).
- **Mobile Device**: Only NFC-equipped models can be used for rental (related to lock/unlock mobile app functionality). 
- **Compliance**: Different city licenses & mobility regulations, which define rental and driving limitations. User is solely responsible for own driving permit on the territory of rental/usage.
- **Privacy**: GDRP defines limitations of personal data usage, retention and non-disclosure.
- **AI Regulation**: AI/ML usage regulation under EU AI Act (Regulation (EU) 2024/1689) as well as any applicable.





