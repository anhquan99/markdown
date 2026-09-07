# Building Micro-Frontends

## Micro-Frontend Technical Implementation
- In a real-world implementation, such as the **ecommerce** case study, the architecture must handle specific user journeys. In this project, the system is decomposed into three core units:
	1. **Authentication MFE:** Handles the sensitive logic of user identity and token management.
	2. **Catalog MFE:** Manages the product display and search capabilities.
	3. **Account Management MFE:** Handles user profiles and order history.
- The **Project Evolution** moves these disparate units into a cohesive **Checkout Experience**, where state must be passed securely between the catalog and the payment gateways.
### Webpack 5 Module Federation & Orchestration
- Module Federation allows a **Host** (the container shell) to load **Remotes** (the MFEs) dynamically.
	- **Orchestration:** Architects must choose between "eager" loading (critical path MFEs) and "lazy" loading (MFEs like Account Management).
	- **Dependency Management:** Singleton dependencies (e.g., React) must be shared to avoid version mismatch and bundle bloat.
### Architectural Warning: Webpack Lock-in
- While Module Federation is powerful, architects must be wary of **Webpack Lock-in**. Coupling your entire architecture to a specific bundler’s internal plugin system can make future migrations to tools like Vite or Esbuild significantly more difficult.
## Build and Deploy Micro-Frontends
- A "Culture of Automation" is the only thing standing between a micro-frontend architecture and operational failure.
### Repository Strategies: Trade-offs at Scale
- **Monorepo (Lerna/Yarn/Nx):**
    - _Pros:_ Shared tooling, atomic refactoring.
    - _Cons:_ CI pipelines become slow; creates a "false" sense of coupling.
- **Polyrepo:**
    - _Pros:_ Absolute team autonomy, isolated blast radius.
    - _Cons:_ High governance overhead; difficult to enforce global standards.
### The Testing Pyramid and Integrity
- Architecture must be protected via **Fitness Functions**—automated tests that check for bundle size limits, performance budgets, or unauthorized dependency sharing. Deployment patterns like **Blue-Green** and **Canary Releases** reduce risk, while the **Strangler Pattern** serves as the primary strategy for carving out pieces of a legacy monolith until only the micro-frontends remain.

## Automation Pipeline Case Study
- A standardized pipeline ensures quality remains consistent across a decentralized organization. In a professional MFE environment, the workflow follows five critical stages:
	1. **Initialization:** Environment setup and dependency resolution.
	2. **Code-Quality Review:** Static analysis, unit/integration testing, and visual regression.
	3. **Build Optimizations:** Tree-shaking and minification tailored for federated loading.
	4. **Post-Build Checks:** Artifact validation against a central registry.
	5. **Deployment:** Atomic pushes to AWS S3/Lambdas.
- This rigorous checking reduces the **blast radius**, ensuring that a failing Catalog MFE never reaches production or impacts the Authentication flow.
## Backend Patterns for Micro-Frontends
- Frontends cannot be truly decoupled if they are tethered to a monolithic API.
- Core Integration Patterns
	1. **Service Dictionary:** Enables dynamic discovery of backend endpoints, avoiding hardcoded URLs.
	2. **Backend-for-Frontend (BFF):** Each MFE gets a bespoke API layer, decoupling it from the complexities of downstream microservices.
	3. **GraphQL Federation:** Uses tools like Apollo to stitch multiple schemas into a single, queryable graph for the UI.
- These patterns enable **"Testing in Production"** by allowing architects to route specific frontend versions to specific backend versions, supporting independent evolution of both layers.
## From Monolith to Micro-Frontends Case Study (ACME Inc.)
- ACME Inc.’s migration from an Angular SPA to React-based MFEs provides a blueprint for technical transition. They navigated four critical hurdles:
	- **State Encapsulation:** Preventing global variable pollution between MFEs.
	- **Authenticated Context:** Securely sharing JWTs across independent views.
	- **Localization:** Centralizing language settings while allowing MFE-specific overrides.
	- **Routing:** Coordinating the Shell's global router with the MFE's local internal routing.
- By utilizing **Canary Releases**, ACME Inc. moved their traffic incrementally, proving that a high-risk monolith can be dismantled without a "big bang" failure.
## Introducing Micro-Frontends in Your Organization
- Conway’s Law dictates that systems reflect communication structures. To succeed, organizations must employ the **Inverse Conway Maneuver**, restructuring teams into "Feature Teams" (owning a domain) rather than "Component Teams" (owning layers like CSS or APIs).
- Decentralized Governance Mechanisms:
	- **RFCs (Requests for Comments):** For transparent architectural debate.
	- **ADRs (Architectural Decision Records):** To document the "why" for future teams.
	- **Communication:** Enhanced through Communities of Practice and Working Backward sessions.