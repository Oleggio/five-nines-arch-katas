# Core User (Mobile App), Operations (Ops UI) & Business + Revenue Features

> Note: Mobile App is minimalistic by features and design. Operations processes are half-manual, the corresponding software is mostly based on GoogleSheets fetching data from database via connectors.

## 🚗 1. Core User Features

- **1-1  User Registration and Login**  
  - Account creation with email, phone, or social login.  
  - Identity verification if regulatory required by region (e.g. attach a valid driving license).

- **1-2  Vehicle Search and Booking**  
  - Find available vehicles (cars, vans, e-bikes, scooters) by location and time.
  - Check price

- **1-3  Booking**
  - Book/cancel booking of a particular vehicle right now (scan scooter) or for defined period in future.
    - **Cars/Vans:** up to 7 days in advance, fixed duration.  
    - **Bikes/Scooters:** up to 30 minutes in advance, open-ended (max 12 hours).
  - **Charge validation (Cars/Vans):** System validates sufficient battery charge before booking (50km/h usage estimate + 20% buffer).
  - **Recurring reservations (Cars/Vans):** Create standing bookings (e.g., every Monday 8am-6pm) for up to 6 months. 24h skip notice required.
  - **Quick rebooking:** One-tap rebook from rental history or save up to 10 favorites for repeat usage patterns.
  - **Booking modification:** Modify date/time/vehicle/location up to 1 hour before rental starts. No modification fee.
  - **Extension during rental (Cars/Vans):** Request extension if no conflicting next booking. Max 2× original duration. Denied if conflict exists.
  - **Zero cancellation fee:** Cancel anytime before rental starts without penalty. Pre-authorization released within 5 minutes.

- **1-4  Lock/Unlock Vehicles via Mobile App**  
  - Lock/unlock through in-app NFC tap or remote server authorization.  
  - Vehicle locks automatically at the end of rental.
  - **NFC primary method (Cars/Vans):** Phone NFC tap (ISO 14443 protocol) unlocks vehicle within 2 seconds.
  - **Remote unlock fallback:** API-based unlock if NFC fails. Max 5 attempts per minute.
  - **Unlock window:** 15 minutes before to 30 minutes after reservation start time.
  - **Late/no-show handling:** 15-minute grace period. After 30 minutes, reservation auto-cancelled with €25 no-show fee (first offense waived).

- **1-5  Real-Time GPS Tracking**  
  - View live location of rented vehicles in the app.
  - **Telemetry ingestion:** GPS, battery %, range, speed data every 30 seconds via Pub/Sub messaging.
  - **Geo-fencing alerts:** Notifications for zone violations or cross-border travel.
  - **Cross-border usage (Cars/Vans):** Vehicles can cross EU borders but must return to origin city. Wrong city return fine: €150 + €1.50/km repatriation cost.

- **1-6  Secure Payment**
  - Add preferred payment method to user profile
  - Supported methods: EU-issued cards, PayPal, Apple Pay, Google Pay, SEPA direct debit.
  - Reserve funds upon booking, capture upon booking/ride start, return funds
  - **Pre-authorization:** Hold = (Duration × 1.5) × Rate + €200 max fines. Released within 24 hours after rental.
  - **Per-minute billing (Cars/Vans):** Precise time-based charging. Final amount calculated at return.
  - **Payment method management:** Add/update/delete methods with PCI-compliant tokenization. Auto-retry failed payments with fallback method.
  - **Multi-currency support:** 6 currencies (EUR, GBP, PLN, SEK, DKK, CZK). Daily exchange rate updates. Price locked at booking time.

- **1-7  Return Flow**  
  - Return vehicles allowed only at designated parking spots.  
  - User submits a **photo proof** of correct return and condition.
  - **Multi-step verification (Cars/Vans):** 
    - GPS validation: ±10m accuracy for correct parking bay.
    - 4 photos required: front exterior, rear exterior, interior, charger connection.
    - AI analysis: Validates charger connected and parking position. Confidence >80% auto-approves, <80% triggers human review (30min/2h SLA).
  - **Return grace period:** 15 minutes after booked end time before late fees apply.

