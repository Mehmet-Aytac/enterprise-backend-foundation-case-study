# Enterprise Backend Foundation Case Study

A public case study for a private enterprise-oriented backend foundation prototype.

The private project explored how a multi-tenant SaaS backend foundation could be structured around authentication, authorization, tenant isolation, auditability, security hardening, validation, and deployment-readiness concerns.

This repository is **not** a runnable open-source starter template. The full source code is private. This public repository documents selected architecture decisions, validation strategy, security lessons, and design tradeoffs for portfolio and technical discussion purposes.

## Why This Case Study Exists

The goal was to explore the kind of shared backend foundation that could sit underneath future business/domain modules, such as operations, reporting, governance, HR-like workflows, audit-heavy systems, or other internal/business applications.

The focus was not on a single domain feature. The focus was on the platform-level concerns that future modules would need to reuse safely:

- tenant boundaries
- browser and API authentication
- refresh-token lifecycle safety
- MFA and account recovery
- role, attribute, relationship, and policy-based authorization
- service-account boundaries
- response minimization
- audit and security event durability
- tamper-evident audit history
- CI, integration, concurrency, and security-abuse validation
- deployment and container hardening considerations

## Case Study Documents

| Document | Purpose |
|---|---|
| [Architecture Overview](./docs/architecture-overview.md) | Conceptual structure of the private backend foundation and its module boundaries. |
| [Security Model](./docs/security-model.md) | Security goals, protected assets, threat boundaries, and major controls. |
| [Authorization Model](./docs/authorization-model.md) | How RBAC, ABAC, ReBAC, PBAC, tenant boundaries, and scoped permissions were approached. |
| [Audit and Integrity](./docs/audit-integrity.md) | Audit outbox, security events, and tamper-evident hash-chain design. |
| [Data Classification](./docs/data-classification.md) | Response minimization, field projection, and sensitive-data handling strategy. |
| [Testing and Validation](./docs/testing-and-validation.md) | Validation matrix, security regression work, and recorded result categories. |
| [Deployment Notes](./docs/deployment-notes.md) | Container, runtime, CI/CD, secret, and platform-readiness considerations. |
| [Limitations](./docs/limitations.md) | What this case study does not prove and what would still be needed before real enterprise production use. |
| [Lessons Learned](./docs/lessons-learned.md) | Practical lessons from the AI-assisted design, review, validation, and hardening process. |

## Technical Scope

The private prototype focused on:

- multi-tenant data boundaries
- DB-backed browser sessions
- explicit API/mobile token flows
- refresh token rotation and reuse classification
- TOTP MFA and recovery-code handling
- service account authentication
- RBAC / ABAC / ReBAC / PBAC authorization
- tenant governance and scoped permissions
- audit logging and security event separation
- outbox-based event dispatching
- tamper-evident per-tenant audit hash-chain verification
- OpenAPI contract validation
- Dockerized API and worker runtime structure
- unit, integration, security, concurrency, and performance smoke tests

## Technology Stack

The private implementation used:

- TypeScript
- Node.js
- Express
- PostgreSQL
- Prisma
- Zod
- OpenAPI
- Docker
- Node test runner

## Representative Hardening Work

During review and validation, several issues were identified and fixed in the private prototype:

- scoped authorization checks could fail open when resource dimension data was missing
- parallel refresh-token rotation could allow two requests to both succeed
- MFA recovery codes needed atomic single-use enforcement
- cookie-based refresh responses needed to avoid exposing token material in JSON bodies
- TOTP enrollment verification needed concurrency hardening
- password-reset webhook delivery needed timeout handling and signed payloads
- service accounts needed explicit guardrails for sensitive permissions
- route documentation needed manifest-based validation to reduce OpenAPI drift

The validation summary is documented in [Testing and Validation](./docs/testing-and-validation.md).

## What This Repository Does Not Include

This public repository does not include:

- full source code
- private implementation details
- deployment secrets
- database schema files
- tests or raw logs
- domain-specific business modules
- future commercial product plans
- customer data
- a runnable public starter template

## Why the Source Code Is Private

The private implementation may be used as the basis for future commercial or domain-specific products. For that reason, this repository is limited to a case-study format.

Selected implementation details or code walkthroughs may be shared during technical interviews when appropriate.

## Development Note

This was an AI-assisted engineering case study.

AI tools were used to help generate, review, harden, and iterate on parts of the private prototype. My role was to define requirements, evaluate architecture, run validation commands, interpret test results, identify edge cases, document decisions, and guide the hardening process.

This repository is presented as a learning, architecture, and validation case study. It is not a claim that every implementation detail was independently authored by hand.

## Status

This is a public portfolio case study for a private prototype, not a packaged open-source framework.