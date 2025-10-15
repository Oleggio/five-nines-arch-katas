# Q4200025 Architectural Katas — Complete Transcript

## Facilitator (Joan) — starts 00:00:00.040

- "Welcome, everyone, and thank you for joining the Q4200025 KATAS Kickoff event."
- "I'm Joan. I'll be your facilitator."
- "We're excited to have you here."
- "We'll be kicking things off shortly, but before we dive into the content, I'd like to go over a few quick housekeeping notes to help you get the most out of today's live event."
- "At the bottom of your screen, you'll see a set of widgets, and the widgets we'll be using the most today are the group chat and the Q&A."
- "We do encourage you to jump into the chat and be a part of the conversation."
- "This event is being recorded, including any participation from attendees, so if you take part via chat or the Q&A, we just ask that you please not post personal information."
- "For attendees who are subject to regulated status, the chat feature should not be used."
- "The audience may include customers, vendors, or competitors of you or your employer. If you choose to take advantage of the interactive features available in this live event, and we hope that you will, you are responsible for any information you choose to share."
- "You will receive an e-mail within the next 24 to 48 hours with a link to the recording so you can review or catch up on anything you've missed."
- "And our host for today's event is Sam Newman. He is a technologist focusing on the areas of cloud, micro services and continuous delivery, 3 topics which seem to overlap. He also provides consulting, training and advisory services to startups and large multinational enterprises alike, drawing on his more than 20 years in IT as a developer, sys admin and architect. He is also an author and experience conference speaker."
- "Now we'll get started and I'll hand things over to our host. Hi, Sam."

## Host (Sam Newman) — starts 00:01:55.800

### Opening and what a kata is — starts 00:01:55.800

- "Hi, Joan."
- "Thank you so much indeed for the lovely introduction and thank you everybody to coming along today to participate in the architectural characters for Q4."
- "We're looking at the whole idea of creating architecture which can be AI enabled and we've kind of got a fun thing for you today."
- "This slide being a little bit of a of a teaser regarding the problem that you're going to be solving for all of us within your teams."
- "Before we get into it, sharing with you what with the what the problem is going to be and talking through the process by how these cat is going to run."
- "I thought it'd be a good idea to just really explain what a cat is and what the purpose of a cat is."
- "Now the concept of a cat is actually first came from the area of martial arts."
- "A catter was something which you would do over and over again to practice a move or set of moves or practice a technique."
- "We then see catters being used, the concepts of code. The really useful Code Catter site which I used to use years ago would give you like a little problem to solve."
- "So in trying to learn a new programming language, you could go get a problem from the Code Catter site and come up with a little bit of a solution for that."
- "The idea being that it's not a real world use of your programming skills, but you're getting to sort of practice something."
- "You're getting to try something out, build something kind of real, but without necessarily needing all the time and energy resources to do that."
- "Now, of course, with coding we can write something and turn it around quite quickly."
- "Architecture becomes that little bit more difficult again."
- "And so then we move on to the architectural catches that Ted New had came up with many years ago."
- "And Ted's idea was, well, we've got code catters, why don't we come up with some architectural catters?"
- "So with an architectural catter, you outline a problem, a context, and then you try and come up with an architecture to solve that particular problem."
- "One of the challenges, of course, being that whenever you look at an architecture for a system, there can often be lots of right ways in which to solve it."
- "And so one of the things we're going to be looking for you to do is to look at all the different, you know, bring you together, your skills, your understanding of technology and the context to them."
- "Propose an architecture to solve the problems of this particular organization."
- "This is an opportunity for you to practice the art of architecture, which is not just about coming up with the right technology."
- "It's about thinking about the system holistically, understanding where trade-offs need to be made."
- "Architecture is also very much about communication."
- "If you're responsible for coming up with an architecture, that's not enough, right?"
- "You have to communicate with people, with the people around you that will then build that system."
- "Although, of course, in the context of an architectural caster, the people you're going to be communicating are actually our judges who are going to be looking at your solution and trying to understand your mindset behind it."
- "And trying then looking at all the different teams and all the different submissions we get to identify, OK, which teams have approached this problem in the right way, the best way."
- "And so yes, it's a bit of a competitive element to this, but a big part of this is how effective you are telling your story around your proposed solution."
- "And just to reiterate, that's not an artificial thing for the concept of this particular competition."
- "The ability to communicate and explain your architecture would be key in any scenario in a workplace."
- "Thinking this is the right architecture is only was less than half the battle."
- "The bigger piece then is influencing people around you, explaining your ideas so that your ideas can become battle tested and improved and helping people to implement them."
- "So through in today's classes, you're going to learn a whole load of new techniques in terms of just practicing these ideas, but also getting better at communicating as well and thinking of different ways in which you can communicate and share the concepts around your architecture in question."

