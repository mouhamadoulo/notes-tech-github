If you're asking for an **Angular 22 learning roadmap (2026)**, here's a practical progression from beginner to advanced, aligned with the current Angular ecosystem. Angular 22 fully embraces Signals, zoneless applications, and modern rendering patterns. ([Angular][1])

## Phase 1: TypeScript Fundamentals

Before Angular:

* TypeScript (types, interfaces, generics)
* ES2023+ JavaScript
* Async/await & Promises
* Modules
* Classes & decorators

**Project:** Build a simple Todo app in TypeScript.

---

## Phase 2: Angular Basics

Learn:

* CLI
* Standalone Components
* Templates
* Data binding
* Directives
* Pipes
* Component lifecycle
* Dependency Injection

**Project:** Notes application.

---

## Phase 3: Routing

* Router configuration
* Lazy loading
* Route guards
* Route resolvers
* Nested routes
* Route parameters

**Project:** Multi-page e-commerce UI.

---

## Phase 4: Signals (Essential in Angular 22)

Master:

* `signal()`
* `computed()`
* `effect()`
* Linked Signals
* `resource()`
* `httpResource()`

These APIs are now production-ready and form Angular's recommended reactive model. ([Angular][1])

---

## Phase 5: Forms

Learn both:

* Reactive Forms
* **Signal Forms** (recommended for new applications)

Topics:

* Validation
* Async validation
* Dynamic forms
* Custom controls

Signal Forms are stable in Angular 22. ([angular.fr][2])

---

## Phase 6: HTTP & APIs

* HttpClient
* Interceptors
* Authentication (JWT/OAuth)
* Error handling
* Retry
* File upload/download

**Project:** Consume a REST API.

---

## Phase 7: State Management

Start with:

* Services + Signals

Then learn:

* NgRx (if working on enterprise applications)
* Signal Store
* Entity management

---

## Phase 8: Performance

Angular 22 emphasizes performance:

* `OnPush` (now the default strategy for new components)
* Zoneless applications
* Deferred loading
* Lazy components
* Incremental hydration
* SSR

These improvements reduce unnecessary change detection and improve startup performance. ([angular.fr][2])

---

## Phase 9: Testing

* Vitest
* Jasmine (legacy projects)
* Component testing
* Service testing
* E2E testing (Playwright/Cypress)

Vitest is now the primary testing direction. ([Angular][1])

---

## Phase 10: Angular Material

Learn:

* Material Design
* Theming
* CDK
* Dialogs
* Tables
* Virtual Scroll
* Drag & Drop

---

## Phase 11: Advanced Angular

* Custom directives
* Custom pipes
* Dynamic components
* Dependency Injection patterns
* Content projection
* ViewContainerRef
* Host Directives

---

## Phase 12: Enterprise Architecture

* Feature-based architecture
* Monorepos (Nx)
* Micro-frontends
* Module federation
* Clean Architecture
* Domain-driven folder structure

---

## Phase 13: Build & Deploy

* Environment configuration
* Production builds
* Docker
* CI/CD (GitHub Actions)
* Firebase
* Azure
* AWS
* Vercel

---

# Recommended Projects

1. Todo App
2. Weather Dashboard
3. Blog CMS
4. Authentication System
5. E-commerce Store
6. Admin Dashboard
7. Chat Application (WebSockets)
8. Kanban Board (Trello clone)
9. Netflix Clone
10. Full ERP/CRM Dashboard

---

# Suggested Learning Order

1. HTML/CSS
2. JavaScript
3. TypeScript
4. Angular Basics
5. Routing
6. Signals
7. Forms
8. HTTP
9. Authentication
10. State Management
11. Angular Material
12. Testing
13. SSR & Hydration
14. Performance
15. Enterprise Architecture
16. Deployment

---

## Bonus Skills

To become a well-rounded Angular developer, also learn:

* RxJS (still important for streams and interoperability)
* REST APIs
* GraphQL
* Node.js + NestJS
* SQL or PostgreSQL
* Docker
* Git & GitHub
* AI-assisted development workflows (e.g., MCP, code generation)

This combination is widely used in enterprise TypeScript stacks and aligns well with Angular's current direction. ([Angular][1])

[1]: https://angular.dev/roadmap?utm_source=chatgpt.com "Roadmap • Angular"
[2]: https://angular.fr/news/angular22?utm_source=chatgpt.com "Nouveautés d'Angular 22 (3 juin 2026) | Angular"
