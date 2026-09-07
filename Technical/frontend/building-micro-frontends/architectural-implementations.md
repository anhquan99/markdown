# Architectural Implementations
## Vertical split
- In a micro-frontend architecture, choosing a **vertical split** is one of the most natural and pragmatic starting points when migrating from a monolithic Single-Page Application (SPA). Under the **Micro-Frontends Decisions Framework**, the vertical split focus defines the application’s boundaries by slicing the user interface into independent, business-domain-driven experiences rather than technical layers.
- When analyzing vertical splits in the larger context of architectural implementations, the book details how this model dictates your choices across composition, routing, data-sharing, and team topologies.
### The Core Concept: DDD Bounded Contexts
- In a vertical split, **each micro-frontend is treated as a fully independent application (or a miniature SPA)** that represents a single, cohesive business subdomain.
	- **Mapping the Domain:** Rooted in Domain-Driven Design (DDD), this pattern maps the frontend interface and its corresponding backend APIs together inside a single **bounded context**. For example, Team Checkout owns the checkout APIs, the database tables, and the checkout micro-frontend end-to-end.
	- **Clean Encapsulation:** Implementation details (such as state management libraries like MobX or Redux) are hidden behind the public contracts of the micro-frontend. Because only one micro-frontend is active at a time inside the user session, teams do not run the risk of style sheet collisions or dependency clashes.
### Technical Implementation Framework
- Implementing a vertical-split architecture typically pushes teams toward a very specific set of technical choices within the decisions' framework
#### Composition: Client-Side via an Application Shell
- While other patterns can theoretically support different composition types, the vast majority of vertical-split architectures utilize **client-side composition** managed by a lightweight, domain-unaware **application shell**.
	- The application shell acts as an orchestrator. It is responsible for loading the initial configuration, verifying global settings (like country or locale), and dynamically mounting and unmounting micro-frontends in response to URL changes.
	- To keep the shell technology-agnostic, the author recommends writing it in **Vanilla JavaScript** rather than loading a heavy UI framework, ensuring it remains as lightweight as possible to preserve fast initial page loads.
#### Routing: Dual-Layer (Global vs. Local)
- Routing is split into two distinct, highly decoupled layers:
	- **Global Routing:** The application shell intercepts navigation requests. On startup, it fetches a dynamic routing configuration mapping URL paths to micro-frontend entry-point assets. The shell handles transition effects, loading indicators, and unloads the current micro-frontend before mounting the next one.
	- **Local Routing:** Once a micro-frontend is mounted, the application shell steps back. The micro-frontend's internal router (e.g., React Router) takes full control of navigating between its own subviews. This ensures that the application shell remains completely unaware of internal domain routing structures.
#### Communication: Lightweight and Decoupled
- Since only one vertical micro-frontend is loaded at a time, complex same-page publish/subscribe event systems (like Event Emitters) are rarely needed. Instead, communication is divided by data volatility:
	- **Query Strings (Volatile Data):** Ephemeral data, such as a product ID or search filter being passed from a search view to a product details view, is appended directly to the URL query string. The newly mounted micro-frontend reads the query parameter on initialization and handles the data fetch.
	- **Web Storage API (Persistent Data):** Persistent session data (such as a JWT authentication token or user preferences) is stored in browser storage (localStorage or cookies).
	- **The Shell Gateway Proxy:** As a security and performance best practice, the micro-frontends do not access local storage directly. Instead, they use standardized APIs exposed by the application shell (e.g., `appshell.get(token)`). This allows the shell to act as a secure proxy, validating storage space, handling encryption, and providing resilient fallbacks across diverse hardware platforms.
