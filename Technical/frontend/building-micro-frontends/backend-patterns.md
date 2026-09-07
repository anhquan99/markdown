# Backend Patterns
## Service Dictionary
- A **Service Dictionary** is a dynamic lookup registry (often provided via a static JSON file or a dedicated API) that lists the available endpoints in the backend.
- **The Problem It Solves:** It eliminates the need to hardcode API URLs, inject environment variables during the CI/CD pipeline, or create shared endpoint libraries that run the risk of becoming outdated.
- **Implementation by Split Style:**
    - **Vertical Splits:** The micro-frontend fetches its domain-specific dictionary directly on initialization. This keeps the payload small and limits the scope strictly to that team’s subdomain.
    - **Horizontal Splits:** To prevent multiple fragments on a page from initiating redundant, performance-degrading round trips, the **application shell** fetches the dictionary once on startup and exposes or injects the list to the loaded child micro-frontends (e.g., using React Context APIs or the browser’s `window` object).
- **Testing-in-Production:** A service dictionary is highly valuable for canary testing. The application shell can generate a runtime ID and pass it in a header to the service dictionary. The dictionary service checks the database for a matching override configuration, returning test endpoints (e.g., API `v2` instead of `v1`) specifically for that session without affecting other users.
---
## API Gateway
- Commonly used in microservices architectures, an **API Gateway** acts as a single, centralized entry point for client-side applications to communicate with downstream services.
- **Edge Functions:** It centralizes cross-cutting concerns like token validation, rate-limiting, request caching, and metrics collection.
- **Protocol Abstraction:** The gateway can handle client requests using standard HTTPS and translate them into more performant internal protocols (like gRPC) used by backend microservices.
- **Subdomain Partitioning:** Rather than routing all company traffic through a single API gateway (which introduces a single point of failure), the author suggests establishing **one API entry point per business domain**. This ensures that if the billing domain fails, it does not impact the catalog experience, while also giving teams full infrastructure ownership over their specific subdomain.
---
## Backend-for-Frontend
- The **BFF pattern** extends the API gateway by establishing custom, specialized backend layers tailored to specific user experiences.
- **Performance Optimization:** If a view (such as a dashboard) requires data from multiple backend microservices, having the client fetch from each service individually increases "chattiness" and degrades mobile performance. A BFF aggregates these downstream API responses, filters out unused data fields, and returns a single, optimized payload designed around the specific UI view.
- **Subdomain BFFs:** Instead of structuring BFFs purely by device type (e.g., a massive Mobile BFF vs. Web BFF, which can lead to redundant code duplication), the author suggests **partitioning BFFs by business subdomains** (e.g., a Checkout BFF and a Catalog BFF). This allows organizations to apply custom Service Level Agreements (SLAs) and caching strategies to popular services independently.
---
## GraphQL Schema Federation
- For medium-to-large enterprises, **GraphQL Schema Federation** is highlighted as a highly scalable strategy.
	- **Decoupled Schemas:** Traditional GraphQL implementations can create organizational bottlenecks if every team is forced to contribute to a single, monolithic schema file. Schema federation allows individual teams to write, own, and deploy their own independent GraphQL schemas (subgraphs).
	- **Unified Gateway:** A central GraphQL gateway (such as Apollo Server) dynamically stitches these schemas together into a single, cohesive data graph.
	- **Client Autonomy:** When micro-frontends render, they query the unified gateway, requesting only the exact fields they require to populate their views. This maps beautifully to both vertical and horizontal client-side split models, allowing frontend teams to iterate on UI layouts without waiting for backend teams to rebuild REST endpoints.
---
## Best Practices for Backend-Frontend Cohesion
1. **APIs Come First:** Organizations must treat APIs as first-class citizens. Designing the contract and event schemas upfront prevents team blockage and enables parallel development.
2. **Move Configurations to the API Layer:** For cross-platform behaviors (like polling intervals or retry back-off limits), logic should be driven by configurations returned in API payloads. This allows teams to dynamically alter system behaviors on the fly without waiting for a mobile app store approval or a frontend redeployment.
3. **Tolerate Strategic Duplication:** When separating BFFs or gateways, do not rush to abstract common functions into shared libraries. If the abstraction tightly couples teams together, tolerating a small amount of code duplication is a necessary trade-off to preserve team velocity and deployability.