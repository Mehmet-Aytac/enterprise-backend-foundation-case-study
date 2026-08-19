# Portfolio Positioning

This document explains how to present the project honestly in a CV, LinkedIn profile, GitHub profile, or technical interview now that the underlying private project has evolved from a backend foundation into an active full-stack ERP/SaaS platform.

## Important Naming Context

The public repository name is historical: it was created when the portfolio artifact documented the backend foundation only.

The private project now includes a real React frontend and should no longer be described as "backend only." At the same time, the public repository still contains more backend deep-dive material than frontend material, so the safest framing is to distinguish:

- the **underlying project**: active full-stack ERP/SaaS platform
- the **public repository**: architecture/validation case study with a strong backend focus plus documented frontend evolution

## Recommended Project Titles

Good CV/project titles include:

```text
Full-Stack Multi-Tenant ERP / SaaS Platform
```

```text
Enterprise ERP Platform — React + Node.js / TypeScript
```

```text
Full-Stack ERP Platform — Private Implementation / Public Architecture Case Study
```

If referring specifically to this public repository rather than the private project, this is also accurate:

```text
Enterprise Backend Foundation Case Study — Full-Stack Project Evolution
```

Avoid titles such as:

```text
Production ERP System
```

```text
Production-Certified Enterprise SaaS
```

```text
Open-Source Full-Stack ERP Framework
```

Those overstate the current status.

## Best Short Description

```text
Private-source, active-development full-stack ERP/SaaS platform built with a React/TypeScript frontend and a security-focused Node.js/Express/PostgreSQL backend. The public case-study repository documents architecture decisions, backend security and tenancy controls, frontend integration patterns, validation strategy, tradeoffs, and current limitations.
```

## Recommended CV Version

```text
Full-Stack Multi-Tenant ERP / SaaS Platform
React, TypeScript, Vite, TanStack Router, TanStack Query, Node.js, Express, PostgreSQL, Prisma, Zod, OpenAPI, Docker

Actively developing a private-source full-stack ERP/SaaS platform. The frontend uses React, TypeScript, Vite, TanStack Router/Query, i18n, accessible Radix primitives, and a project-owned Fetch client for browser/API integration. The backend provides multi-tenant isolation, session/JWT authentication, centralized authorization, validation, audit/security processing, OpenAPI contracts, integration/security testing, Docker deployment assets, and CI validation. Public repository documents selected architecture and engineering evidence while the full implementation remains private.
```

## CV Bullet Options

Use only the bullets that fit the specific role.

### Full-stack focused

```text
- Actively developing a multi-tenant ERP/SaaS platform with React, TypeScript, Vite, TanStack Router/Query, Node.js, Express, Prisma, and PostgreSQL.
- Built permission-aware ERP frontend flows with localized UI, accessible interaction primitives, server-state query/mutation patterns, and browser-safe API integration.
- Integrated frontend browser flows with session-cookie authentication, CSRF-aware mutating requests, backend bootstrap context, and normalized API errors.
- Designed frontend feature boundaries around business partners, customer accounts, catalog, sales, settings, and reusable application infrastructure.
```

### Backend focused

```text
- Designed a multi-tenant backend foundation for ERP/internal business applications with tenant isolation, secure authentication flows, and service-account boundaries.
- Modeled centralized authorization using RBAC, ABAC, PBAC, and ReBAC concepts with deny-by-default decisions and server-derived access facts.
- Built auditability-oriented patterns with audit/security event separation, durable outbox processing, and tamper-evident hash-chain verification.
- Maintained validation evidence covering integration, abuse-case, concurrency, response-leak, OpenAPI, CI, and deployment-readiness checks.
```

### Portfolio/public evidence focused

```text
- Published a public architecture and engineering case study while keeping the full implementation private for future commercial/domain-specific reuse.
- Documented implemented behavior separately from planned work to avoid presenting unfinished frontend or production claims as complete.
```

## LinkedIn / GitHub Pin Description

```text
Public architecture and engineering case study for a private, active-development full-stack ERP/SaaS platform: React/TypeScript frontend plus a security-focused multi-tenant Node.js/PostgreSQL backend.
```

## Interview Framing

A strong explanation is:

```text
The project started as a multi-tenant backend foundation and later grew into a full-stack ERP application. The implementation is private, while this public repository documents selected architecture, validation, and engineering evidence. On the backend I focused heavily on tenant boundaries, authentication, authorization, sensitive responses, auditability, and deployment hardening. On the frontend I am building the operational React application around real ERP workflows, TanStack Router/Query, localization, accessible UI primitives, and browser-safe session/API integration. It is active development, not a finished production product.
```

## What To Emphasize

For Backend Developer roles, emphasize:

- Node.js / TypeScript / Express
- PostgreSQL / Prisma
- REST/OpenAPI contracts
- authentication/session/token flows
- tenant isolation
- centralized authorization
- validation and error handling
- integration/security testing
- Docker/CI/deployment readiness

For Full Stack Developer roles, additionally emphasize:

- React + TypeScript
- Vite
- TanStack Router
- TanStack Query / server-state management
- API integration
- cookie/session + CSRF browser flows
- i18n
- accessible UI primitives
- feature/module boundaries
- frontend/backend contract thinking

## What Not To Overstate

Do not claim:

- live production customer usage
- external certification or audit
- a fully completed ERP
- complete frontend coverage for every backend module
- public runnable source
- that every planned frontend architecture item is already implemented

## Correct Status Wording

Good wording:

```text
active development
full-stack ERP/SaaS platform
production-oriented engineering goals
private-source implementation
public architecture and engineering case study
security-focused multi-tenant backend
operational React frontend under active development
```

Wording to avoid:

```text
production-ready
production-certified
fully complete ERP
used by enterprise customers
open-source framework
fully implemented frontend architecture
```

## Why This Is A Strong Portfolio Project

The value is no longer only backend architecture and no longer only a visible frontend.

The strongest signal is the connection between both sides:

- browser session behavior has to match backend auth rules
- CSRF changes the API client design
- permissions shape the UI without becoming the security authority
- tenant/branch context affects routing, queries, and server decisions
- API contracts affect frontend types and workflows
- sensitive response rules affect caching and UI exposure
- domain workflows force frontend and backend boundaries to agree

That is closer to real full-stack product engineering than treating frontend and backend as unrelated demo projects.
