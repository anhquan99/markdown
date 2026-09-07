# Organization And Governance
## Conway’s Law and the "Inverse Conway Maneuver"
- **Conway’s Law:** Mel Conway’s 1968 paper established that _"Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations"_. If an organization maintains siloed frontend, backend, and database departments, it will inevitably produce a coupled, monolithic software architecture.
- **The Inverse Conway Maneuver:** To combat this technical lock-in, the author advocates for the "Inverse Conway Maneuver". This strategy recommends **evolving your organizational and team structures to actively mirror the desired software architecture**. By organizing cross-functional, domain-driven teams first, you naturally foster the development of autonomous, decoupled micro-frontends.
---
## Decentralized Organizations and Servant-Leadership
- **Decentralized Governance:** Instead of a centralized architectural committee enforcing a "one-size-fits-all" framework (which often becomes a technical bottleneck), individual teams are empowered to make localized technical decisions based on the immediate needs of their business subdomains.
- **The Role of Tech Leadership:** In a decentralized structure, architects, principal engineers, and tech leads act as **servant-leaders**. Their role shifts from making top-down decisions to:
    1. Defining the overarching **architectural guardrails** and boundaries within which teams can safely operate.
    2. Posing the right questions to help teams solve problems collaboratively.
    3. Standardizing and owning cross-cutting, platform-level configurations.
- **What Remains Centralized:** Decentralization does not mean chaos. Core structural decisions must remain standardized across all domains, managed by a centralized Platform or Developer Experience (DX) team. This includes the CI/CD pipeline infrastructure, core programming language/framework standards, error and logging observability platforms, performance budget thresholds, and production-failure support governance.
---
## Team Topologies: Feature vs. Component Teams
- **Feature Teams (Cross-Functional Teams):**
    - _Structure:_ Organized with all the skills (frontend, backend, QA, design) required to deliver a business feature end-to-end.
    - _Architectural Alignment:_ Highly recommended for **horizontal splits**. A feature team (e.g., "Team Burrito") owns a specific micro-frontend fragment (like Product Details) and the APIs it consumes.
    - _Pros:_ Lowers cognitive load because developers focus strictly on their user-facing domain, accelerating feature iterations.
    - _Cons:_ Requires one team to step up and assume ownership over global page composition to prevent layout and event clashes.
- **Component Teams:**
    - _Structure:_ Organized strictly by engineering discipline or technical component.
    - _Architectural Alignment:_ Commonly aligned with **vertical splits**. Backend teams focus purely on exposing highly scalable APIs, while frontend teams focus entirely on browser rendering.
    - _Pros:_ Ideal for cross-platform enterprises (developing web, iOS, Android, and smart TV apps simultaneously). Because APIs are consumed by several clients, component teams design APIs with all consumers in mind, rather than optimizing for just one device and treating others as second-class clients.
---
## Asynchronous Governance: RFCs and ADRs
To maintain open communication and alignment across distributed or remote teams without drowning in synchronous meetings, the author details two essential documentation practices:
- **Requests for Comments (RFCs):**
    - _Purpose:_ A timeboxed markdown document used to propose technical improvements, tool changes, or API contract updates.
    - _Benefit:_ It establishes highly efficient asynchronous communication. Teams can coordinate API contract changes, weigh drawbacks and options, and collect feedback collaboratively across different time zones. Most importantly, it creates a permanent written record that helps onboard new hires by illustrating the context of why a technical direction was taken.
- **Architectural Decision Records (ADRs):**
    - _Purpose:_ High-level records used by architects and tech leads to document the specific rationale behind key architectural implementations.
    - _Benefit:_ ADRs capture a "snapshot in time" of the company’s technical and business realities (the "forces" at play) when a decision was finalized. This helps future developers understand the pragmatic trade-offs that dictated past technology choices.
---
## Enhancing Communication and Preventing Team Silos
To prevent team decentralization from turning into isolated silos, tech departments must actively foster a collaborative culture through targeted techniques:
- **Working Backward (PR/FAQs):** A methodology where teams write a hypothetical press release and mock FAQ list _before_ starting development. This acts as a concise documentation baseline that bridges product managers, backend engineers, and frontend teams before writing code.
- **Communities of Practice (CoP) & Town Halls:** Regular (biweekly or monthly) cross-team meetings of engineers from the same discipline (such as frontend developers). These forums are used to share best practices, discuss pipeline optimizations, and run **Mob Programming** sessions (where an entire group collaboratively solves a highly complex technical feature on a single screen).
- **Managing External Dependencies:** The author warns that **frequent external dependencies are the strongest indicator that your micro-frontend boundaries are poorly designed**. If a team is consistently blocked by other teams to deploy a change, the architecture has mutated into a distributed monolith. When this friction bubbles up, technical leadership must actively rebalance boundaries. This is achieved by either splitting a complex micro-frontend to reduce cognitive load (e.g., dividing an authentication MFE into Sign-In and Sign-Up) or peeling off a complex part into a shared, runtime-loaded component (e.g., extracting a specialized video player).