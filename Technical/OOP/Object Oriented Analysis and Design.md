- <mark style="background: #FFB86CA6;">OOA&D is about having lots of options. There is never one right way to solve a problem, so the more options you have, the better chance you'll find a good solution to every problem.</mark>
# <mark style="background: #ADCCFFA6;">Steps for making software in general</mark>
### <mark style="background: #D2B3FFA6;">1. Make sure your software does what the customer wnats it to do.</mark>
- Don't create problems to solve problems.
- Always begin a project by figuring out what the customer wants.
- You can solve a big problem by breaking it into lots of function pieces, and then workingon each of those peices individually.
- Customer don't pay you for greate code , they pay you for greate software.
### <mark style="background: #D2B3FFA6;"> 2. Apply basic OO principles to add flexibility.</mark>
- Anytime you see duplicate code, look for a place to encapsulate.
- Once you've got the basic functionality of an app in place work onrefining the design so it's flexible.
- Your system must work in the real world, not just when everything goes as you expect it to.
### <mark style="background: #D2B3FFA6;">3. Strive for a maintainable and reusable design.</mark>
- With a functiopnal and flexible design, you can employ design patterns to improve your design further, and make your app easier to reuse.
- Find the parts of your application that change often, and try and separate them from the parts of you application that don't change.
- Most good designs come from analysis of bad designs.
- Never be afraid to make mistakes and then change things around.
# <mark style="background: #ADCCFFA6;">THE OBJECT-ORIENTED ANALYSIS DESIGN PROJECT LIFECYCLE</mark> 
### <mark style="background: #FF5582A6;">1. Feature list</mark>
- Talk to the customer
- Key feature list
### <mark style="background: #FF5582A6;">2. Use case diagrams</mark>
- External intiator
### <mark style="background: #FF5582A6;">3. Break up the problem</mark>
- Architecture
- Analysis
- Commonality
- Delegation
- Variability
### <mark style="background: #FF5582A6;">4. Requirements</mark>
- External initiator
- Scenario
- Alternate path
- Iteration
- Requirement list
### <mark style="background: #FF5582A6;">5. Domain analysis</mark>
- Talk to the customer
- Textual analysis
- Architecture
- Iteration
### <mark style="background: #FF5582A6;">6. Preliminary design</mark>
- Encapsulation
- Design pattern
- Iteration
- Cohension
- Design principles
- Class diagram
### <mark style="background: #FF5582A6;">7. Implementation</mark>
- Encapsulation
- OO Principles
- Design pattern
- Test driven development
- Iteration
### <mark style="background: #FF5582A6;">8. Delivery</mark>
- Design principles
- Feature driven development
- Test scenario
# <mark style="background: #ADCCFFA6;">Definitions</mark>
### <mark style="background: #D2B3FFA6;">Delegation</mark>
- The act of one object forwarding an operation to another object, to be performed on behalf the first object.
### <mark style="background: #D2B3FFA6;">Encapsulation </mark>
- Allows you to hide the inner working of your application's parts, but yet make it clear what each part does.
### <mark style="background: #D2B3FFA6;">Use case</mark>
- A use case is a technique for capturing the potential requirements of a new system or software change. Each use case provides one or more scenarios that convey how the system should interact with the end user or another system to achive a specific goal.
- A use case should container 3 parts:
	- <mark style="background: #FFB86CA6;">Clear value:</mark> every use case must have a clear value to the system. If the use case doesn't help the customer achive their goal, then the use case isn't of much use.
	- <mark style="background: #FFB86CA6;">Start and stop:</mark> every use case must have a definite starting and stopping point. Something must begin the process and trhen there must be a condition that indicates that the process is complete.
	- <mark style="background: #FFB86CA6;">Exxternal initiator: </mark>Every use case is started off by an external initiator, outside of the system. Sometimes that initiator is a person, but it could be anything outside of the system.