### Architectural Trade-offs and Characteristics
- **Deployability (5/5):** Because each micro-frontend compiles down to standard static JavaScript chunks and HTML files, they can be deployed independently into cloud storage (like Amazon S3) with a Content Delivery Network (CDN) in front of them. This introduces a highly scalable and robust deployment pipeline.
- **Scalability (5/5):** Serving static files through global CDN points of presence scales flawlessly, reducing origin server loads and optimizing time-to-first-byte globally.
- **Simplicity (4/5):** The mental shift is highly intuitive. Because the developer experience so closely mirrors writing a traditional Single-Page Application, developers do not need to learn complex orchestration tools or alter their daily coding habits.
- **Developer Experience (4/5):** Frontend developers use familiar bundlers, linting setups, and local testing scripts. Teams can run and save code locally with lightning-fast rebuild times because they are only compiling their small subdomain rather than the entire enterprise monolith.
- **Testability (4/5):** Testing is straightforward. Each domain team writes standard unit and integration tests inside their repository. The only complex edge case is end-to-end testing of cross-domain redirects (such as verifying a logout action inside the catalog MFE successfully lands on the sign-in MFE).
- **Performance (4/5):** Highly optimized for real-world user paths. Instead of forcing a visitor to download a giant, monolithic 500 KB bundle containing the code for every section, a user viewing the landing page only downloads the small <100 KB bundle needed for that specific view.
- **Modularity (2/5):** This is the vertical split's primary weakness. Because each micro-frontend is designed end-to-end as a single logical business domain, you cannot easily share features across views. Furthermore, if a vertical micro-frontend becomes too large and needs to be split, decoupling the internal dependencies is highly complex.

---

## Horizontal split
- In a micro-frontend architecture, selecting a **Horizontal Split Focus** is one of the most granular, versatile, and complex technical paths an architect can take. Under the **Micro-Frontends Decisions Framework**, a horizontal split means that **multiple micro-frontends exist simultaneously inside the same view or page**, with different teams taking ownership of individual visual fragments.
- When evaluating the horizontal split in the larger context of architectural implementations, the book outlines how this focus reshapes technical choices across boundaries, composition, communication, and backend integration.
### The Core Philosophy and Use Cases
- In a horizontal split, different visual sections of a single page (such as a header, a product list, a video player, or a comment section) are modeled as individual, self-contained business representations of a subdomain.
- **Target Scenarios:** The author notes that horizontal splits are highly suited for **large-scale engineering departments** or projects with a **high level of code reusability**. This includes:
    - **E-commerce Sites:** Where the catalog UI features subtle behavior variations (such as a generic product carousel and a specialized video player) on the same page.
    - **B2B / Multitenant Applications:** Where a customer can request high customization of specific elements on a dashboard without needing to fork or redeploy the entire core platform.
### The "Component vs. Micro-Frontend" Boundary Trap
- Because multiple fragments share the screen, a major implementation challenge is avoiding **over-engineering**. The author warns against creating several tiny, fractional micro-frontends (often referred to as "nano-frontends"), as this blurs the line between a _micro-frontend_ and a standard _UI component_.
- A clear **rule of thumb** to distinguish the two:
	- **UI Component:** A technical solution built for reusability. It is typically extended for different use cases by exposing multiple configuration properties (props) to cover various scenarios.
	- **Micro-Frontend:** A complete business representation of a subdomain. It encapsulates its own internal business logic and communicates with the outside world strictly via events.
	- **The Over-Engineering Red Flag:** If you discover that multiple horizontal micro-frontends on the same page are fetching data from the **exact same API**, it is a clear sign that you have pushed the split too far and must refactor to consolidate those boundaries.
### Composition Paradigms for Horizontal Splits
- A horizontal split relies heavily on the chosen composition layer to stitch fragments together seamlessly.
#### Client-Side Composition
- An **application shell** is served to the client and acts as the orchestrator to load, mount, and coordinate the active micro-frontends on the page. The author evaluates three main technologies:
	1. **Webpack 5 Module Federation:** Allows the host application shell to dynamically import remote JavaScript chunks at runtime. Its biggest advantage is **dependency deduplication**—marking framework files like React or Vue as singletons so the browser only downloads them once, which drastically optimizes client-side performance.
	2. **Iframes:** Provides the absolute strongest sandboxed runtime environment, preventing stylesheet and dependency clashes between micro-frontends. However, they are heavy on CPU and memory, non-indexable for SEO, and notoriously difficult to end-to-end test due to deep DOM nesting.
	3. **Web Components:** Uses custom elements (e.g., `<my-account-mfe>`) and the Shadow DOM to encapsulate CSS styles so they do not leak globally. It is highly framework-agnostic but requires dynamic rendering strategies if SEO is a key requirement.
