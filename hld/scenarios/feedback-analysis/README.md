
# AI-assisted Feedback Analysis
## Overview
MobiCorp wants to increase user satisfaction, build loyalty and expand to new locations in every city.
To achieve this goal, it needs to collect user feedback, analyze, classify it and plan the next action. 
Feedback can be provided by selecting predefined good/bad options, but also in a conversation with AI chatbot.
To reduce costs, this feature could be activated for a subset of users enabled during certain periods of time.

MobiCorp could also dynamically change prompt so that chatbot asks users for certain kinds of feedback: 
- Vehicle location
- Ease of driving
- User interface
- etc

This system could also help with A/B testing if MobiCorp asks for feedback after introducing a new feature to a certain user.

## Container View

* `Feedback Service` initiates either stores feedback from predefined choices/options or initiates a conversations. 
After a conversation is over it uses same model to extract and normalize the feedback and classify it as needed.
It keeps it locally for further processing.

![Diagram](Feedback%20Analysis.drawio.png)

![]()
