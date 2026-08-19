# Frontend Architecture and Integration

This document describes the frontend side of the private full-stack ERP/SaaS project and separates **implemented behavior** from **selected but not-yet-complete architecture work**.

The public repository does not include the frontend source code. The purpose is to document enough implementation evidence and architectural intent for portfolio review without exposing the private application.

## Current Project Position

The project originally began as a backend foundation. A separate `frontend/` workspace was later introduced, and the private project is now an active full-stack application.

The current frontend goal is not to build every possible ERP module at once. The active direction is to establish reusable internal-product foundations that future vertical/domain products can build on safely:

- authenticated application shell
- tenant/company and branch context
- permission-aware navigation
- customer/account workflows
- catalog workflows
- sales quote/order foundations
- preferences/settings
- reusable API, localization, formatting, and UI boundaries

Some broader ERP modules remain intentionally deferred or only partially surfaced while the shared foundation is hardened.

## Current Frontend Stack

The private implementation currently uses:

- React
- TypeScript
- Vite
- TanStack Router
- TanStack Query
- Radix Primitives
- i18next / react-i18next
- Fetch API through a small project-owned HTTP client
- design-token CSS
- Lucide icons

The selected architecture also includes:

- Zod for local form coercion and UX validation where useful
- TypeScript API types derived from the backend OpenAPI document

Those last two items are architectural targets and should not be interpreted as a claim that every planned integration step is already complete.

## Application Structure

The private frontend is organized around explicit application boundaries instead of one large component tree.

Representative areas include:

```text
frontend/src/
  app/
  domain/
  features/
  i18n/
  shared/
  styles/
  vertical-modules/
```

The practical goal is to keep application bootstrapping, domain contracts, reusable infrastructure, feature-specific UI, localization, and styling concerns separate enough that the frontend can grow without turning every new ERP module into a cross-application rewrite.

## Routing

TanStack Router is used to build the application route tree.

Current implementation evidence includes:

- lazy-loaded page components
- route-level loading fallback
- shared application shell
- permission-aware route shaping
- routes for dashboard, business partners, customer accounts, catalog, sales, preferences/settings, and administrative foundation pages

The frontend may hide or block unavailable surfaces for UX reasons, but this is not treated as a security boundary. Backend authorization remains authoritative.

## Server State

TanStack Query is used for server-state and adapter-backed data flows.

Representative patterns include:

- query hooks for list/detail data
- mutation hooks for create/update/delete commands
- query-key scoping
- cache invalidation after successful mutations
- short-lived or disabled caching for sensitive projections where appropriate

This separation matters because ERP screens frequently combine filtered lists, detail records, permissions, generated identifiers, and command results. Treating all of that as ordinary local component state would make lifecycle and cache behavior much harder to reason about.

## API Client and Browser Security

The frontend uses a small project-owned Fetch wrapper rather than hiding browser-security behavior behind a large SDK.

Current implementation includes:

- configurable API base URL
- `credentials: 'include'` for browser session flows
- JSON request/response handling
- normalized API error objects
- CSRF-token acquisition before mutating cookie-authenticated requests
- `x-csrf-token` propagation for mutations when required

The browser application also contains an internal API-session setup/smoke flow that exercises the real login, session cookie, CSRF, and bootstrap path during integration work.

The important rule is that client-side state does not become security authority. Tenant identity, permission checks, sensitive response projection, and business invariants remain backend-owned.

## Bootstrap and Context

The application shell is designed around an authenticated bootstrap contract rather than guessing tenant, branch, module, permission, and preference state from unrelated endpoints.

The backend bootstrap response provides the frontend with information such as:

- authenticated user identity
- tenant context
- available branches and default branch
- enabled modules
- role/permission information
- session/trust timing
- preferences/localization facts

The frontend uses those facts to shape navigation and UX. It does not use them to bypass server-side checks.

## Localization and Presentation

User-facing system text is routed through i18next/react-i18next rather than being spread as hard-coded copy across components.

The frontend currently maintains Turkish and English locale structures.

Styling is built around design tokens and project-owned visual rules. The first MVP intentionally avoids a heavy component platform so that the application can establish its own dense operational ERP character before locking into a large visual framework.

Radix Primitives are used where accessible interaction behavior is more important than owning every low-level implementation detail, for example dialogs and other behavior-heavy controls.

## Representative Feature Evidence

### Business Partners

The Business Partners surface demonstrates several frontend concerns together:

- customer/supplier role switching
- list filtering and sorting
- query-driven data loading
- permission-sensitive create behavior
- detail selection
- accessible create/detail dialogs
- mutation handling and cache invalidation
- localized system text

This is useful portfolio evidence because it is not only a static screen; it connects component state, server-state patterns, permission-aware UX, domain types, and API/adaptor boundaries.

### Customer Accounts, Catalog, and Sales

The private repository also contains dedicated feature boundaries for customer accounts, catalog, and sales. The active development plan prioritizes these areas because they are important for the first reusable commercial/vertical foundation.

Not every broader ERP feature is claimed as complete. Purchasing, Inventory, Finance, HR, and other long-term surfaces are intentionally staged according to product need rather than presented as finished functionality.

## Implemented vs Planned

A key rule for this case study is to keep these separate.

### Implemented today

- Vite + React + TypeScript workspace
- TanStack Router route tree
- TanStack Query provider and feature query/mutation patterns
- i18n structure
- design-token styling foundation
- Radix-based accessible interaction surfaces
- project-owned credentialed Fetch client
- CSRF-aware mutation behavior
- browser login/session/bootstrap integration smoke path
- real feature/page boundaries for multiple ERP areas

### Selected architecture / still evolving

- complete OpenAPI-derived frontend type generation coverage
- broader Zod-based local form validation coverage
- complete API-backed behavior for every currently visible/deferred module
- production-grade frontend test stack across all flows
- complete ERP frontend coverage
- external production validation

This distinction is intentional. The project is active and should be evaluated by the quality of its current engineering evidence and direction, not by pretending unfinished work is complete.

## Why This Frontend Matters To The Backend Case Study

The frontend changed the nature of the project.

A backend architecture can look correct in isolation while still being difficult to consume safely from a real browser application. Building the frontend exposed practical integration questions around:

- session-cookie behavior
- CSRF
- bootstrap context
- permission-aware navigation
- sensitive projections and cache behavior
- generated identifiers
- list/detail/query boundaries
- error mapping
- localization
- module enablement
- user and tenant preferences

That makes the full-stack project stronger as an engineering case study: backend contracts are being exercised by a real application surface rather than only documented theoretically.

## Honest Limits

The frontend is active development, not a finished commercial ERP UI.

This case study does not claim:

- complete frontend coverage for every backend module
- production deployment to real customers
- external accessibility or security certification
- complete end-to-end test coverage
- public reproducibility of the private implementation

The accurate description is: **an actively developed operational React frontend integrated with a private multi-tenant ERP backend foundation, documented through a public architecture and engineering case study.**