### Problem brief: Mobility Corp — starts 00:07:01.360

- "So what we're going to be doing from each of you is we're going to give you a realistic example of a company and I've made-up a sort of a fake ish company, right, that companies like this do exist."
- "And we're going to ask you to come up with an architecture to solve the challenges this particular company is facing."
- "You're going to come back to us a proposal saying here's how we think it should be built."
- "We think you should use this technology."
- "We think these services should exist here with the challenging places and we want in this particular cater for you to think carefully about how you would make use of AI."
- "Are there certain parts of this problem space that fit Gen. AI in really effective ways?"
- "Or maybe there are other areas where you considered the use of AI but decided eventually not to use it."
- "So we are going to be looking for you to explain some of your decision making."
- "We'll talk a little bit about AD Rs later on."
- "So can you use AI as part of the solution?"
- "If so, where?"
- "Here's the problem, right?"
- "So what are we building this for?"
- "Who is this architecture for?"
- "So the system we're going to be looking to build an architecture for is a company called Mobility Corp."
- "So Mobility Corp are a company that provides short term rental for last mile transport."
- "I did actually came up with that, you know, kind of tag line myself."
- "Feel like I should be in marketing."
- "So what does that actually means?"
- "It means scooters, right?"
- "It means electric scooters and it means E bikes largely speaking."
- "So you kind of get to public transport a certain way and then you want to jump on an E bike, jump on a scooter, get where you need to be."
- "However, we also have a growing fleet of electric cars and vans."
- "The idea of this being a car share based system, you don't have to own your own car or your own van."
- "You could walk to a place nearby your house and get into a car that you've booked, go do your errands, then come back."
- "This is also, this is partly about, you know, it being cheaper for our customers because they don't actually have to own a full car."
- "But also it's better for the planet as well because we can have one car used by loads of people rather than everybody having to build and maintain their own car."
- "So when's the customers?"
- "They sign up and then they can rent the available vehicles."
- "And we operate in multiple locations."
- "Mostly we're in city and urban locations and but we're looking to go to more suburban locations."
- "It's kind of general context of where we're at."
- "So mostly in cities and large towns, E bikes, scooters, cars and vans."
- "People sign up and they could book a vehicle, go off and drive it and then they have to return that vehicle."

### Usage context and Q&A prompts — starts 00:09:05.120

- "Now, by the way, as I'm going through all of this and I'm outlining the problem, I want all of you to be thinking up what questions have I got for Sam?"
- "This is a great opportunity."
- "This kick off session says it's a great opportunity for you to ask more questions about Mobility Corps context and the business problems that they're trying to deal with."
- "So if you do have questions about Mobility's Corp and their whole kind of where they're at and the problems and the business space, everything else, just put a question in that Q&A widget and I'll answer these questions as we go through."
- "So you have to come up with an architecture for Mobility Corp incorporating AR functionality where appropriate."

### Booking system details — starts 00:09:16.520

- "So let's talk through kind of overview of how this works."
- "So from a booking point of view, we have kind of a bit of a separation between cars and vans and our scooters and E bikes."
- "The cars and vans you can book up to seven days in advance."
- "And when you book them, you say how long you're going to book them for."
- "So you'd have to say, I want to book a van, I want to book it for four hours, and then you'll be told where that van is."
- "Bikes and scooters can be booked for up to 30 minutes in advance, but the booking is open-ended and you can keep them for up to 12 hours."
- "This is much more like the kind of line bike situation I'm sure some of you might have for the similar things you might have for Bolt or whatever else where you can go scan AQR code or you could book it in advance, go for a little poodle, drop them off at designated locations."

