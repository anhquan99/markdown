- Model your workflows as state machines (1 per workflow).
- Written in JSON.
- Visualization of the workflow and the execution of the workflow, as well as history.
# Task states
- Do some work in your state machine.
- Invoke 1 AWS service.
- Run an 1 activity.
## States
- **Choice state**: test for a condition to send to a branch.
- **Fail or succeed state**: stop execution with failure or success.
- **Pass state**: pass its input to its output or inject some fixed data, without performing work.
- **Wait state**: Provide a delay for a certain amount of time or until a specified time/date.
- **Map state**: Dynamically iterate steps.
- **Parallel state**: begin parallel branches of execution.
# Error handling
- Use Retry (to retry failed state) and Catch (transition to failure path) in the state machine to handle the error instead of inside the Application Code.
- Predefined error codes:
	- `States.All` any error
	- `States.Timeout`
	- `States.TaskFailed`
	- `States.Permissions`
## Retry
- `ErrorEquals`
- `IntervalSeconds`
- `BackoffRate`
- `MaxAttempts`
- When max attempts are reached the Catch kicks in.
## Catch
- Evaluated from top to bottom.
- `ErrorEquals`
- `Next` state to send to
- `ResultPath` - a path that determines what input is sent to the state specified in the Next field
# Wait for Task Token
- Allows you to pause Step Functions during a Task until a Task Token is returned.
# Activity tasks
- Enables you to have the Task work performed by an Activity Worker.
- Activity Workers apps can be running on EC2, Lambda, mobile device, ...