- **1-8  Fees and Charges**  
  - Price calculation based on parameters (vehicle type, period of reservation, type of reservation)
  - Automatic calculation of late-return or damage fees as well as penalties.
  - **Fine types & caps (total cap €200 excluding damage):**
    - **Late return:** €0.50/minute after grace period (cap €100)
    - **Wrong location:** €50 flat + €1.50/km retrieval (cap €300)
    - **Charger blocking:** €10 flat if left >60min after 80% charge
    - **Cleaning fees:** Minor €50, Major €100, Severe €150 (AI-assessed with human review)
    - **No-show:** €25 flat fee if not unlocked within 30 minutes
    - **Damage:** Actual repair cost (uncapped, based on quote)
  - **First-time waivers:** Auto-applied for first offense per fine type (varies by loyalty tier).
  - **Dispute resolution:** 48-hour window to contest charges with evidence. 5-day decision timeline. One escalation allowed.

- **1-9  Loyalty & Rewards**
  - **Tiered membership:** Bronze (default), Silver (10 rentals/month or €500/month), Gold (20 rentals or €1000/month).
  - **Discounts:** Bronze 0%, Silver 10%, Gold 15% off base rates.
  - **Auto-upgrade/downgrade:** Based on rolling 30-day usage patterns.
  - **Gold tier benefits:** Extended fine waivers, priority support, premium vehicle access.

- **1-10  Data Privacy & Rights**
  - **GDPR compliance:** EU data residency. Right to access, rectify, erase, and port data.
  - **Data retention:** Active accounts indefinitely. Closed accounts 90 days. Financial records 7 years. Photos 90 days.
  - **Multi-language support:** 7 languages (EN, DE, FR, ES, IT, NL, PL). Fallback to English if translation missing.
  - **Consent management:** Separate opt-ins for essential processing, analytics, marketing, and profiling.


 
# ⚙️ 2. Operations Features

- **2-1  Fleet Overview Dashboard**
  - View all vehicles per territory, including model, plate, and battery/fuel level.  
  - Filter by status: *Free*, *Reserved*, *Riding*, *Under Maintenance*.

- **2-2  Vehicle Details and History**  
  - Access full servicing and maintenance history per vehicle.

- **2-3  Operational Control**  
  - Remote **lock/unlock** capability.  
  - Remote **engine or motor switch-off** for security or safety, by support request or to stop usage on unsolicited territory.
  - **Remote disable (theft prevention):** Operations Manager role with 2FA can disable vehicle (lock doors, disable ignition, sound alarm). All actions audited. 15-minute re-enable SLA for false alarms.

- **2-4  Task Management**  
  - Assign manual tasks to operations team (battery swap, relocation, cleaning, maintenance).
  - **Crew mobile app:** View assigned tasks, navigation to vehicles, photo upload, completion logging.
  - **Charger blocking prevention:** Auto-detect vehicles at charger >60min after 80%. Notify customer first, dispatch crew if needed.
  - **Partner charging integration:** Integrate partner charging networks. Validate receipts. Auto-reimburse or credit customer accounts.

- **2-5  Fleet Monitoring**  
  - Real-time visibility of vehicle locations and statuses.  
  - Integration with local traffic and regulation data for compliance.
  - **Maintenance tracking:** Vehicle states (Active, Due Soon, Required, In Maintenance, Post-Inspection, Out of Service). 90% odometer threshold triggers alerts.
  - **Automated alerts:** Battery health <80%, sensor malfunctions, 30-day inspection expiration warnings.

- **2-6  Support and Communication**  
  - Users can contact operations (and vice versa) for issue resolution **by phone call only**.
  - **Roadside assistance:** Customer requests help via app. System dispatches nearest crew. SLA: <30min response in urban areas.

## 🏢 3. Business & Revenue Features

- **3-1  Corporate Accounts**
  - B2B account management with bulk pricing and usage controls.
  - Admin portal for employee management, usage caps, and consolidated monthly billing.
  - Volume discounts negotiated per corporate contract.

- **3-2  Dynamic Pricing**
  - Real-time price adjustments based on demand, weather, events, competitor pricing.
  - Surge pricing during high demand (time of day, day of week, local events).
  - Loyalty tier discounts applied automatically.
  - Price transparency: Show base rate + surge percentage to customers.