### Payment and fines — starts 00:10:58.720

- "From a payment point of view, you pay per minute for how long you're going to be using these things for."
- "We also apply fines if the vehicles are returned to the wrong location or if they are returned late."
- "So in the case of things like cars and vans, you know, you have a time, the vehicle has to be back at a certain time and after a certain period of time we start finding you."
- "And obviously if you looks like you can steal the car, we actually have the ability to kind of, you know, track the car and everything else."
- "So we can assume all of these vehicles have GPS trackers on them for the purpose of knowing where they are geographically."
- "And as I mentioned, if things get really bad, we can disable remotely cars and vans."

### Vehicle technology and access — starts 00:11:45.440

- "Kind of question, is insurance out of scope?"
- "You could assume that it is it as much as we kind of as part of the payment we take from customers, we basically kind of insure the vehicles, we've insured the vehicles on our side."
- "So basically assume that insurance is not something you have to worry about and Ms."
- "customers can cancel, that's absolutely fine."
- "So you don't."
- "It wouldn't, I would imagine, change the architecture too much, but customers can cancel basically anytime without penalty."
- "So the vehicles, all transport vehicles contain GPS trackers."
- "So we know that there they are at all times we can remotely lock and unlock those vehicles."
- "So assume that all of these vehicles have like some kind of mechanical device or electronic device that allows for remote locking and unlocking."
- "So in the case of the cars and vans, that that stuff has already been installed and that you can assume that you've got some kind of API that you can call to, you know, unlock or lock those."
- "Same goes for the scooters and the E bikes."
- "We will assume that when you actually go to unlock these vehicles that our customers have a mobile device that is NFC capable."
- "So in other words, some they have to put their mobile device next to the vehicle to unlock it and that goes for the scooters, the bikes, the vans and the cars."

### Returns and charging requirements — starts 00:12:15.440

- "On returning the vehicles they have to the cars and vans, bikes and scooters have to be returned to designated parking spots."
- "So that is important."
- "So and we will also want proof of return."
- "In the case of cars and vans, they also have to be plugged into the EV chargers at the destination bay."
- "So when you return your car, you don't just drop it off."
- "You have to remember to plug it in so that it can be charged for the next customer."
- "And we find people if they don't plug it in."
- "We also require customers to take a photo as proof of return."
- "And if they haven't parked it properly or they haven't plugged it into the charger correctly, then we find them for that as well."
- "Customers can also report issues with vehicles."
- "So, you know, wonky bicycle saddle or dirty car, things like that."

### Operations: batteries and redistribution — starts 00:13:25.520

- "The E bikes and scooters do use swappable batteries."
- "So we have members of our crew that go and replace flat batteries with fully charged batteries."
- "This is a roadside operation."
- "We also want to redistribute E bikes and scooters."
- "So what happens is you can get large clustering in certain locations."
- "So you can imagine that if there's a big tourist landmark, lots of people will use E bikes to get to that location, but then they won't use E bikes to leave."
- "So we also need our crew members to go and redistribute those E bikes and scooters to different locations."

### Business challenges — starts 00:14:33.600

- "So what are some of the challenges that Mobility Core faces?"
- "And this is where we come to some of the reasons why they want to change their architecture."
- "One of the biggest challenges that they face is that their vehicles aren't often in the places they need to be at the times that they need to be there."
- "So customers come to rely on the fact that there'll be a scooter or an E bike or a car available for them."
- "If that's not there, then the customers get frustrated and they stop using the service."
- "So we need to be clever about making sure that the vehicles are where people want them to be when they want them to be there."
- "So fundamentally, the business wants to be able to forecast demand."
- "So by looking at things like the time, the day of the week, weather patterns, events going on."
- "They want to be able to predict, well, we're going to need more E bikes here, we're going to need more cars there."
- "And then they can proactively start moving vehicles to the right locations at the right times."
- "They also want to be more intelligent about which crews to send off to do battery swaps first."
- "And which crews to send off to do redistribution first, so that they can prioritize the operations in a way that maximizes both customer satisfaction and revenue."
- "And another challenge they have as well is that their users tend to use the service in an ad hoc way."
- "You know, they just turn up and if there's something available, they use it and if there isn't they go and find some other mechanism."
- "What they would much prefer is to have much more predictable usage."
- "So they want people to get into the habit of using Mobility Corp as a service."
- "And that predictable usage allows them to do a better job of moving vehicles to the right location at the right time."

