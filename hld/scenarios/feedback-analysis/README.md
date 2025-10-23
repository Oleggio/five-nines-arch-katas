
# AI-Assisted Feedback Analysis

## Overview
To increase user satisfaction, build loyalty and expand to new locations in every city,
MobiCorp needs to collect user feedback, analyze, classify it and plan the next action. 
Feedback can be provided by selecting predefined good/bad options, but also in a conversation with AI chatbot.
To reduce costs, this feature could be activated for a subset of users enabled during certain periods of time.

MobiCorp could also dynamically change prompt so that chatbot asks users for certain kinds of feedback: 
- vehicle location,
- ease of driving,
- user interface.

This system could also help with A/B testing if MobiCorp asks for feedback after introducing a new feature to a certain user.

> Note: AI scenario is described in detail in [Appendix B](/requirements/Appendix%20B%3A%20AI%20scenarios%20explained.md).

## Container View

| Container Name   | Functionality Overview                                                                                                                                                                                                                                             |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Feedback Service | Collects feedback either from predefined choices/options or from a conversations with AI chatbot. After a conversation is over it uses same AI model to extract, normalize and classify the feedback as needed. It stores analysis results for further processing. |
| Feedback Store   | Stores processed feedback, which could be further analyzed in analytical pipelines.                                                                                                                                                                                |
| Vertex AI        | Acts as an interface to Gemini or custom AI model ([ADR-0003](../../../adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md)). If Gemini costs rise too much, we could switch to a smaller customer model.                           |

![Diagram](Feedback%20Analysis.drawio.png)

![]()
