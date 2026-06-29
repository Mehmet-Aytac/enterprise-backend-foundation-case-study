# Security Model

This document summarizes the security model explored in the private enterprise backend foundation.

It is a case-study summary, not a third-party security audit, compliance certificate, penetration-test report, or claim of live enterprise production usage.

## Security Goals

The private project was designed around these goals:

- prevent cross-tenant data access unless explicitly authorized through a system-administration path
- keep authentication and authorization separate
- make authorization deterministic, centralized, and deny-by-default
- protect browser cookie sessions from CSRF-style mutation attempts
- keep sessions revocable and persisted outside the application process
- make token reuse, suspicious login, failed credentials, and security-sensitive lifecycle events visible
- preserve audit durability through an outbox worker
- make audit history tamper-evident at the application level
- avoid trusting client-supplied proxy/IP headers unless trusted proxy hops are explicitly configured
- minimize sensitive data exposure in responses and logs

## Protected Assets

The private design treated these as protected assets:

- user credentials and password hashes
- browser session tokens
- access and refresh tokens
- refresh-token hashes and token-family state
- MFA secrets and recovery codes
- password-reset token material
- tenant data and tenant governance settings
- organization hierarchy and membership assignments
- role, permission, policy, and relationship grants
- service-account credentials and permission assignments
- audit logs, security events, and outbox records
- PII, confidential business attributes, HR-like fields, performance data, and security-sensitive metadata

## Actor Types

The design considered several actor types:

- anonymous internet client
- authenticated tenant user
- tenant administrator
- system administrator
- browser client using cookie transport
- API/mobile client using bearer transport
- service account using a machine credential
- outbox worker process
- database migration process

## Trust Boundaries

Important trust boundaries included:

- internet/client boundary into the Express API
- reverse proxy boundary into the application process
- API request path into Prisma/PostgreSQL
- API request path into audit/security outbox
- outbox worker into durable audit/security tables
- browser cookie transport into CSRF-protected mutation routes
- bearer token transport into token/session verification
- service-account credential boundary into machine-principal handling

## Major Threats And Controls

### Cross-tenant access

Tenant boundaries were treated as a first-order security boundary.

Tenant-owned records should be accessed through server-derived tenant context, not tenant IDs supplied by request bodies. Business-level permissions are not safe if the target object belongs to another tenant.

### CSRF against browser sessions

Browser cookie flows require CSRF protection for mutating requests. Bearer-only API/mobile clients should not be forced through browser CSRF checks because they do not rely on ambient browser cookies.

### Token replay and refresh-token reuse

Refresh tokens are designed to be persisted only as hashes and rotated on use.

Reuse attempts are classified so that benign parallel races can be rejected without issuing credentials, while suspicious or repeated reuse can revoke the affected session family.

### Weak or stale sessions

Sessions are DB-backed and revocable. Sensitive operations can require higher session trust through step-up authentication.

### Machine identity confusion

Service accounts are modeled as machine principals, not human users.

Human-only routes should reject service-account principals explicitly. Sensitive service-account permissions require extra guardrails because machine credentials have different risk than human sessions.

### Relationship overreach

Relationship-based access must be bound to an exact tenant, subject, relation type, resource type, and resource ID.

A vague relationship label such as “manager” is not enough by itself. The permission decision needs the exact resource being accessed.

### PII and sensitive-field leakage

Route authorization is not enough.

A user may be allowed to access a resource while still being denied fields such as PII, confidential attributes, internal metadata, performance metrics, or security details. These fields should fail closed unless explicit grants exist.

### Audit loss or audit inconsistency

Audit and security writes are routed through durable outbox processing.

Audit logs use a per-tenant hash-chain append strategy so historical modification or deletion can be detected by verification tooling under the application-level threat model.

## Security Review Rule

A change should be treated as security-breaking if it:

- weakens tenant isolation
- exposes more data by default
- trusts more client input
- weakens credential handling
- bypasses centralized authorization
- removes audit/security visibility
- makes failure modes less explicit
- merges browser-cookie and API-token behavior in a confusing way
- turns a fail-closed path into a fail-open path

Such changes should require a compensating control and targeted regression tests.

## What This Model Does Not Claim

This case study does not claim:

- external security certification
- independent penetration testing
- compliance approval
- absolute audit immutability
- full protection against an attacker who controls the database, application code, backups, and logs
- complete production operational maturity

The professional claim is narrower and more honest: the private prototype explored real backend security boundaries and documented how those boundaries should be validated before production use.