### [Q&A] Round 1 — starts 00:17:26.520

**Question:** "Are we constrained to one country or multiple countries?"

**Sam:** "That's a really good question. For the purpose of this exercise, let's assume that you're EU only. I think that just makes this problem easier to deal with. We don't have to worry about too many different currency implications and regulatory implications. So we're EU only."

**Question:** "Languages and currencies?"

**Sam:** "Yeah, so we could have multiple languages and we could have multiple currencies. And obviously we have GDPR and other aspects that come from a regulatory point of view of being in the EU. But I think that for the exercise EU is a nice scope to avoid us having to deal with some of the other complicated aspects that would come from a truly global system."

**Question:** "More questions about insurance and what happens when someone doesn't bring the car back or steals it?"

**Sam:** "The expectation is that when we take payment from a customer, part of that payment process goes towards insurance. And so when we're renting you a vehicle, you are kind of insured to use that vehicle during that period of time. Obviously, if somebody steals a car, that becomes a kind of police matter. But I wouldn't suggest that the processes around reporting thefts need to be part of your architectural solution. You could just assume that, you know, if somebody steals a car, we've already got some processes to deal with that."

**Question:** "When you mentioned returning to designated locations, are these particular geo-fenced areas or specific parking bays?"

**Sam:** "Both. For cars and vans, they have to be returned to specific parking bays. And part of that is because they have to be plugged in for charging. For the E bikes and scooters, it's more geo-fenced areas, but they are quite specific geo-fenced areas. So, you know, think of it as like a 20 by 20 meter area or whatever."

**Question:** "And is there any consideration of different speed limits in different areas?"

**Sam:** "Yeah, and some of those geo-fenced areas that the E bikes and scooters get returned to may also have speed limits associated with them. So some of those areas might be areas where we cap the maximum speed that the E bikes and scooters can go."

**Question:** "Is it OK to use third party services?"

**Sam:** "Absolutely. Third party services, third party mapping services, routing services. I mean, if you can make the business case for it and you are able to explain it in your architecture and you can maybe put some thought into, you know, how you might extract yourselves from those third party services if you need to, then yeah, absolutely go for it."

**Question:** "So Here maps, Google Maps, that kind of thing?"

**Sam:** "Yep. Google Maps, Here maps, whatever."

**Question:** "What happens at the end of 12 hours for bikes and scooters? Do they automatically lock?"

**Sam:** "Yes. So at the end of 12 hours, they would automatically lock. And then the customer would have to walk them back to one of the designated return areas and drop them off at one of those locations."

**Question:** "What happens if cars or vans run out of battery charge during a trip?"

**Sam:** "That's a good one. OK, so first of all, we won't actually let people book a car or a van unless it's got enough charge to fulfill their booking. So if somebody wants to book a car for four hours and we know that this car only has enough battery for two hours, we wouldn't let them book it. However, things can go wrong. Cars can break down, they can run out of charge. At that point, we do have a roadside recovery service that we can dispatch to help the customer. We also have got a lot of partner locations around the EU where customers can actually top up the charge on the cars and the vans if they need to and we can pay for that on their behalf."

**Question:** "For E bikes and scooters, what happens if they run out of charge?"

**Sam:** "Well, they should still be functional. You can still pedal an E bike even if it doesn't have any electrical power. With scooters, you can sort of walk with them and push them along if you need to. So they don't become completely unusable, but obviously the customer experience is not great."

**Question:** "What kind of scale are we talking about here?"

