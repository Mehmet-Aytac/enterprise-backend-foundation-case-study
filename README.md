# Enterprise Backend Foundation Case Study

This repository documents a private, AI-assisted enterprise backend foundation prototype.

The full source code is private. This public repository is intended as a portfolio case study that summarizes the architecture, security model, validation strategy, hardening work, and lessons learned.

## Overview

The private project explored how a multi-tenant SaaS backend foundation could be structured around security, auditability, authorization, tenant isolation, and production-readiness concerns.

It was not built as a public starter template or a finished SaaS product. It was developed as a private backend foundation intended to support future business/domain modules.

## Technical Scope

The private implementation focused on:

- Multi-tenant data boundaries
- Browser session authentication
- API/mobile token authentication
- Refresh token rotation and reuse detection
- MFA and recovery-code hardening
- Service account authentication
- RBAC / ABAC / ReBAC / PBAC authorization
- Tenant governance and scoped permissions
- Audit logging and security event separation
- Outbox-based event dispatching
- Tamper-evident audit hash-chain verification
- OpenAPI contract validation
- Dockerized API and worker runtime structure
- Unit, integration, security, and concurrency tests

## Technology Stack

The private prototype used:

- TypeScript
- Node.js
- Express
- PostgreSQL
- Prisma
- Zod
- OpenAPI
- Docker
- Node test runner

## Architecture Summary

The private implementation was organized into several conceptual layers:

- `core`: authentication, authorization, audit, tenancy, middleware, and shared utilities
- `infrastructure`: database, configuration, logging, telemetry, and security primitives
- `modules`: API-facing modules such as identity, users, roles, permissions, tenants, sessions, service accounts, audit logs, and security events
- `tools`: validation, benchmark, smoke test, and audit verification scripts

The design goal was to keep domain modules thin and force them to reuse shared security, authorization, audit, and tenant-boundary controls.

## Authentication Model

The project separated browser-oriented authentication from API-oriented authentication.

Browser flows used DB-backed sessions, secure HTTP-only cookies, CSRF protection, and refresh-token rotation.

API/mobile-style flows used explicit token endpoints.

Security-sensitive flows were reviewed and hardened for concurrency issues, including refresh token rotation, MFA recovery code usage, and TOTP enrollment verification.

## Authorization Model

The authorization layer explored a combination of:

- RBAC for role-based permissions
- ABAC for attribute-based checks
- ReBAC for relationship-bound access
- PBAC for policy-based allow/deny decisions

A key hardening lesson was to fail closed when scoped permissions require branch, department, or team context but the target resource does not provide the required server-derived dimension data.

## Audit and Integrity Model

The private prototype included audit logs, security events, durable outbox dispatching, and per-tenant audit hash-chain verification.

The design focuses on tamper-evidence at the application level. It does not claim that records are impossible to alter under all infrastructure conditions, especially in self-hosted or root-access environments.

## Testing and Validation

The private repository included validation around:

- Type checking
- Linting and formatting
- Prisma schema validation
- OpenAPI contract validation
- Unit tests
- Integration tests
- Fresh database integration runs
- Permission-engine regression tests
- Refresh-token concurrency tests
- MFA recovery-code concurrency tests
- TOTP enrollment concurrency tests
- Audit hash-chain verification
- Local container security scanning

## Security Hardening Lessons

During review and validation, several issues were identified and fixed in the private prototype, including:

- Scoped authorization checks failing open when resource dimension data was missing
- Refresh token rotation allowing parallel refresh attempts to both succeed
- MFA recovery codes requiring atomic single-use enforcement
- Cookie-based refresh responses exposing tokens in response bodies
- TOTP enrollment verification requiring concurrency hardening
- Password reset webhook delivery needing timeout and signed payloads
- Service account sensitive-permission boundaries requiring explicit guardrails
- OpenAPI route documentation needing a manifest-based validation process

## What This Repository Does Not Include

This public repository does not include:

- Full source code
- Private implementation details
- Deployment secrets
- Domain-specific business modules
- Customer data
- A runnable public starter template

## Why the Source Code Is Private

The private implementation may be used as the basis for future commercial or domain-specific products. For that reason, this repository is limited to a case-study format.

Selected implementation details or code walkthroughs may be shared during technical interviews when appropriate.

## Development Note

This was an AI-assisted engineering case study.

AI tools were used to help generate, review, harden, and iterate on parts of the private prototype. My role was to define requirements, evaluate architecture, run validation commands, interpret test results, identify edge cases, document decisions, and guide the hardening process.

This repository is presented as a learning and architecture case study, not as a claim that every implementation detail was independently authored by hand.

## Limitations

This case study does not claim that the private prototype is a certified enterprise product.

Areas that would require further work before real enterprise deployment include:

- Independent penetration testing
- SAST/DAST integration
- Backup and restore runbooks
- Incident response procedures
- Production monitoring dashboards
- Secret rotation processes
- External compliance review

## Status

This is a public case study for a private prototype, not a packaged open-source framework.
