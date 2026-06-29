# Authorization Model

This document summarizes the authorization model explored in the private enterprise backend foundation.

The goal was to centralize access decisions and avoid spreading permission logic across controllers and services.

## Core Principle

Authorization was designed to be:

- explicit
- centralized
- deterministic
- deny-by-default
- tenant-aware
- auditable
- based on server-derived facts rather than client claims

A route declares the permission it requires. The permission engine decides whether the authenticated principal can perform the action on the target resource.

In everyday terms: the controller should not act like a security guard making local guesses. It should enter through the same main gate as every other route.

## Decision Inputs

A safe authorization decision needs trusted inputs.

Typical inputs include:

- authenticated principal type: human user, service account, or system actor
- tenant context
- requested resource type and action
- route permission key
- resource owner user ID when ownership matters
- branch, department, team, region, location, or custom dimension facts
- resource classification or sensitivity
- relationship facts for ReBAC
- tenant policy rules for PBAC
- session trust level for sensitive operations

The important rule is that resource facts should be derived server-side. Client-supplied ownership, tenant ID, branch code, classification, or relationship data cannot be trusted for authorization.

## Access-Control Styles

The private prototype explored a combined model rather than a single permission style.

### RBAC

Role-based access control was used for role-to-permission assignment.

Example concepts:

- an owner role can manage tenant settings
- an auditor role can read audit logs
- an operator role can update operational records

RBAC provides the base grant model.

### ABAC

Attribute-based access control was used for additional conditions based on trusted subject, resource, request, or scope attributes.

Example concepts:

- allow access only when a user's clearance level matches the resource classification
- deny access when a tenant governance rule does not allow the required dimension
- require stronger session trust for sensitive actions

ABAC inputs should be server-derived or approved attributes, not arbitrary client-provided values.

### ReBAC

Relationship-based access control was used for resource-bound relationship checks.

Example concepts:

- a manager can read records owned by a subordinate
- a user can access a resource only when an exact relationship tuple exists

A key safety rule is that ReBAC checks must be bound to exact resource type and resource ID. A broad relationship label is not enough.

### PBAC

Policy-based access control was used for tenant-level allow/deny policy rules.

The intended behavior is deny-overrides. If a matching deny policy exists, it wins over an allow. If allow policies exist for a target resource/action, an allow gate must pass.

## Tenant Boundary First

Tenant isolation is treated as a first-order security boundary.

The permission engine should reject cross-tenant access before business-level permission rules, except for explicitly authorized system-administration paths.

This matters because a role, relationship, or policy grant is unsafe if the target object belongs to another tenant.

## Scoped Permissions

The prototype supported organization-style scope constraints such as:

- self-only
- branch-only
- department-only
- team-only
- region/location scopes
- custom tenant dimensions
- required relationships
- required owner relationships
- required session trust

A key hardening lesson was that scoped permissions must fail closed when required target-resource facts are missing.

For example, if a permission is branch-scoped and the route does not provide the target resource's branch code from trusted server-side data, the safe decision is to deny access rather than assume the missing value is acceptable.

## Route-Level Pattern

A protected route should conceptually follow this order:

```text
authenticate request
check tenant module / feature availability if relevant
reject machine principals when a human actor is required
resolve trusted resource facts from the server/database
evaluate centralized permission decision
execute controller/service logic
project response fields according to grants
write audit/security evidence when needed
```

The route should not hide permission logic inside service methods as a second, weaker access system. Services may enforce tenant-owned existence and consistency, but they should not replace the permission engine.

## Service Account Boundaries

Service accounts were treated as machine principals, not users.

Design rules included:

- service accounts receive explicit tenant-scoped permissions
- service accounts do not inherit human user memberships
- human-only operations reject service-account principals
- sensitive permissions require explicit service-account-sensitive approval
- audit/security events use machine actor fields rather than pretending the service account is a user

This prevents an API key from silently becoming a human administrator.

## Field Projection Is Separate

Route authorization decides whether the principal can access the resource.

Field projection decides which fields can be returned in the response.

This distinction matters because a user may be allowed to list records but not allowed to see PII, confidential attributes, financial data, performance data, or security-sensitive metadata.

## Lessons From Hardening

The most important authorization lesson was that missing server-derived facts should not be treated as harmless.

If a permission depends on ownership, branch, department, team, relationship, classification, or other resource facts, then the route must provide those facts from trusted server-side data. If it cannot, the decision should fail closed.

That one rule is the difference between “permission checks exist” and “permission checks are reliable.”
