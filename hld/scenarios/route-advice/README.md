
# Route Advice System
## Overview
The goal of the system is to provide route/experience advice to user based on their needs, fleet availability and weather conditions.
This should help us achive the following business goals
* Revenue growth 
* Customer satisfaction increase

## Container View

* `Route Recommendation Service` gets route information from the user, including start and end location and and a message describing what they would like to see along the route.
The service is responsible for aggregating all information required to generate route recommendations:
  * Based on the route's starting point and destination, it retrieves interesting locations along the way. In the future, this could be enhanced to suggest locations based on the user's past preferences.
  * It retrieves weather information to adjust the estimated ride duration accordingly.
  * It collects and stores user feedback to calculate satisfaction metrics.
* `Vertex AI` acts as a facade for the large language model. It receives all aggregated route information as a prompt and 
generates a personalized route proposal for a user.

![Diagram](Scooter_Bike%20Route%20Recommendation.drawio.png)
