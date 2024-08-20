# The dual write problem
- The single indicator that you may have a dual write problem is the need to write to more than 1 system of a record predictably.
# The modular monolith
- It is not a microservices pattern but an exception to the microservices rule that can be combined cautiously with microservices.
- It is a system designed in a modular way with exactly one deployment unit, but differ from an accidentally create monolith  that grows over time.
## Approach
1. Convert both microservices (Service A and Service B) into library modules that can be deployed into a shared runtime.
2. Make both microservices share the same database instance, because they can participate in the same transactions and make the transaction executed locally.
![[Pasted image 20240820173701.png]]
## Benefits
Simple
## Drawbacks
- A shared runtime prevents us from independently deploying and scaling modules, and prevents failure isolation.
- The logical separation of tables in a single database is not strong. With time, it can turn into a shared integration layer.
- Module coupling and sharing transaction context requires coordination during the development stage and increases the coupling between services.
## Use cases
- Writes to disparate resources can't be eventually consistent.
- Write to heterogeneous data sources.
- Exactly-once message processing is required and we cannot refactor a system and make its operations idempotent.
- Integrating with third-party black-box systems or legacy systems that implement the two-phase commit specification.
# Two-phase commit
## Approach
The operation includes 2 phases:
1. Prepare phase: the coordinator node send a prepare message to all participating nodes, asking them if they are ready to commit the transaction. Each participant acquires a “lock” on the resource/s and replies with either a Yes or No message, indicating whether they can commit.
2. Commit phase: the coordinator decides whether to commit or abort the transaction based on the responses received in the prepare phase. If all participants have responded with a Yes message, the coordinator sends a commit message to all the participants. If any participant has responded with a No message, the coordinator sends an abort message to all the participants, and the transaction is rolled back.
![[Pasted image 20240820175415.png]]
## Benefits
- Consistency
- Atomicity
- Simplicity
## Drawbacks
- Hard to scale dual to all nodes must coordinate with each other
- Expose a single point of failure
- With high throughput, the operation can have significant impact on the performance
# Saga
Transaction in Sage must be [[Microservice#ACID|ACID]]
## Orchestration
### Approach
- Like the 2 phase commit, there is a service acts as the coordinator and orchestrator of the overall distributed state change.
- The orchestrator service has the responsibility to call other services until they reach the desired state or take corrective actions if they fail.
- The orchestrator uses its local database to keep track of state changes, and it is responsible for recovering any failures related to state changes.
![[Pasted image 20240820231018.png]]
### Benefits
- Good for complex workflows involving many participants or new participants added over time.
- Suitable when there is control over every participant in the process, and control over the flow of activities.
- Doesn't introduce cyclical dependencies, because the orchestrator unilaterally depends on the saga participants.
- Saga participants don't need to know about commands for other participants. Clear separation of concerns simplifies business logic.
### Drawbacks
- Additional design complexity requires an implementation of a coordination logic.
- Expose single point of failure.
- Eventual consistency.
### Considerations
- It's challenging as it must coordinate a transaction and maintain data consistency for a process spanning multiple microservices.
- Hard to debug.
- Can't roll back data.
- The implementation must be capable of handling a set of potential transient failures, and provide idempotence for reducing side-effects and ensuring data consistency.
- It's best to implement observability to monitor and track the saga workflow.
- The lack of participant data isolation imposes durability challenges. The saga implementation must include countermeasures to reduce anomalies.
- Compensating transactions don't always work.
### Use cases
- Best for:
	- Ensure data consistency in a distributed system without tight coupling.
	- Roll back or compensate if one of the operations in the sequence fails.
- Less suitable for:
	- Tightly coupled transactions.
	- Compensating transactions that occur in earlier participants.
	- Cyclic dependencies.
## Choreography
### Approach
- Coordinate microservices with exchange events without a centralized point of control.
- Each local transaction publishes domain events that trigger local transactions in other services.
![[Pasted image 20240820233438.png]]
### Benefits
- Good for simple workflows that require few participants and don't need a coordination logic.
- Doesn't require additional service implementation and maintenance.
- Doesn't introduce a single point of failure, since the responsibilities are distributed across the saga participants.
### Drawbacks
- Workflow can become confusing when adding new steps, as it's difficult to track which saga participants listen to which commands.
- There's a risk of cyclic dependency between saga participants because they have to consume each other's commands.
- Integration testing is difficult because all services must be running to simulate a transaction.
# Parallel pipelines
## Approach
- A route service accepts and forward requests to services through a message broker in a single local transaction, therefor services can process the requests independently and in parallel.
![[Pasted image 20240820234654.png]]
# Comparison
![[Pasted image 20240821000329.png]]
![[Pasted image 20240821000346.png]]
# Reference
- https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared#how_to_choose_a_distributed_transactions_strategy
- https://learn.microsoft.com/en-us/azure/architecture/reference-architectures/saga/saga