- Requirement always change. If you've good use cases though, you can usually change your software quickly to adjust to those new requirements.
-  A good use case clearly and accurately explains what a system does, in language that's easily understood.
### <mark style="background: #D2B3FFA6;">Requirement</mark>
- A singular need detailing what a particular product or service should be or do. It is most commonly used in a format sense in system engineering or software engineering.
### <mark style="background: #D2B3FFA6;">Scenario</mark>
- A complete path through a use case, from the first step to the last, is called a screnario.
- Most use cases have several different scenarios, but they always share the same user goal.
### <mark style="background: #D2B3FFA6;">Textual analysis</mark>
- Looking at the nouns (and verbs) in your use case to figure out classes and methods is called textual analysis.
- With a good use case complete, textual analysis is a quick and easy way to figure out the classes in your system.
- Pay attension to the nouns in your use cas, even when they aren't classes in your system.
- Think about how the classes you do have can support the behavior you use case describles.
### <mark style="background: #D2B3FFA6;">Abstract class</mark>
- Abstract classes are placeholder s for actual implementation classes.
- The abstract class defines behavior, and the subclasses implement that behavior.
- Whenever you find common behavior in 2 or more places, look to abstract that behavior into a class, and then reuse that behavior in the common classes.
### <mark style="background: #D2B3FFA6;">Interface</mark>
- Suppose you've got an application that has an interface, and then lots of subclasses that inherit common behavior from that interface.
- By coding to an interface, your code will work with all of the interface's subclasses-even ons that haven't been created yet.
### <mark style="background: #D2B3FFA6;">Cohesion</mark>
- Cohesion measures the degree of connetivity among the elements of a single module, class, or object. The higher the cohesion of your software is, the more well-defined and related the reposibilities of each individual class in your application. Each class has a very specific set of closely related actions it performs.
- A cohesive class does one thing really well and does not try to do or be something else.
### <mark style="background: #D2B3FFA6;">Domain analysis</mark>
- The process of identifying, collecting organizing and representing the relevant information of a domain, based upon the study of exsting systems and their development histories, knowledge captured from domain experts, underlying theory, and emerging technology within a domain.
### <mark style="background: #D2B3FFA6;">Architecturally significant</mark>
- The things in your application that are really important are architecturally significant, and you should focus on them first.
### <mark style="background: #D2B3FFA6;">Risk</mark>
- The reason that features are architecturally significant is that they all introduce risk to your project.
- It doen't matter which one you start with-as long as you are working towards reducing the risks in succeeding.
- Focus on 1 feature at a time to reduce risk in your project.
- Don't get distracted with features that won't help reduce risk.
# <mark style="background: #ADCCFFA6;">OOA&D toolbox</mark>
### <mark style="background: #D2B3FFA6;">Requirements</mark>
- The best way to get good requirements is to understand what a system is supposed to do.
- Good requirements ensure your system works like your customers expect.
- Make sure your requirements cover all the steps in the use cases for your system.
- Use your use cases to find out about things your customer forgot to tell you.
- Your use cases will reveal any imcomplete or missing requirements that you might have to add to your system.
- Your requirements will always change (and grow) over time. And when it does your system has to envole to handle new requirements.
### <mark style="background: #D2B3FFA6;">OO Principles</mark>
- Encapsulate what varies.
- Code to an interface rather than an implementation makes your software easier to extend.
- Each class in your application should have only one reason to change.
- Classes are about behavior and functionality.
- <mark style="background: #FFB86CA6;">Principles:</mark>
	- <mark style="background: #FFB8EBA6;">The open-closed principle (OCP)</mark>
	- <mark style="background: #FFB8EBA6;">The don't repeat yourseft principle (DRY):</mark> is about having each piece of infomation and behavior in your system in a single sensible place
	- <mark style="background: #FFB8EBA6;">The single responsibility principle (SRP)</mark>: is about making sure that a class does only 1 thing and does it well.
	- <mark style="background: #FFB8EBA6;">The Liskov substitution principle (LSP)</mark>
	- <mark style="background: #FFB8EBA6;">Delegate:</mark> if you need to use functionality in another class, but you don't want to change that functionality, consider using delegation instead of ingeritance.
	- <mark style="background: #FFB8EBA6;">Composition:</mark> the object composed of other behaviors owns those behaviors. When the object is destroyed, so are all of its behaviors. The behaviors in a composition do not exist outside of the composition itseft.
	- Aggregation: wehn you want the benefits of composition, but you're using behavior from an object that does exist outside of <mark style="background: #FFB8EBA6;">your</mark> object.
### <mark style="background: #D2B3FFA6;">Analysis and design</mark>
- Analysis helps you ensureyour system works in a real-world context.
- Well-design software is easy to change and extend.
- Use basic OO principles like encapsulation and inheritance to make your software more flexible.
- If a design isn't flexible, then change it! Nver settle on bad design, even if it's your bad design that has to change.
- Make sure each of your classes is cohesive: each of your classes should focus on doing ONE THING really well.
- Always stive for higher cohesion as you move through your software's design life cycle.
- Sometimes the best way to write greate code is to hold off on writing code as long as you can.
### <mark style="background: #D2B3FFA6;">Solving big problems</mark>
- Listen to the customer, and figure out what they want you to build.
- Put together a feature list, in language the customer understands.
- Make sure your features are what the customer actually wants.
- Create blueprints of the system using use case diagrams (and use cases).
- Break the big system up into lots of smaller sections.
- Apply design patterns to the smaller sections of the system.
- Use basic OOA&D principles to design an code each smaller section.
### <mark style="background: #D2B3FFA6;">Development approaches</mark>
- Use case driven development takes a single use case in your system, and focuses on completing the code to implement that entire use case, including all of its scenarios, before moving on to anything else in the application.
- Feature driven development focuses on a single feature and codes all the behavior of that feature, before moving on to anything else in the application.
- Test driven development writes test scenarios for a price of functionality before writing the code for that functionality. Then you write software to pass all the tests.
- Good software development usually incorporates all of these development models at different states of the development cycle.
### <mark style="background: #D2B3FFA6;">Programming practices</mark>
- Programming by contract sets up an agrrement about how your software behaves that you and users of your software agree to abide by.
- Defensive programming doesn't trust other software, and does extensive error and data checking to ensure the other software doesn't give you bad or unsafe information.
# <mark style="background: #ADCCFFA6;">The top 10 topics</mark>
### <mark style="background: #D2B3FFA6;">The problem with Is-A and Has-A</mark>
- If you think the accosiate bettwen the rectangle and the square, you would think it is an Is-A relationshi, but it is not. The rectangle have the method `setHeight()` or `setWidth()` and the square will have those methods but does the square have those methods? No, it doesn't have thos methods.
- Use inheritance/Is-A when 1 object behaves like another, rather than just when the Is-A relationship applies. 
### <mark style="background: #D2B3FFA6;">Anti patterns</mark>
- Anti patterns are about recognizing and avoiding bad solutions to common problems.
### <mark style="background: #D2B3FFA6;">CRC cards</mark>
- It helps implement the SRP.