**Sam:** "Good question. Across, let's say, for a EU country, for an average EU country, let's assume that we've got about 200 cars, about 200 vans, about 5,000 E bikes and about 5,000 scooters."

**Question:** "Are subscriptions part of this?"

**Sam:** "Not really. Unless you think subscriptions are something that fundamentally changes your architectural approach, I wouldn't worry about them. The focus really is on the kind of, you know, pay as you go, pay per minute model."

**Question:** "Should we focus on scaling up the fleet or improving utilization?"

**Sam:** "Great question. The business would much prefer to improve utilization of the existing fleet before they start adding more vehicles. More vehicles costs more money, right. Storage costs money. Maintenance costs money. And also more vehicles is worse for the planet. So they would much prefer to be more intelligent about how they use the existing fleet."

**Question:** "Do the vehicles provide range telemetry, like how much charge is left?"

**Sam:** "Yes. So assume that all of the vehicles can tell you their remaining range. So if a car says, you know, I've got a 300 kilometer range, it can tell you how much of that 300 kilometers is left."

**Question:** "Are there any constraints around cross-border usage within the EU?"

**Sam:** "No, that's fine. So people can take a car from, say, Berlin and drive it to Amsterdam if they want to. But they have to bring it back to a designated return location in the city where they got it from originally."

**Question:** "Are there any incentives to influence user behavior?"

**Sam:** "Uh, not as part of the current business model. But if you think that introducing, let's say, incentives or maybe even changing the business model to allow some more flexibility, for example, you know, allowing people to do one way rentals, if you think that would help address some of the core business objectives, then feel free to propose that. But make sure that you explain the trade-offs that would be involved."

**Question:** "Who are the target customers?"

**Sam:** "Good question. These are convenience focused customers. It's a pretty broad socioeconomic spread. So it's not super high-end or super low-end. It's kind of, you know, the people who can afford to use Uber regularly, that kind of customer."

**Question:** "Can customers extend their bookings?"

**Sam:** "For cars and vans, yes, they can ask to extend their bookings, but obviously we can't guarantee that we'll be able to give them that extension because somebody else might have booked that vehicle for later on. For E bikes and scooters, as I said, they're kind of open-ended anyway. You can keep them for up to 12 hours, so there's not really an extension concept there."

**Question:** "What kind of vehicle interface are we looking at? HTTP, MQTT, something else?"

**Sam:** "Good question. You tell me. You're designing the architecture. But assume that the vehicles can communicate with your backend systems, whether that's over cellular or whether that's over WiFi when they're back at base. You need to make some decisions about things like how often do you want telemetry from the vehicles. Every 30 seconds, every 60 seconds, you know, what makes sense?"

**Question:** "You mentioned booking windows. Can you clarify them again?"

**Sam:** "Yeah, so for cars and vans, you can book them up to seven days in advance. For E bikes and scooters, you can book them up to 30 minutes in advance."

**Question:** "When does billing start for E bikes and scooters?"

**Sam:** "So for E bikes and scooters, billing starts as soon as you make the reservation. So even if you book it 30 minutes in advance, you start getting billed as soon as you make that reservation."

**Question:** "How do you handle payments? Do you take payment upfront?"

**Sam:** "We do pre-authorization on a customer's credit card or debit card. So we put a hold on their card for an estimated amount. And then at the end of their trip, we charge them for the actual usage."

**Question:** "Is payment time-based or distance-based?"

**Sam:** "Time-based. Payment is per minute, not per kilometer."

**Question:** "What happens if a vehicle breaks down during someone's trip?"

**Sam:** "Good question. We would want to make sure that we have some mechanisms in place for customers to report breakdowns and for us to then dispatch either roadside assistance or recovery services to help them out."

**Question:** "Is there historical data available for forecasting?"

**Sam:** "You can assume that there is some historical data available. But part of what you're designing is the data capture and the data processing that will be used for future forecasting."

**Question:** "What kind of vans are we talking about? Cargo vans or passenger vans?"

**Sam:** "Mostly cargo vans, but some of them can seat 2 or 3 passengers as well. Think of them as small commercial vehicles."

