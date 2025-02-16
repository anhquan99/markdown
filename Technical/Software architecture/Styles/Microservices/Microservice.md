"If you can't build a well-structured monolith, what makes you think microservice is the answer"
# Definition
- Small
- Does one thing
- Autonomous
# Principles
- Rely only the public API
- Use the right tool for the job
- Secure your services
- Be a good citizen within the ecosystem
- Automate everything
# Challenges of MSA
- Understanding of business domain
- Distributed system - Performance and Reliability
- Distributed transactions across services
- Decentralize data management
- Complexity of testing deploying operating a distributed system
- Many tolling options
- Lots of new technologies such as K8s, service mesh, ...
# Data
## Database guarantee
### ACID
- **Atomicity**: all or nothing rule
- **Consistency**: database must be consistent before and after the transaction
- **Isolation**: multiple transactions occur independently without interference.
- **Durability**: the changes of a successful transaction occurs even if the system failure occurs.
- Mostly used in monolithic. 
### CAP
- Used for distributed system.
- "**Two out of three**" concept.
- **Consistency**: all nodes see the same data at the same time.
- **Availability**: a guarantee that every request receives a response about whether it was successful or failed.
- **Partition tolerance**: the system continues to operate despite arbitrary loss or failure of part of system.
### BASE
- Basically Available: guarantee the availability of the data as regards CAP theorem.
- Soft state: the database may be in an inconsistent state temporarily. This is because data replication across the system can take time.
- Eventual consistency: the system will eventually reach a consistent state after all updates have been propagated.
## Management
### Database per service
- Benefits:
	- Helps ensure services are loosely coupled.
	- Each service can use a suited type of database to its needs.
- Drawbacks:
	- Join query multiple database is hard.
	- Implement distributed transaction is hard.
	- Complexity of managing multiple SQL and NoSQL database.
### API Composition
- Solution:
	- Use an API composer, or aggregator, to implement a query by invoking individual microservices that own the data, then combines the results by performing an in-memory join.
- Benefits:
	- Simple way to query data.
- Drawbacks:
	- Inefficient in-memory join of large datasets.
	- Performance.
### [[cqrs]]
- Solution:
	- Define a view database, which is a read-only replica that is designed to support that query.
	- The application keeps the replica up to date by subscribing to Domain event published by the service that own the data.
- Types:
	- No CQRS
	- Separated class structure
	- Separated model
	- Separated storage
- Benefits:
	- Supports multiple denormalized views that are scalable and performant.
	- Improve separation of concerns with simpler command and query models.
- Drawbacks:
	- Increase complexity.
	- Potential code duplication.
	- Replication log/eventually consistent views.
- Eventually transaction:
	- Outbox
- For important transaction:
	- Saga
	- Compensating transaction
- CDC microservice
### Saga
- Problem:
	- How to implement transaction that span services?
- Solution:
	- Implement each business transaction that spans multiple services as a saga. 
	- A saga is a sequence of local transactions. 
	- Each local transaction updates the database and publishes a message or event to trigger the next local transaction in the saga. If a local transaction fails because it violates a business rule then the saga executes a series of compensating transactions that undo the changes that were made by the preceding local transactions.
- Types:
	- Choreography: each local transaction publishes domain event that trigger local transaction in other services.
	- Orchestration: an orchestrator (object) tell the participants what local transactions to execute.
- Benefits:
	- Enables an application to maintain data consistency across multiple services without using distributed transactions.
- Drawbacks:
	- Complex.
	- Must design compensating transactions that explicitly undo changes made earlier in a saga.
# Communication styles
### REST
### gRPC
### Message broker
- Asynchronous
- Publish/Subscribe
- Increase complexity
# External APIs
### Direct 
### Gateway
### Back-end for Front-end (BFF)
- Each type of front end will communicate to specific back-end.
### GraphQL
- Solve problem over-fetching and under-fetching.
# Security
### Cleartext header
- The security token is removed when passed the API gateway, after that a service can call any services without needing any authenticate.
### Token transmission
- A service call another services must create a token to post the token to the call. (I maybe wrong here)
### OAuth scopes
### Token exchange
# Testing
### Contract test
### Unit test
### Integration test