
# Battery Replacement Process
## Overview
In order to increase vehicle utilization, increase use satisfaction and match demand MobiCorp needs to replace batteries before they completely run out of charge. The replacement charge threshold depends on the average distance users travel on each type of vehicle and changes rarely.


## Container View

* `Fleet Service` receives almost real-time updates about vehicle locations and charge levels. When it notices that a charge level has fallen below the threshold it marks vehicle as unavailable for booking and notifies the user to stop the ride due to low battery level. User is not obliged to stop the ride immediately, and can even continue using a completely discharged vehicle. The only restriction is 12 hours, after which the vehicle will be locked remotely.
* `Maintenance Service` records all maintenance requests. Then it algorithmically picks the next vehicle to maintain and notifies members of maintenance crew riding around in a van where to go to locate the vehicle and where to move it to based on current demand. Demand prediction is described in another scenario.

 
![Diagram](Battery%20Replacement.drawio.png)