**Question:** "How does the battery swap process work?"

**Sam:** "It's a pretty quick operation. Our crew members can swap out a battery in a couple of minutes. They carry charged batteries with them and they swap out the flat ones."

**Question:** "Do customers use a mobile app?"

**Sam:** "Yes, assume that customers primarily interact with the service through a mobile app. They can also call us if they need to, but the mobile app is the primary interface."

**Question:** "Is trip routing and journey sharing in scope?"

**Sam:** "No, that's out of scope. Customers can use Google Maps or Waze or whatever they want for routing. We're not trying to compete with those services."

**Question:** "What happens if vehicles lose connectivity?"

**Sam:** "Good question. Obviously, we need to track vehicles for theft and recovery purposes. But I wouldn't make loss of connectivity a primary focus of your architecture. The vehicles are going to be in urban areas most of the time, so connectivity should generally be available."

**Question:** "How often should vehicles send telemetry?"

**Sam:** "Let's assume at least every 30 seconds when they're in use. But you can adjust that if you think it makes sense for your architecture. Just make sure you explain your reasoning."

**Question:** "Can you book vehicles for specific flight times?"

**Sam:** "No, you book by time duration, not by specific events like flights. If you want a car for 4 hours, you book it for 4 hours, regardless of what you plan to do with it during that time."

### Deliverables and judging criteria — starts 00:44:40.760

- "OK, so let's talk about deliverables."
- "So what are we looking for from you?"
- "We want you to produce a short narrative. Think of it as kind of a README file that explains your solution."
- "We want you to produce some clear diagrams."
- "We want you to produce some ADRs that document your key architectural decisions."
- "And then if you reach the semifinals, we'll also ask you to produce a short, five minute video."

### Diagram requirements — starts 00:45:17.360

- "In terms of diagrams, the key thing that I want to focus on here is keep them simple."
- "We see a lot of diagrams that are just too complicated, too much going on."
- "Think boxes, arrows, and words."
- "That's it."
- "If you are using colors or shapes to mean specific things, make sure you include a key."
- "And make sure that you export your diagrams into your GitHub repository."
- "So don't just link to a Lucid chart or a Miro board or whatever."
- "Export them as PDFs or images and include them in your repository."

### ADR requirements — starts 00:46:03.360

- "ADRs, Architectural Decision Records."
- "These are really important for us to understand your thinking."
- "We want to know what options you considered, what decision you made, and why you made that decision."
- "What were the trade-offs?"
- "This is particularly important when it comes to your AI decisions."
- "Where did you decide to use AI and where did you decide not to use AI, and why?"

### Communication and judging focus — starts 00:46:31.560

- "Remember, this is all about communication."
- "Your job is to communicate your architecture in such a way that we judges reach the same understanding that you had when you were designing the architecture."
- "It's not enough to just show us boxes and arrows."
- "You need to tell us the story."

### AI-specific judging criteria — starts 00:47:12.040

- "In terms of the judging criteria, we're looking for a few specific things."
- "First, we want to see interesting uses of GenAI."
- "Not just, 'Oh, we'll throw ChatGPT in here.'"
- "We want to see thoughtful, appropriate uses of AI that actually solve real business problems."
- "Second, we want to see that your solution is appropriate for the business context."
- "Third, we want to see clear decision making and clear communication."
- "And fourth, we want to see that you've thought about some of the challenges and uncertainties around AI."

### Handling AI uncertainty — starts 00:54:41.040

- "One of the big challenges with GenAI right now is that the space is changing very rapidly."
- "Models are improving, new models are coming out, pricing is changing, vendors are pivoting or shutting down."
- "We want to see that you've thought about how you would handle these uncertainties."
- "How would you switch from one AI model to another if the quality degraded?"
- "How would you handle pricing volatility?"
- "What would you do if your AI vendor shut down or pivoted away from the service you're using?"

### AI validation and verification — starts 00:49:59.120

- "Another thing we want to see is that you've thought about validation and verification of AI results."
- "GenAI is probabilistic. It doesn't always give you the right answer."
- "How do you verify that the AI is giving you reasonable results?"
- "How do you handle cases where the AI gives you bad advice or wrong predictions?"

