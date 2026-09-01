# Building Micro-Frontends

## The Frontend Landscape
|Architecture|Description|Primary Strategic Benefit|
|---|---|---|
|**Single-Page Applications (SPAs)**|Encapsulates the application in a few JavaScript bundles downloaded at the start.|Native-like experience with instantaneous view transitions.|
|**Isomorphic Applications**|"Universal" code that executes on both the server and the client.|Optimized SEO and faster "Time to Interaction" via server-side pre-rendering.|
|**Static-Page Websites**|Traditional approach where every link initiates a full page load from the server.|Low technical overhead and high security for simple content.|
|**Jamstack**|Modern stack utilizing JavaScript, APIs, and Pre-rendered Markup.|Exceptional scalability and performance by serving static artifacts via a CDN.|
## Micro-Frontend Principles
| Microservices Principle             | Application in Micro-Frontends                         | Technical Realization                                            |
| ----------------------------------- | ------------------------------------------------------ | ---------------------------------------------------------------- |
| **Modeled Around Business Domains** | Aligning MFEs with Bounded Contexts (DDD).             | Avoids "Component MFEs" and reduces cross-team friction.         |
| **Culture of Automation**           | Robust CI/CD for hundreds of independent units.        | Automated testing and deployment blueprints.                     |
| **Hide Implementation Details**     | Consumer-driven contracts and encapsulation.           | Teams can swap frameworks (e.g., React to Vue) internally.       |
| **Decentralized Governance**        | Empowering teams to choose the right tool for the job. | Guardrails over mandates; team-level tech choices.               |
| **Independent Deployment**          | Autonomous release cycles.                             | Zero-downtime releases without global coordination.              |
| **Isolate Failure**                 | Preventing a single error from crashing the UI.        | Graceful degradation and circuit breakers in the shell.          |
| **Highly Observable**               | Deep visibility into client-side behavior.             | Integrated tracking (e.g., Sentry, LogRocket, or OpenTelemetry). |
```ad-note
Complexity vs. Value

Micro-frontends are not a silver bullet. They introduce significant operational overhead in automation and observability. Their value is exclusively realized in high-scale scenarios: long-term maintenance of sprawling platforms, organizations with multiple distributed teams, or complex legacy migrations. For small teams or short-lived projects, the architectural "tax" of micro-frontends will almost always outweigh the benefits.
```
## Micro-Frontend Architectures and Challenges
- The Micro-Frontend Decisions Framework serves as the cornerstone for project initiation, forcing architects to address four pillars: **Define**, **Compose**, **Route**, and **Communicate**.
### Define
- The most critical choice is how to slice the application:
	- **Vertical Splits - one micro-frontend per view:** A team owns a business domain end-to-end (e.g., the entire "Catalog" experience). This is the gold standard for reducing cross-team dependencies and is informed by Domain-Driven Design (DDD).
	- **Horizontal Splits - multiple micro-frontends on the same page:** Multiple MFEs coexist on a single view (e.g., a header, a search bar, and a footer from different teams). This offers high reusability but requires extreme discipline to avoid performance degradation and integration "hell."
### Compose
- Once you define your micro-frontends, you must choose where and how to stitch them together into a unified user interface:
	- **Client-Side Composition:** An application shell in the browser dynamically loads micro-frontend bundles from a CDN or origin. The author details several client-side technologies: **iframes** (excellent for sandbox isolation but hard to make responsive and difficult to test), **Web Components** (using custom elements as wrappers and shadow DOM to encapsulate CSS styles), and **Webpack 5 Module Federation** (which allows host and remote modules to dynamically share dependencies like React, loading them only once).
	- **Server-Side Composition:** The origin server aggregates different micro-frontends and returns a compiled HTML page. This is highly optimized for performance metrics (such as time-to-interactive) and SEO, making it the preferred choice for highly indexed websites like e-commerce or news.
	- **Edge-Side Composition:** The composition happens at the CDN level using **Edge-Side Includes (ESI)**. By delegating page assembly to edge points of presence globally, it drastically reduces latency. This is ideal for static-content applications (like product catalogs) but suffers from poor local developer experience (DX) since testing requires running local CDN environments
### Route
- Navigating Between Views
- **Client-Side Routing:** The application shell handles **global routing**, intercepting URL changes and loading/unloading the correct micro-frontend. Meanwhile, the loaded micro-frontend is solely responsible for **local routing** (moving between its own internal views). This keeps the shell domain-unaware and highly lightweight.
- **Server-Side Routing:** The origin application servers manage routing, mapping paths directly to SSR templates. Scaling this architecture under burst traffic requires horizontal scaling of application servers.
- **Edge-Side Routing:** The CDN routes request paths directly to edge-composed templates.
