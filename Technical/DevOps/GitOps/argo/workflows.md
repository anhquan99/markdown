# Workflows
- It is a container-native workflow engine for orchestrating parallel jobs on k8s.
## Support
- Define workflows where each step is a container
- Model multistep workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes. 
## Use cases
- Machine learning pipelines
- Data and batch processing
- Infrastructure automation
- CI/CD
- ...
## Architecture
![](Image/Pasted%20image%2020260410222447.png)
- Argo Server
- Workflow Controller
- User namespace: act as execution plane
### Workflow controller
![](/Image/Pasted%20image%2020260410223408.png)
## Specification
### Workflow
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow 
metadata: 
  generateName: hello-world- # Name of this Workflow
spec:
  entrypoint: hello-world # Defines "hello-world" as the "main" template
  templates: 
  - name: hello-world # Defines the "hello-world" template 
    container: 
      image: busybox 
      command: [echo]
      args: ["hello world"] # This template runs "echo" in the "busybox" image with arguments "hello world"
```
### Template types
- **Container**
- **Script:** a wrapper from the container type, used to run the script.
```yaml
  - name: gen-random-int
    script:
      image: python:alpine3.23
      command: [python]
      source: |
        import random
        i = random.randint(1, 100)
        print(i)
```
- **Resource:** performs operations on cluster Resources directly
```yaml
  - name: k8s-owner-reference
    resource:
      action: create
      manifest: |
        apiVersion: v1
        kind: ConfigMap
        metadata:
          generateName: owned-eg-
        data:
          some: value
```
- **Suspend:** used for suspending execution
```yaml
  - name: delay
    suspend:
      duration: "20s"
```
- **ContainerSet:** like container but allow run multiple container on a pod
- **HTTP:** an HTTP request call
```yaml
      http:
        timeoutSeconds: 20 # Default 30
        url: "{{inputs.parameters.url}}"
        method: "GET" # Default GET
        headers:
          - name: "x-header-name"
            value: "test-value"
```
- **Plugin:** allows you to reference an executor plugin
```yaml
  - name: main
    plugin:
      slack:
        text: "{{workflow.name}} finished!"
```
### Parameters
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: hello-world-parameters-
spec:
  # invoke the print-message template with "hello world" as the argument to the message parameter
  entrypoint: print-message
  arguments:
    parameters:
    - name: message
      value: hello world

  templates:
  - name: print-message
    inputs:
      parameters:
      - name: message       # parameter declaration
    container:
      # run echo with that message input parameter as args
      image: busybox
      command: [echo]
      args: ["{{inputs.parameters.message}}"]
```
- Parameters can be overridden when using argo CLI with `-p` option or using a parameters files using `--parameter-file`.
### Output parameter
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: output-parameter-
spec:
  entrypoint: output-parameter
  templates:
  - name: output-parameter
    steps:
    - - name: generate-parameter
        template: hello-world-to-file
    - - name: consume-parameter
        template: print-message
        arguments:
          parameters:
          # Pass the hello-param output from the generate-parameter step as the message input to print-message
          - name: message
            value: "{{steps.generate-parameter.outputs.parameters.hello-param}}"

  - name: hello-world-to-file
    container:
      image: busybox
      command: [sh, -c]
      args: ["echo -n hello world > /tmp/hello_world.txt"]  # generate the content of hello_world.txt
    outputs:
      parameters:
      - name: hello-param  # name of output parameter
        valueFrom:
          path: /tmp/hello_world.txt # set the value of hello-param to the contents of this hello-world.txt

  - name: print-message
    inputs:
      parameters:
      - name: message
    container:
      image: busybox
      command: [echo]
      args: ["{{inputs.parameters.message}}"]
```
### Step
- Use double dash for a group of steps.
- Use single dash for parallel steps.
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: steps-
spec:
  entrypoint: hello-hello-hello

  # This spec contains two templates: hello-hello-hello and print-message
  templates:
  - name: hello-hello-hello
    # Instead of just running a container
    # This template has a sequence of steps
    steps:
    - - name: hello1            # hello1 is run before the following steps
        template: print-message
        arguments:
          parameters:
          - name: message
            value: "hello1"
    - - name: hello2a           # double dash => run after previous step
        template: print-message
        arguments:
          parameters:
          - name: message
            value: "hello2a"
      - name: hello2b           # single dash => run in parallel with previous step
        template: print-message
        arguments:
          parameters:
          - name: message
            value: "hello2b"

  # This is the same template as from the previous example
  - name: print-message
    inputs:
      parameters:
      - name: message
    container:
      image: busybox
      command: [echo]
      args: ["{{inputs.parameters.message}}"]
```
### Workflow template
- A reusable recipe for workflow, defined once and used across multiple workflows.
- Cluster workflow template is cluster wide version of a workflow template where it is name spaced.
### Condition
- A condition to allow which step should run based on a condition.
- Use `when`.
### Daemon
- Run a container in the background as a service.
- It is terminated when the workflow is done.
- Use `deamon: true`.
### Timeout
- Set a maximum duration for a step or an entire workflow execution.
- If the workflow exceeds this duration, it is terminated and mark as failed.
- Use `activeDeadlineSeconds`.
### Exit handler
- An exit handler specifies a template that guaranteed to run at very end of a workflow, regardless of whether it is succeeded, failed, or terminated.
- Use `onExit`.
### Retry
- Allow a step to retry with a strategy.
- Use `retryStrategy`.
### Success condition
- Success condition helps argo to understand the success definition of a workflow.
- Use `successCondition` and `failureCondition`.
### Artifacts
- A generated artifact(s) from a step for other steps to consume.
- If the artifact consumes too much storage resources, argo support the garbage collection to automatically delete them based on a defined strategy. Use `artifactGC`.
## Directed Acyclic Graph (DAG)
- DAG is tasks (nodes) linked by one-way arrows with 2 rules:
	- Directed: arrows have specific direction.
	- Acyclic: No cycles - following arrows never returns to start.
### Patterns
- Fan-out: 1 task triggers many.
- Fan-in: many tasks must complete before one can start.
### Dependency
- A task is connected by another task(s) by dependency.
- The dependency can be evaluated by operator (and, or, not) and the status of the task (ex: succeeded, failed, ...).
```ad-note
The `dependencies: ["A","B","C"]` is translated to A && B && C
```
### Fail fast
- By default DAGs are designed to stop immediately when a task within it fails and mark the workflow failed.
- If fail fast is configured to be false then all the task is run until completed.
- Use `failFast`.