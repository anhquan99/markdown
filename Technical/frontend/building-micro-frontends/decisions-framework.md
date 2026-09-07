# Decisions framework
- The Micro-Frontend Decisions Framework serves as the cornerstone for project initiation, forcing architects to address four pillars: **Define**, **Compose**, **Route**, and **Communicate**.
## Define
### Vertical Splits 
- **One micro-frontend per view:** A team owns a business domain end-to-end (e.g., the entire "Catalog" experience).
- This is the gold standard for reducing cross-team dependencies and is informed by Domain-Driven Design (DDD).
### Horizontal Splits
- **Multiple micro-frontends on the same page:** Multiple MFEs coexist on a single view (e.g., a header, a search bar, and a footer from different teams).
- This offers high reusability but requires extreme discipline to avoid performance degradation and integration "hell."
---
## Compose
- Once you define your micro-frontends, you must choose where and how to stitch them together into a unified user interface:
### Client-Side Composition
- An application shell in the browser dynamically loads micro-frontend bundles from a CDN or origin. The author details several client-side technologies: **iframes** (excellent for sandbox isolation but hard to make responsive and difficult to test), **Web Components** (using custom elements as wrappers and shadow DOM to encapsulate CSS styles), and **Webpack 5 Module Federation** (which allows host and remote modules to dynamically share dependencies like React, loading them only once).
### Server-Side Composition
- The origin server aggregates different micro-frontends and returns a compiled HTML page. This is highly optimized for performance metrics (such as time-to-interactive) and SEO, making it the preferred choice for highly indexed websites like e-commerce or news.
### Edge-Side Composition
- The composition happens at the CDN level using **Edge-Side Includes (ESI)**. By delegating page assembly to edge points of presence globally, it drastically reduces latency. This is ideal for static-content applications (like product catalogs) but suffers from poor local developer experience (DX) since testing requires running local CDN environments
---
## Route
- Navigating Between Views
### Client-Side Routing
- The application shell handles **global routing**, intercepting URL changes and loading/unloading the correct micro-frontend. Meanwhile, the loaded micro-frontend is solely responsible for **local routing** (moving between its own internal views). This keeps the shell domain-unaware and highly lightweight.
### Server-Side Routing
- The origin application servers manage routing, mapping paths directly to SSR templates. Scaling this architecture under burst traffic requires horizontal scaling of application servers.
### Edge-Side Routing
- The CDN routes request paths directly to edge-composed templates.
---
## Communicate
### The Shared-State Antipattern
- It is strongly advises **against** using a global shared state across different teams' micro-frontends. This creates "tight coupling," forcing teams to coordinate deployments and dragging them into a sociotechnical bottleneck.
### Same-Page Communication
- If horizontal micro-frontends on the same page must communicate, they should use decoupled, asynchronous mechanisms like custom browser events, an **Event Emitter**, or reactive streams (following the publisher/subscriber choreography pattern).
	- **Custom Events:** Leveraging native browser standards, teams can dispatch custom objects via the global `window` object.
	- **Event Emitters (Publisher/Subscriber):** A highly robust pattern is to instantiate a centralized **Event Emitter** inside the parent container or **application shell**, and then inject that instance into the child micro-frontends as a property (prop) at runtime.
	- **Reactive Streams:** This implementation enforces unidirectional data flows. Decoupled components subscribe to declarative reactive data streams, cleanly separation concerns.
### Cross-Page/Cross-View Communication
- For volatile, transient data (like a product ID), teams should pass information via **URL query strings**. For persistent session data (like JWT authentication tokens or local user preferences), they should use **web storage** (localStorage or cookies) accessed via APIs provided by the application shell.
	- **URL Query Strings**
	- **Web Storage & Cookies**
---
## Best Practice
- To mitigate storage fragmentation and security vulnerabilities, the recommendation using the **application shell as an API gateway proxy**: Instead of letting micro-frontends execute direct `localStorage.setItem` commands, the application shell exposes standardized storage helper methods (e.g., `appshell.set(token)` and `appshell.get(token)`).
- Advantages:
	- It hides storage fragmentation across different hardware targets (e.g., handling storage differently on smart TVs, mobile browsers, and desktop environments).
	- It acts as a centralized gatekeeper to perform memory checks, data encryption, and validation.
	- It keeps domain logic out of the shell, maintaining the micro-frontends' strict runtime boundaries.