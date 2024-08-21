# 4-step process for effective system design interview
## 1. Understand the problem and establish design scope
- Don't give out an answer quickly, instead a thorough understanding of the requirements.
- Ask the right questions and don't be afraid to ask questions. Examples:
	- What specific features are we going to build?
	- How many users does the product have?
	- How fast does the company anticipate to scale up? What are the anticipated scales in 3 months, 6 months, and a year?
	- What is the company’s technology stack? What existing services you might leverage to simplify the design?
## 2. Propose high-level design and get buy-in
- Come up with an initial blueprint for the design. Ask for feedback. Treat your interviewer as a teammate and work together. Many good interviewers love to talk and get involved.
- Draw box diagrams with key components on the whiteboard or paper. This might include clients (mobile/web), APIs, web servers, data stores, cache, CDN, message queue, etc.
- Do back-of-the-envelope calculations to evaluate if your blueprint fits the scale constraints. Think out loud. Communicate with your interviewer if back-of-the-envelope is necessary before diving into it.
## 3. Design deep dive
- Identify and prioritize components in the architecture.
## 4. Wrap up
- Follow-up questions can be asked:
	- The interviewer might want you to identify the system bottlenecks and discuss potential improvements. Never say your design is perfect and nothing can be improved. There is always something to improve upon. This is a great opportunity to show your critical thinking and leave a good final impression.
	- It could be useful to give the interviewer a recap of your design. This is particularly important if you suggested a few solutions. Refreshing your interviewer’s memory can be helpful after a long session.
	- Error cases (server failure, network loss, etc.) are interesting to talk about.
	- Operation issues are worth mentioning. How do you monitor metrics and error logs? How to roll out the system?
	- How to handle the next scale curve is also an interesting topic. For example, if your current design supports 1 million users, what changes do you need to make to support 10 million users?
	- Propose other refinements you need if you had more time.
- Dos:
	- Always ask for clarification. Do not assume your assumption is correct.
	- Understand the requirements of the problem.
	- There is neither the right answer nor the best answer. A solution designed to solve the problems of a young startup is different from that of an established company with millions of users. Make sure you understand the requirements.
	- Let the interviewer know what you are thinking. Communicate with your interview.
	- Suggest multiple approaches if possible.
	- Once you agree with your interviewer on the blueprint, go into details on each component. Design the most critical components first.
	- Bounce ideas off the interviewer. A good interviewer works with you as a teammate.
	- Never give up.
- Don'ts:
	- Don't be unprepared for typical interview questions.
	- Don’t jump into a solution without clarifying the requirements and assumptions.
	- Don’t go into too much detail on a single component in the beginning. Give the highlevel design first then drills down.
	- If you get stuck, don't hesitate to ask for hints.
	- Again, communicate. Don't think in silence.
	- Don’t think your interview is done once you give the design. You are not done until your interviewer says you are done. Ask for feedback early and often.
# Time allocation
- Step 1 Understand the problem and establish design scope: 3 - 10 minutes
- Step 2 Propose high-level design and get buy-in: 10 - 15 minutes
- Step 3 Design deep dive: 10 - 25 minutes
- Step 4 Wrap: 3 - 5 minutes
