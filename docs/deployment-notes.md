# Deployment Notes

This document summarizes the deployment and runtime concerns explored in the private enterprise backend foundation prototype.

The public repository does not include runnable deployment templates. This document describes the design considerations only.

## Runtime Shape

The private prototype used a split runtime model:

- API process for HTTP requests
- worker process for audit/security outbox dispatch
- PostgreSQL as the source of truth for sessions, refresh tokens, audit outbox records, audit logs, security events, and business data
- one-off migration process before new runtime releases

The API and worker were intended to be stateless at the application-process level.

## Container Hardening Ideas

The private Docker design explored several runtime hardening practices:

- multi-stage build
- production dependency install in the runtime layer
- no application secrets baked into the image
- no local logs written to container filesystem
- stdout logging
- non-root runtime user
- separate API and worker commands from the same immutable image
- Prisma migrations present in the runtime image
- runtime image checks to ensure package managers were not left available unnecessarily

## Environment Validation

The private prototype included fail-fast environment validation for security-sensitive configuration.

Important deployment checks included:

- production/staging environments should not allow credentialed wildcard CORS
- production cookies should be secure
- trusted proxy hop count should match the real infrastructure topology
- API docs should be disabled by default in production
- password-reset delivery should use a trusted webhook in production-like environments
- webhook delivery should use a configured secret and timeout
- application encryption keys should not use development defaults in production

## CI/CD Validation Strategy

The private repository included a multi-layer validation approach:

- code contract checks: typecheck, lint, formatting, OpenAPI validation, tests, dependency audit, and build
- platform checks: Docker build, Compose configuration, Kubernetes-style manifest rendering, and ECS-style template validation
- runtime image checks: non-root execution and restricted runtime tool surface
- integration checks: PostgreSQL service, migrations, seed, integration tests, hash-chain verification, and performance smoke checks
- vulnerability scanning: dependency audit and container vulnerability scan gates

## Production Considerations Not Fully Solved By Code

A backend foundation can provide good defaults, but real production readiness also depends on operational controls.

Before a real enterprise deployment, the following should be planned:

- managed PostgreSQL or equivalent operational database strategy
- backup and restore runbooks
- migration rollback strategy
- log and audit retention policy
- monitoring dashboards
- alerting for outbox dead-letter growth and security events
- secret storage and rotation process
- incident response procedure
- periodic vulnerability scans
- external penetration testing

## Hosted vs Self-Hosted Integrity

The audit integrity story depends on deployment ownership.

In a managed SaaS model, the provider can protect database access, application code, audit trails, and external logs more strongly.

In a self-hosted or customer-root-access model, infrastructure administrators may be able to modify data, code, or logs. In that model, audit integrity claims should be phrased as application-level tamper evidence unless external anchoring, protected backups, or third-party log export are added.

## Portfolio Takeaway

The deployment work was not only about making the application start in a container.

The main lesson was that production readiness is a combination of application design, runtime hardening, CI gates, environment validation, operational runbooks, and honest boundaries around what the software can and cannot guarantee by itself.