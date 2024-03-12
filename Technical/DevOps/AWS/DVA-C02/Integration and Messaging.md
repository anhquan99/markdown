- Patterns of application communication:
	- Synchronous communications (application to application)
	- Asynchronous/Event based (application to queue to application)
- Service model:
	- [[SQS]] (simple queue service): queue
	- [[SNS]] (simple notification service): pub/sub
	- [[Kinesis]]: real-time streaming model
	
| SQS                                              | SNS                                                  | Kinesis                                                  |     |
| ------------------------------------------------ | ---------------------------------------------------- | -------------------------------------------------------- | --- |
| Consumer pull data                               | Push data to many subscribers                        | Standard: pull data (2MB per shard)                      |     |
| Data is deleted after being consumed             | Up to 12500000 subscribers                           | Enhanced-fan out: push data (2MB per shard per consumer) |     |
| Can have as many workers (consumers) as you want | Data is not persisted (lost if not delivered)        | Possibility to replay data                               |     |
| No need to provision throughput                  | Pub/sub                                              | Meant for real-time big data, analytics and ETL          |     |
| Ordering guarantees only on FIFO queues          | Up to 100000 topics                                  | Ordering at the shard level                              |     |
| Individual message delay capability              | No need to provision throughput                      | Data expires after X days                                |     |
|                                                  | Integrates with SQS for fan-out architecture pattern | Provisioned mode or on-demand capacity mode              |     |
|                                                  | FIFO capability for SQS FIFO                         |                                                          |     |
