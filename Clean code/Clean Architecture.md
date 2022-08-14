# Clean architecture
## What is Software Architecture
- Refers to the fundamental stuctures of a software system and the discipline of creating sucj structure and systems
- Each stucture comprises software elements, relations among them, and properties of both elements and relations
- Software relations:
	- Code to code (need to optimize this level)
	- Deployment 
	- Service to service (hardest optimization)
- Type of Software Architecture:
	- Enterprise Architecture: level where people are diveded like marketing team, dev team, ... 
	- System Architecture: high level structure like ip, devices, ...
	- Application Architecture: the internal structure of an application (classes, components and design patterns)
## What is good architechture
- Your architectures should tell readers about the system, not about the frameworks you used in your system.
- A good architecture allows major decisions to be deferred. A good architect maximizes the number of decisions not made
Good architecture | Bad architecture 
------------ | ------------
Easy to understand | Hard to understand
Easy to test | Hard to test
Easy to maintain | Hard to maintain
Flexible | Rigid
## Traditional Database Centric Architecture
- Create unecessary coupling
- Business logic tends to be placed as close to the data as possible. In case of relational databases, it is usually DB functions and stored procedures
- Application code is often considered to be secondary. The development is usually started with modeling the database structure. Application code conforms to the model built in DB
## Loose coupling and High chesion

Cohesion | Coupling
------------ | ------------
Cohesion is the concept of **intra module** | Coupling is the concept of **inter module**
Cohesion represents the relationship within module | Coupling repreents the relationship between modules
Increase in cohesion is good for software | Increasing in coupling is aviuded for software
Cohesion represents the functional strength of modules | Coupling represents the independence among modules
Highly cohesive gives the best software | Whereas loosely coupling gives the best software
In cohesion, module focuses on the single thing | In coupling, modules are connected to the modules

- **Intra module:** 2 modules in the same code application
- **Inter module:** 2 modules in diffrent service like microservice
- **High cohesion** means keeping parts of a code base that are related to each other in a single place
- **Loose coupling**, at the same time, is about separating unrelated parts of the code base as much as possible
## Domain Centric Architecture
![[Pasted image 20220814143046.png]]
## Clean Architecture
![[Pasted image 20220814145436.png]]
![[Pasted image 20220814154441.png]]
![[Pasted image 20220814160153.png]]
![[Pasted image 20220814160214.png]]
- The entities and use cases layer should be vertical slice
- The controllers and external interfaces should be horizontal slice
- The entities can refered as domain
### Benefits of Clean Architecture
- Framework independent
- Database independent
- UI independent
- Independent of any external agency
- Highly testable
- These architectures fit very well with the Domain Driven Design approach
## Entities
- Represent you domain objects has ID for identity
- Plain objects: no frameworks, no annotations
- Apply only logic that is applicable in general to the whole entity. There is a disadvantage of using entities 
## Use cases
- Represent your business actions 
- Pure business logic, plain code
- The usecase does not konw who triggered it and how the results are going to be presented
- This layer can be open to use some libary, but it should prefer as only pure business
## Interface adapter
- Convert data from the format most convenient for the use cases and entities, to the format most convenient for some external agency such as the database or the web
- Convert data from the format most convenient for entities and use cases, into the form most convenient for whatever persistence framework is being used, example The database
- Convert data from some external form, such as an external service, to the inernal form used by the use cases and entities.
## Framworks/Libraries and Drivers
- The outer most layer is generally composed of frameworks and tools such as the Database, the Web Framework, etc
- Generally, you don't write much code in this layer other than glude code that communicates to the next circle inwards
  ![[Pasted image 20220814155713.png]]
## Dependency rules
- Source co dependencies can only point inwards
- Nothing in an inner circle can know anything at all about something in an outer circle
## Tests
- Unit test
- Integration test
- E2E test (end to end test)
## Clean architecture and Micro-services
- Micro-services are a deployment option
- Clean architecture does not care how the components are deployed
- A micro-service architecture can nicely conform to the Clean Architecture
## Clean architecture and Serverless
- One of main obstacles with Serverless development:
	- Local testing
	- Vendor Lock-in
- Serverless is a deployment option too
- Code written in the decoupled way (following Clean architecture rules) is easy to test
- Clean architecture can help to mitigate the risks/fears of vendor lock-in
![[Pasted image 20220814222920.png]]
## Problem with Clean architecture
- Monolithic in approach
- Mock heavy
- Abstraction upon abstractions
- Heavy coupling inside layers
- Resolve with clean architecture
	- Vertical slice architecture: minimize coupling between slices and maximize coupling within a slice
		- Things that change together, belong together
		- Maximize cohesion along axes of change and minimize coupling between them
		- ![[Pasted image 20220814223417.png]]
## Hexagonal architecture vs Onion architecture
- ![[Pasted image 20220814223842.png]]
- Onion architecture: application core can access all the layers
- Hexagonal architecture: 2 layers
- 2 architecture combines with SOLID we get the clean architecture