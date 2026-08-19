# Interview Walkthrough

This document provides a safe, code-free walkthrough for explaining the private full-stack ERP/SaaS project during technical interviews.

The goal is to make the engineering work understandable without exposing the private implementation or overstating unfinished work.

## 1. Start With The Honest Scope

Use this framing first:

```text
The project started as a multi-tenant backend foundation and has evolved into an active full-stack ERP/SaaS application. The implementation is private. This public repository documents selected architecture, validation evidence, frontend integration patterns, tradeoffs, and limitations. It is active development, not a finished production product.
```

This gives the reviewer the current project shape immediately and avoids the outdated impression that the work is backend-only.

## 2. Explain The Full-Stack Problem

A useful project question is:

```text
How do you build an ERP application where the browser UI, API contracts, tenant context, permissions, sensitive data, domain workflows, auditability, and deployment rules stay consistent as the system grows?
```

In simple terms: the frontend and backend should not become two unrelated applications with different rules.

## 3. Explain The Runtime Shape

Reference the architecture diagram in the README.

A concise explanation:

```text
The React frontend sends credentialed requests through a small Fetch client. The Express backend resolves the session or token, builds trusted tenant/request context, evaluates authorization, runs tenant-scoped domain logic, persists data through Prisma/PostgreSQL, and writes audit/security evidence through the outbox path when required.
```

The key point:

```text
The frontend may shape the user experience from permissions and capabilities, but the backend remains the authority for tenant boundaries, access decisions, sensitive response fields, validation, and business invariants.
```

## 4. Explain The Frontend Architecture

A useful summary:

```text
The frontend is a separate Vite + React + TypeScript workspace. TanStack Router handles application routing, TanStack Query handles server-state and mutations, i18next handles system localization, Radix Primitives provide accessible behavior-heavy controls, and a small project-owned Fetch client handles browser/API integration.
```

Useful implementation examples to mention:

- lazy-loaded ERP routes
- permission-aware route/navigation shaping
- query/mutation hooks and cache invalidation
- Business Partners list/detail/create flows
- customer-account, catalog, sales, dashboard, and settings feature boundaries
- localized Turkish/English system text
- session-cookie + CSRF-aware API requests
- real login/session/bootstrap integration smoke path

Be explicit that the frontend is still under active development and that not every planned ERP surface is complete.

## 5. Explain Browser Authentication and CSRF

A good explanation:

```text
Browser authentication is session-cookie based. Mutating cookie-authenticated requests need CSRF protection, so the frontend HTTP client obtains the CSRF token when necessary and sends it with the mutation. The browser client uses credentials-included requests rather than treating localStorage tokens as the default browser auth model.
```

Why it matters:

```text
This is a good example of a backend security decision directly changing frontend implementation behavior.
```

## 6. Explain Tenant Isolation

A good explanation:

```text
In a multi-tenant system, tenant boundaries are security boundaries, not ordinary filters. Tenant context comes from authenticated server-side request state, and tenant-owned reads/writes must remain scoped even if a client sends an object ID that belongs to another tenant.
```

Useful analogy:

```text
It is like an apartment building. Knowing another apartment number should never become enough to open that apartment's door.
```

## 7. Explain Authorization

A good explanation:

```text
The backend uses centralized deny-by-default authorization concepts including RBAC, ABAC, ReBAC, and PBAC. Routes declare required access and the permission engine evaluates trusted server-derived facts.
```

Frontend connection:

```text
The frontend can hide or disable unavailable actions for usability, but it never treats that visibility decision as authorization. The server checks again.
```

Useful analogy:

```text
The client can display the permission slip, but it cannot grade its own permission slip.
```

## 8. Explain Server State and Feature Boundaries

A good explanation:

```text
ERP screens combine lists, filters, detail records, permissions, generated identifiers, and commands. TanStack Query keeps those server-state concerns explicit, while feature folders keep domain-specific UI and adapters from turning into one global component layer.
```

You can use Business Partners as a concrete example because it combines role-aware lists, filtering, permission-sensitive create behavior, dialogs, mutations, and cache invalidation.

## 9. Explain Response Minimization

A good explanation:

```text
Route access and field access are different. A user may be allowed to list records without being allowed to see every sensitive field on those records.
```

Frontend implication:

```text
Sensitive projection rules also affect cache and UI design. The frontend should not broadly cache or expose restricted fields just because one route can return them under stronger permission.
```

Useful analogy:

```text
Being allowed into a building does not mean every filing cabinet inside the building is open to you.
```

## 10. Explain Auditability

A good explanation:

```text
Audit logs and security events are treated as separate concepts. Important evidence can be routed through a durable outbox worker. Audit entries use a per-tenant hash-chain strategy to provide tamper evidence at the application level.
```

Always add the limitation:

```text
Tamper-evident does not mean immutable. Stronger guarantees require operational controls such as protected backups or external log anchoring.
```

## 11. Explain Validation Work

A good explanation:

```text
The private project is checked through code-contract validation, fresh-database runs, integration behavior, tenant-boundary attempts, response-leak checks, concurrency-sensitive flows, audit hash-chain verification, CI/platform checks, and deployment-oriented review. Frontend work also includes real browser/session/bootstrap integration smoke paths while broader frontend test coverage continues to evolve.
```

Be honest:

```text
These are private/internal engineering checks, not an external audit or certification.
```

## 12. Explain AI Assistance Professionally

Use wording such as:

```text
AI tools were used during implementation support, generation, review, hardening, and documentation. My role was to define requirements, evaluate architecture, review implementation behavior, run validation commands, interpret results, identify edge cases, document decisions, and direct the hardening process.
```

The important point is understanding and ownership of decisions, not pretending no tools were used.

## 13. Good Questions To Invite

This project is useful for discussing:

- React/frontend architecture for internal ERP applications
- TanStack Query and server-state boundaries
- browser session cookies and CSRF
- frontend/backend API contracts
- tenant isolation failures
- centralized vs local authorization
- permission-aware UX vs real authorization
- sensitive field projection and frontend caching
- audit outbox design
- concurrency-sensitive auth flows
- validation strategy
- production-readiness boundaries
- AI-assisted engineering workflow and review discipline

## 14. Questions That Require Careful Scope

### Is this running in production?

```text
No. It is active development and production-oriented in engineering goals, but it is not presented as a live production system.
```

### Is the frontend complete?

```text
No. There is a real React frontend with routing, server-state patterns, API/session integration, localization, and multiple ERP feature boundaries, but broader workflows and production-grade frontend validation are still being developed.
```

### Can I run the public repository?

```text
No. The public repository is a case study. The full backend and frontend implementation is private.
```

### Is it externally audited?

```text
No. The validation was performed within the private project and documented here. External review would be required before stronger production or certification claims.
```

## 15. Closing Pitch

A concise closing statement:

```text
The strongest part of this project is the connection between the two sides. The backend forced me to think about tenant boundaries, auth, authorization, sensitive responses, audit evidence, concurrency, and deployment constraints. Building the React frontend then forced those contracts to work in real browser flows, server-state management, permission-aware UX, localization, and ERP workflows. It is still active development, but it gives me practical experience reasoning about a system end to end rather than treating frontend and backend as separate demos.
```
