# From development to production
## Bind mounts shouldn't be used in production.
- <mark style="background: #ADCCFFA6;">In development:</mark>
	- Containers should encapsulate the runtime environment, but not necessarily the code.
	- Use "Bind mounts" to provide your local host project files to the running container.
	- Allows for instant updates without restarting the container.
- <mark style="background: #ADCCFFA6;">In production:</mark>
	- Image/container is the "single source of truth"
	- A container should really work standalone, you <mark style="background: #FF5582A6;">should not</mark> have source code on your remote machine.
	- Use `COPY` to copy a code snapshot into the image.
	- Ensures that every image runs without any extra, surrounding configuration or code.
## Containerized apps might need a build step (ex React app)
- [[multi-stage-builds]]
```ad-note
Multi-container projects might need to be split (or should be split) across multiple host/remote machine.
```
## Trade-offs between control and responsibility might be worth it.
- Run the "do it yourself" [[deploy-image-to-aws-ec2]]. Managing the configuration more controls, but you have more responsibility. You need to create them, manage them, keep them updated, monitor them, scale them, ...
- With the [[Deploy image to AWS ECS]], the creation, management, updating is handled automatically, monitoring and scaling is simplified.