#### Server-Side Composition
- A **composer layer** (such as an NGINX reverse proxy or custom application servers) aggregates the HTML fragments served by different microservice backends before delivering a compiled page to the browser.
	- **SSI (Server-Side Includes):** A classic 1990s web-server standard that uses placeholder directives like `<!--# include virtual="..." -->` to fetch and assemble page pieces in parallel at the origin server level.
	- **Enterprise SSR Frameworks:** The book cites Zalando's _Interface Framework_, American Express’s _OneApp_ (serving Holocron modules), and OpenTable’s _OpenComponents_. This approach yields peak initial load performance and SEO but is complex to scale under traffic bursts.
#### Edge-Side Composition
- Uses **Edge-Side Includes (ESI)** at the global CDN level to assemble dynamic HTML pages closer to the user. While highly scalable and performant for static product catalogs (like IKEA), it is incredibly complex to test locally due to the difficulty of replicating CDN behavior on a developer's laptop.
#### Inter-MFE Communication (Preserving Loose Coupling)
- Sharing state directly (e.g., using a global Redux store across different teams' micro-frontends) is a **severe architectural antipattern**. It tightly couples teams, introduces deployment coordinate dependencies, and turns your setup into a Coupled Monolith.
- To maintain independent deployability, horizontal micro-frontends must implement the **choreography pattern** via decoupled, asynchronous messages:
	- **EventEmitter or CustomEvents:** One micro-frontend dispatches a decoupled custom event payload, and any interested sibling micro-frontends listen and react to it independently.
	- **URL Query Strings:** Used for volatile, ephemeral data (like passing a product ID from a catalog fragment to a detail fragment).
	- **Web Storage/Cookies:** Reserved for long-lived, persistent session data (such as user authentication tokens).
#### Backend API Integration Challenges
- A horizontal split introduces specific data fetching hurdles that must be architected carefully to prevent performance decay:
	- **The BFF Dilemma:** Using a Backend-for-Frontend (BFF) gateway with client-side horizontal composition can create complex coupling. If the application shell is forced to retrieve data for all micro-frontends on the page to prevent "chattiness", it creates a deployment dependency bottleneck.
	- **The Service Dictionary Rule:** To prevent multiple horizontal fragments from initiating redundant, overlapping API calls to find backend endpoints, the **application shell must fetch the Service Dictionary once at startup**. The shell then exposes this unified endpoint list via the browser's global `window` object or injects it using React Context APIs to keep individual micro-frontends fast and decoupled.
### Technical Characteristics and Scoring Matrix

|Architecture Characteristic|Client-Side (Module Federation)|Client-Side (Iframes)|Server-Side Composition|Edge-Side Composition (ESI)|
|---|---|---|---|---|
|**Deployability**|**4/5**|**5/5**|**4/5**|**3/5**|
|**Modularity**|**4/5**|**3/5**|**5/5**|**4/5**|
|**Simplicity**|**5/5**|**3/5**|**3/5**|**2/5**|
|**Testability**|**4/5**|**3/5**|**4/5**|**3/5**|
|**Performance**|**4/5**|**2/5**|**5/5**|**3/5**|
|**Developer Experience**|**5/5**|**3/5**|**3/5**|**2/5**|
|**Scalability**|**5/5**|**5/5**|**3/5**|**4/5**|
|**Coordination**|**3/5**|**3/5**|**3/5**|**3/5**|