### [Q&A] Round 2 — Tools, vendors, and sharing — starts 00:56:27.680

**Question:** "Are tools like Lucidchart and Miro OK to use for diagrams?"

**Sam:** "Absolutely. Use whatever diagramming tool you're comfortable with. Just make sure that you export the final diagrams into your GitHub repository rather than just linking to them."

**Question:** "What about cloud platforms and managed services?"

**Sam:** "You can absolutely use cloud platforms like AWS, Azure, GCP. You can use managed services like Salesforce Data Cloud or Agentforce or whatever. Just make sure that you discuss the trade-offs in your ADRs. What are the benefits of using these services? What are the risks? How locked in would you be? What would you do if pricing changed dramatically?"

**Question:** "Are submissions going to be public?"

**Sam:** "Yes, the submissions will be public. So don't include any sensitive information in your submissions. And actually, this is a good thing because it means you can look at submissions from previous kata exercises to get a sense of what we're looking for."

### [Q&A] Using GenAI to do the work — starts 01:06:30.160

**Question:** "Are we allowed to use GenAI to help us create our solutions?"

**Sam:** "We're not going to police whether you use GenAI or not. But remember, the point of this exercise is for you to learn. So if you're using GenAI to generate your diagrams or your ADRs, you're kind of missing the point. But if you want to use GenAI to help you with formatting or to help you think through some ideas, that's fine. Just make sure that the thinking and the decision making is yours."

### [Q&A] More on criteria and cloud choices — starts 01:08:54.120

**Question:** "If we go all-in on one cloud platform, how should we handle that in our ADRs?"

**Sam:** "Great question. If you decide to go all-in on, say, AWS, then you need to acknowledge in your ADRs that you're taking on vendor lock-in risk. What would you do if AWS pricing changed dramatically? What would you do if AWS shut down a service you're relying on? How portable is your solution? These are all things that you should discuss. And there's some great thinking on this from people like Gregor Hohpe about the trade-offs of cloud lock-in."

### [Q&A] Public sharing and feedback — starts 01:10:05.200

**Question:** "Will we get detailed feedback on our submissions?"

**Sam:** "Unfortunately, we won't be able to provide detailed feedback to every team. There are just too many submissions and not enough time. But the submissions from previous exercises are all available publicly, so you can look and see what kinds of things have worked well in the past."

### [Q&A] Final clarifications — starts 01:11:41.120

**Question:** "Should we work as individuals or as teams?"

**Sam:** "Teams. Architecture is a collaborative discipline. Working as a team will give you better results and it's more realistic to how architecture works in the real world."

**Question:** "Any specific requirements around authentication and authorization?"

**Sam:** "Nothing specific. Just assume that you need to authenticate customers and authorize their access to vehicles. You can use whatever approach makes sense for your architecture."

**Question:** "You mentioned that customers can cancel without penalty. Is there any minimum duration for rentals?"

**Sam:** "No minimum duration. Billing is per minute, so if someone books a car and then cancels it after one minute, they pay for one minute."

## Judges segment — starts 01:01:07.280

### Judge (Andrew Harmel Law) — starts 01:01:30.120

- "Hi everyone. I'm Andrew Harmel Law."
- "I'm looking forward to seeing your submissions."
- "The key thing I'm looking for is your train of thought. How did you get to your architecture?"
- "There is no single right answer to this problem. There are multiple good answers and probably some bad answers."
- "What I want to understand is why you think your answer is appropriate for this business context."
- "Clear reasoning and trade-offs will help us judge your solution fairly."

### Judge (Gayathri "Go 3") — starts 01:03:41.560

- "Hi everyone. I'm Gayathri, but you can call me Go 3."
- "I'm really looking forward to seeing some meaningful GenAI solutions."
- "I want to see that you've called out your assumptions explicitly."
- "I want to see that you've identified risks, particularly around the uncertainties in the AI space."
- "How are you protecting business value when the AI landscape is changing so rapidly?"
- "I also want to see that you've thought about the practical aspects. Pricing, implementation complexity, operational overhead."
- "Connect your solutions to enterprise realities."
- "By the way, I've got a book coming out called 'Full Stack Testing' from O'Reilly, if you're interested in quality engineering practices."

