# Resource Definition
- Like [[Docker compose]] for multiple deploy.
- It's declarative.
- A config file is defined and applied to change the desired state.
- It's better to declare the **service** first because all pods are built from top to bottom.
- `livenessProbe` health check