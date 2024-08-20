# Problems
- A project faces serious problems when its language is fractured. Domain experts use their jargon while technical team members have their own language tuned for discussing the domain in terms of design.
- The terminology of day-to-day discussions is disconnected from the terminology embedded in the code (ultimately the most important product of a software project). And even the same person uses different language in speech and in writing, so that the most incisive expressions of the domain often emerge in a transient form that is never captured in the code or even in writing.
- Translation blunts communication and makes knowledge crunching anemic.
- Yet none of these dialects can be a common language because none serves all needs.
# Ubiquitous language
- It includes the names of classes and prominent operations and supplemented with terms from high-level organizing principles imposed on the model.
- The **LANGUAGE** includes terms to discuss rules that have been made explicit in the model.
- This language is enriched with the names of patterns the team commonly applies to the domain model.
- Domain experts will speak outside the scope of the ubiquitous language, to explain and give broader context.
- The model-based language should be used among developers to describe not only artifacts in the system, but tasks and functionality. This same model should supply the language for the developers and domain experts to communicate with each other, and for the domain experts to communicate among themselves about requirements, development planning, and features. The more pervasively the language is used, the more smoothly understanding will flow.
# Solution
- Use the model as the backbone of a language. Commit the team to exercising that language relentlessly in all communication within the team and in the code. Use the same language in diagrams, writing, and especially speech.
- Iron out difficulties by experimenting with alternative expressions, which reflect alternative models. Then refactor the code, renaming classes, methods, and modules to conform to the new model. Resolve confusion over terms in conversation, in just the way we come to agree on the meaning of ordinary words.
- Recognize that a change in the **UBIQUITOUS LANGUAGE** is a change to the model.
- Domain experts should object to terms or structures that are awkward or inadequate to convey domain understanding; developers should watch for ambiguity or inconsistency that will trip up design.
# Modeling out loud
Play with the model as you talk about the system. Describe scenarios out loud using the elements and interactions of the model, combining concepts in ways allowed by the model. Find easier ways to say what you need to say, and then take those new ideas back down to the diagrams and code.