# GitOps
## Definition
- GitOps is a declarative, pull-based framework for continuous delivery of cloud-native applications.
- The core idea is to treat your Git repository as the canonical "single source of truth" for your application code, configurations, and infrastructure definitions.
- Whenever a change is made to this repository, an automated process synchronizes the live environment with the desired state defined in Git.
- Natively built for K8s echo system.
- GitOps is a model developed on pre-existing practices.
  ![](/Image/pasted-image-20240822115446.png)

## Principles
1. **Declarative Desired State:** The desired state of the system is declared in code (e.g., using Kubernetes manifests, Helm charts, or Kustomize) rather than achieved through manual imperative commands.
2. **Versioned in Git:** All declarative configurations are committed to a version control system like Git, providing a single source of truth with immutable commits, diffs, and historical audit trails.
3. **Automated Application of Changes:** GitOps agents (controllers or operators) continuously watch the Git repository for changes (commits or pull requests) and apply them to the target environments automatically.
4. **Continuous Reconciliation:** GitOps agents run a control loop that constantly observes the live environment, compares it to the Git repository, and reconciles any drift (automatically correcting out-of-band modifications)
5. **State Reconciliation:** self-correction mechanism, which is the core engine of GitOps. The GitOps operator is going to ensure the entire system is self-healing to reduce the risk of human error, which will loop from these 3 steps:
	- Observe
	- Diff
	- Act
6. **State store:** single source of truth where every change is recorded.
7. **Feedback loop:** process of observing the actual state of the system and then use this information to inform and improve your desired state and reconciliation process.
## Challenges with traditional workflow deployments
- Addressing problems can be tricky and the cluster (in K8s) can be modified intentionally or accidentally.
- Does not allow developers to return a cluster to its previous stable state easily.
- Can be difficult to spot these issues or visible but fixing them can be complex ad time-consuming Even after applying a fix, it can be very difficult to understand if the current state of the env matches the desired state.
## Operator
- The operator handles CD part, in the image below it is Argo CD.
  ![](/Image/pasted-image-20240822134119.png)

## Types
- **Cloud infrastructure:** used for infrastructure automation
  - Products: Kubestack, Jenkin X
- **Cluster:** used for application automation
  - Products: Flux, Argo CD
## Decision points
1. Where to host git repo?
2. Git repo structure? Level of separation for repos?
3. Runtime (K8s) env structure?
4. Namespace structure? (involved in the FinOps, namespace per env)
5. Operator to use?
6. Sync method?
## Progressive delivery
- Minimum downtime (can be no downtime) and easy roll back.
  ![](/Image/pasted-image-20240822135821.png)

## Deployment model
- **Push-based Deployments:** In a push-based model, popular CI/CD tools (like Jenkins or CircleCI) trigger a build pipeline when application code updates. The pipeline updates the environment configuration and applies the manifests directly to the infrastructure. A downside is that the CI/CD pipeline requires broad ("god-mode") credentials to access the deployment environment.
- **Pull-based Deployments:** The pull-based approach is considered the more secure best practice. An _operator_ living inside the deployment cluster continuously compares the live state against the Git repository. Because the operator lives inside the cluster, it simply pulls updates, meaning external CI/CD pipelines never need direct access to Kubernetes
## Deployment strategy
### Recreate
- All existing Pods are killed before new ones are created.
- **Pros:** No version mismatch (good for database migrations).
- **Cons:** Guaranteed downtime.
### Rolling Update
- Default deployment strategy.
- Kubernetes slowly spins up new pods while draining old ones.
- **Pros:** No downtime; easy to set up.
- **Cons:** No automated "smart" rollback if the new version has a subtle bug (like a memory leak).
### Blue-green deployments
- The GitOps controller sees a new version in Git and creates the **Green** environment. Once Green passes health checks, the **Service** or **Ingress** object in Git is updated to point its selector to the Green Pods. Traffic flips 100% from Blue to Green.
- **Pros:** Near-zero downtime; instant rollback by flipping the Service back.
- **Cons:** Doubled resource costs (you're running two full sets of Pods during the switch).
  ![](/Image/pasted-image-20240822140034.png)

### Canary deployments
- The new version is rolled out to a small subset of users (e.g., 5%) before moving to 100%.
- How it works:
	- You define a "Rollout" object in Git with specific steps (e.g., `setWeight: 10`, `pause: {duration: 1h}`).
	- The controller shifts 10% of traffic to the new version.
	- It can automatically analyze metrics (Prometheus/Datadog). If error rates spike, it **automatically reverts** the Git state or the cluster state.
- **Pros:** Safest method; prevents "blast radius" of bugs.
- **Cons:** Requires a Service Mesh (Istio, Linkerd) or a capable Ingress (Nginx, Traefik) to split traffic accurately.
  ![](/Image/pasted-image-20240822140502.png)

### A/B Testing
- Similar to Canary, but traffic is split based on **specific criteria** (e.g., Header: `User-Agent: iPhone`, or a Cookie) rather than just a percentage.
- **Pros:** Excellent for testing new features on a specific demographic.
- **Cons:** Complex routing logic to manage in your manifests.
### Shadowing
- **Duplicates** incoming requests.
- How it works:
	- **Production (Version A):** Receives the original request, processes it, and sends the response back to the user.
	- **Shadow (Version B):** Receives a "mirror" or "copy" of that same request. It processes the request, but the **response is ignored/discarded**.
	- **Comparison:** You monitor the logs and performance of Version B to see how it handles the real load, data, and edge cases before ever letting a user see it.
## Repository Patterns
- **Monorepo:** Storing the entire configuration in a single repository. While simple, it can suffer from performance issues due to a high volume of commits and complicated per-folder authorization.
- **Repo per Team:** Allocating a repository for each team (or tenant), which naturally mimics organizational structures and simplifies access control.
- **Repo per App:** Keeping an application's code and GitOps configuration separated. The GitOps configs can be populated via **Config Replication** (pushed from a CI server) or by using a **Repo Pointer** (the GitOps operator pulls directly from the app repo via Custom Resources).
- **Repo per Environment:** Creating separate repositories for development, staging, and production. This is often used for strict security or organizational requirements but can result in a tedious deployment process
## Automated rollbacks with Metrics
- Automate canary deployments, you can set different thresholds that define if a deployment is successful or not.
  ![](/Image/pasted-image-20240822141119.png)

## GitOps benefits
- **Auditability and Rollback:** Git acts as an audit log and a transaction log. Rolling back a failed deployment is as simple as executing a `git revert`.
- **High Velocity:** Product teams can ship updates many times a day using tools they are already familiar with.
- **Security:** With a pull-based approach, developers do not need direct access to production environments, greatly reducing the attack surface.
- **Resilience and Consistency:** Automated reconciliation self-heals any drifted or failed deployments, ensuring the live state matches the repository
## Challenges and Limitations
- **Programmatic Update Conflicts:** Git was designed for manual editing. Automated CI pipelines writing back to Git can cause push conflicts requiring complex retry mechanisms.
- **Repository Proliferation:** Complex environments may require hundreds of Git repositories, demanding significant automation just to manage and provision them.
- **Lack of Visibility:** Answering questions like "how often was application X deployed?" can be surprisingly difficult, as it requires digging through vast git histories and text files.
- **Secret Management:** Git is not designed to store plain-text secrets. While workarounds like sealed secrets exist, tracking secrets distributed across many Git repositories remains challenging.
- **Lack of Input Validation:** API calls can validate requests immediately, but GitOps relies on the user to ensure manifest files are correct before committing
## Tools

- [[Argo CD]]
