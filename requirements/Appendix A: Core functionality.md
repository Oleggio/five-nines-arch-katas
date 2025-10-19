# Core User (Mobile App) & Operations (Ops UI) Features

> Note: Mobile App is minimalistic by features and design. Operations processes are half-manual, the corresponding software is mostly based on GoogleSheets fetching data from database via connectors.

## 🚗 Core User Features

- **User Registration and Login**  
  - Account creation with email, phone, or social login.  
  - Identity verification if regulatory required by region (e.g. attach a valid driving license).

- **Vehicle Search and Booking**  
  - Find available vehicles (cars, vans, e-bikes, scooters) by location and time.
  - Check price

- **Booking**
  - Book/cancel booking of a particular vehicle right now (scan scooter) or for defined period in future.
    - **Cars/Vans:** up to 7 days in advance, fixed duration.  
    - **Bikes/Scooters:** up to 30 minutes in advance, open-ended (max 12 hours).

- **Lock/Unlock Vehicles via Mobile App**  
  - Lock/unlock through in-app NFC tap or remote server authorization.  
  - Vehicle locks automatically at the end of rental.

- **Real-Time GPS Tracking**  
  - View live location of rented vehicles in the app.

- **Secure Payment**
  - Add preferred payment method to user profile
  - Supported methods: EU-issued cards, PayPal, Apple Pay, Google Pay, SEPA direct debit.
  - Reserve funds upon booking, capture upon booking/ride start, return funds

- **Return Flow**  
  - Return vehicles allowed only at designated parking spots.  
  - User submits a **photo proof** of correct return and condition.

- **Fees and Charges**  
  - Price calculation based on parameters (vehicle type, period of reservation, type of reservation)
  - Automatic calculation of late-return or damage fees as well as penalties.


 
# ⚙️ Operations Features

- **Fleet Overview Dashboard**
  - View all vehicles per territory, including model, plate, and battery/fuel level.  
  - Filter by status: *Free*, *Reserved*, *Riding*, *Under Maintenance*.

- **Vehicle Details and History**  
  - Access full servicing and maintenance history per vehicle.

- **Operational Control**  
  - Remote **lock/unlock** capability.  
  - Remote **engine or motor switch-off** for security or safety, by support request or to stop usage on unsolicited territory.  

- **Task Management**  
  - Assign manual tasks to operations team (battery swap, relocation, cleaning, maintenance).

- **Fleet Monitoring**  
  - Real-time visibility of vehicle locations and statuses.  
  - Integration with local traffic and regulation data for compliance.

- **Support and Communication**  
  - Users can contact operations (and vice versa) for issue resolution **by phone call only**.
