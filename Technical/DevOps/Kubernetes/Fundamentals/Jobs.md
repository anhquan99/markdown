
- A Job creates one or more Pods to perform a given task. The Job object takes the responsibility of Pod failures. It makes sure that the given task is completed successfully. Once the task is complete, all the Pods have terminated automatically. Job configuration options include:
	- `parallelism` - to set the number of pods allowed to run in parallel;
	- `completions` - to set the number of expected completions;
	- `activeDeadlineSeconds` - to set the duration of the Job;
	- `backoffLimit` - to set the number of retries before Job is marked as failed;
	- `ttlSecondsAfterFinished` - to delay the cleanup of the finished Jobs.
- CronJob perform Jobs at scheduled times/dates with options:
	- `startingDeadlineSeconds` - to set the deadline to start a Job if scheduled time was missed;
	- `concurrencyPolicy` - to _allow_ or _forbid_ concurrent Jobs or to _replace_ old Jobs with new ones.