# Authorization Model

This document summarizes the authorization model explored in the private enterprise backend foundation prototype.

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

A route should declare what permission it requires. The permission engine should decide whether the authenticated principal can perform the action on the target resource.

## Access-Control Styles

The private prototype explored a combined model rather than a single permission style.

### RBAC

Role-based access control was used for role-to-permission assignment.

Example concept:

- an owner role can manage users
- an auditor role can read audit logs
- a technician role can update operational records

RBAC provided the base grant model.

### ABAC

Attribute-based access control was used for additional conditions based on trusted subject, resource, request, or scope attributes.

Example concept:

- allow access only when a user's clearance level matches the resource classification
- deny if the request does not satisfy a governance-controlled dimension

ABAC inputs were expected to be server-derived or approved attributes, not arbitrary client-provided values.

### ReBAC

Relationship-based access control was used for resource-bound relationship checks.

Example concept:

- a manager can read records owned by a subordinate
- a user can access a resource only if a specific relationship tuple exists

A key safety rule was that ReBAC checks must be bound to exact resource type and resource ID, not vague relationship labels.

### PBAC

Policy-based access control was used for tenant-level allow/deny policy rules.

The intended behavior was deny-overrides. If a matching deny policy exists, it should win over an allow. If allow policies exist for a target resource/action, an allow gate must pass.

## Tenant Boundary First

Tenant isolation was treated as a first-order security boundary.

The permission engine should reject cross-tenant access before evaluating business-level permission rules, except for explicitly authorized system-administration paths.

This matters because role or relationship permissions are unsafe if the target object belongs to another tenant.

## Scoped Permissions

The prototype supported organization-style scope constraints such as:

- self-only
- branch-only
- department-only
- team-only
- required relationships
- required owner relationships
- required dimensions

A key review finding was that scoped permissions must fail closed when required target-resource facts are missing.

For example, if a permission is branch-scoped and the route does not provide the target resource's branch code, the safe decision is to deny access rather than assume the missing value is acceptable.

## Service Account Boundaries

Service accounts were treated as machine principals, not users.

Design rules included:

- service accounts receive explicit tenant-scoped permissions
- service accounts do not inherit human user memberships
- human-only operations reject service-account principals
- sensitive permissions require an explicit service-account-sensitive approval flag
- audit/security events use machine actor fields instead of human actor fields

## Field Projection Is Separate

Route authorization decides whether the principal can access the resource.

Field projection decides which fields can be returned in the response.

This distinction matters because a user may be allowed to list users but not allowed to see PII, confidential attributes, performance data, or security-sensitive metadata.

## Lessons From Hardening

The most important authorization lesson was that missing server-derived facts should not be treated as harmless.

If a permission depends on resource ownership, branch, department, team, relationship, or classification, then the route must provide those facts from trusted server-side data. If it cannot, the authorization decision should fail closed.