### Host mentions third judge — starts 01:05:56.760

**Sam:** "Our third judge is Kent Beck. He'll be joining us for the semifinals and the finals."

## Submission process and schedule — starts 01:12:47.520

### Repository and documentation requirements

- "You need to create a public GitHub repository for your submission."
- "Include all of your documentation and all of your visual assets in that repository."
- "Don't just link to external tools. Export everything and include it in the repo."
- "Make it easy for us judges to find everything. A clear README file as an entry point would be great."

### Key dates and deadlines

- "Team registration: Fill out the Google Form by October 15th, 12:00 PM Eastern Time."
- "Repository snapshot: We'll take a snapshot of your repository at 11:59 PM Eastern Time on October 23rd."
- "Semifinalists announced: November 5th."
- "Five-minute videos due: November 9th (semifinals only)."
- "Final event and winner announcement: November 13th."

## Host (Sam Newman) — wrap-up — starts 01:16:50.720

- "Remember, the purpose of this exercise is practice."
- "Practice thinking about architecture holistically."
- "Practice thinking about trade-offs."
- "Practice communicating your ideas clearly."
- "And practice thinking about appropriate uses of GenAI - or when not to use it."
- "There are lots of resources available to help you."
- "Get your teams registered by the first deadline."
- "Get your solutions submitted by October 23rd."
- "Thank you to our judges Andrew and Go 3."
- "Thank you to everyone who organized this event."
- "And thank you to all of you for the great questions."
- "I hope you're well and I'm looking forward to seeing your submissions."
- "Over to Joan."

## Facilitator (Joan) — closing — starts 01:19:34.920

- "That concludes event one. Thank you for attending."
- "The session was recorded. You'll receive the recording within 24 to 48 hours."
- "We look forward to having you join us for Event 2. Take care. Bye."

## Complete Technical Requirements Summary

### Vehicle Fleet Composition (per EU country)
- 200 cars (electric)
- 200 vans (electric, mostly cargo, some 2-3 passenger)
- 5,000 e-bikes
- 5,000 e-scooters

### Booking Rules
- **Cars/Vans:** Up to 7 days advance booking, fixed duration required
- **Bikes/Scooters:** Up to 30 minutes advance booking, open-ended up to 12 hours maximum

### Vehicle Technology
- GPS tracking on all vehicles (at least every 30 seconds when in use)
- Remote lock/unlock capability via API
- NFC unlock mechanism (customer mobile device)
- Range telemetry available for all vehicles
- Cars/vans: automatic disable capability if stolen

### Return Requirements
- **Cars/Vans:** Specific parking bays, must be plugged into EV chargers
- **Bikes/Scooters:** Designated geo-fenced areas (approximately 20x20 meters)
- Photo proof of return required
- Fines for wrong location or late return
- Speed limits may apply in certain geo-zones

### Payment Model
- Pre-authorization on customer payment method
- Per-minute billing (not distance-based)
- No minimum duration
- Billing starts at reservation for bikes/scooters
- Cancellation without penalty allowed anytime

### Operations
- **Battery Management:** Swappable batteries for bikes/scooters, roadside crew replacement
- **Redistribution:** Crew-based vehicle repositioning to prevent clustering
- **Maintenance:** Customer reporting of vehicle issues
- **Recovery:** Roadside assistance for breakdowns/charge issues
- **Partner Charging:** Third-party locations for customer vehicle charging

### Business Objectives
- Forecast demand (time, day, weather, events)
- Optimize crew prioritization (battery swaps vs redistribution)
- Improve vehicle utilization before fleet expansion
- Encourage habitual vs ad-hoc usage patterns
- Ensure vehicle availability at right place/time

### Regulatory Context
- EU-only scope
- GDPR compliance required
- Multiple languages and currencies
- Cross-border usage allowed (return to origin city)
- Insurance handled by company (out of architectural scope)