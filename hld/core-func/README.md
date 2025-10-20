# Core Functionality Overview

## Container View

| Container Name  | Functionality Overview                                                                               |
|-----------------|------------------------------------------------------------------------------------------------------|
| Mobile App      | User interface to access MobiCorp rental functionality                                               |
| API Gateway     | Provides authentication, push notifications, session management                                      |
| Fleet Service   | Tracks vehicle location, can reserve them, send commands to lock, unlock, disable                    |
| Ride Service    | Initiates the ride, calculates ride total cost, ends ride.                                           |
| Payment Gateway | Reserves payments, charges users.                                                                    |
| Price Service   | Offers dynamic price for each user based on different conditions.                                    |
| Event Backbone  | Message broker to allow services communicate asynchronously for better throughput.                   |
| MQTT Broker     | Relays messages received from vehicles through GCP IoT platform.                                     |
| Vehicle         | Notifies about its location and battery status (charge, temperature).                                |
| User Service    | Keeps user preferences, favourite transport, routes, etc to personalize experience and build loyalty |

![Diagram](Core%20Containers.drawio.png)
