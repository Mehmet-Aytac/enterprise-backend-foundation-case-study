# Lessons Learned

This document summarizes the main lessons from the private full-stack ERP/SaaS project as it evolved from a backend foundation into an application with a real operational frontend.

## 1. Architecture Is Easier To Generate Than To Trust

AI-assisted development can produce a large amount of structure quickly.

Architecture only becomes meaningful when assumptions are tested:

- What happens under concurrent requests?
- What happens when authorization facts are missing?
- What happens when a token is reused?
- What happens when a response accidentally includes sensitive data?
- What happens when a browser flow does not match the backend auth model?
- What happens when frontend cache behavior ignores tenant or permission boundaries?

The valuable work is not producing files. It is reviewing behavior, reproducing failure modes, interpreting evidence, and hardening the system repeatedly.

## 2. Security-Sensitive Code Needs Regression Evidence

Important problems are often behavioral rather than syntactic.

Examples encountered during backend review included:

- scoped authorization failing open when required resource facts were missing
- refresh-token rotation needing concurrency-safe behavior
- MFA recovery codes needing atomic single-use enforcement
- TOTP enrollment verification needing concurrency hardening
- browser cookie flows needing to avoid exposing token material

These kinds of fixes are more convincing when the failure mode can be reproduced and checked again.

## 3. Missing Security Facts Should Fail Closed

If access depends on branch, department, team, ownership, relationship, classification, session trust, or another server-derived fact, those facts must come from trusted data.

If the system cannot establish the facts required for an allow decision, denial is safer than guessing.

## 4. Browser Auth And API Auth Affect Both Sides Of The Stack

Cookie-based browser authentication and explicit bearer-token API authentication have different risks and different client behavior.

Separating them clarified both backend and frontend design:

- browser flows use HTTP-only cookies
- cookie-authenticated mutations require CSRF protection
- API/mobile flows can use explicit token endpoints
- browser UI does not need to treat localStorage tokens as the default auth mechanism
- the frontend HTTP client must reflect the backend transport/security model

This became one of the clearest examples of full-stack architecture: a backend security decision directly changes browser implementation.

## 5. Frontend Visibility Is Not Authorization

Permission-aware navigation improves usability, but hiding a route or button does not protect data.

The frontend can use bootstrap permissions/capabilities to shape the UI. The backend still has to make the real access decision.

This distinction is easy to say and easy to accidentally violate when frontend and backend are developed separately.

## 6. Server State Needs Explicit Boundaries

ERP interfaces combine filters, lists, detail records, permissions, generated identifiers, mutations, and sensitive projections.

TanStack Query made it clearer that these are server-owned facts with cache lifecycles, not ordinary local component variables.

Important lessons include:

- scope query keys by the context that actually changes the data
- invalidate affected data after successful commands
- avoid broad caching of sensitive projections
- keep mutation results and lifecycle rules aligned with backend authority

## 7. A Real Frontend Is A Contract Test For Backend Design

The backend can look elegant while viewed only through route definitions and tests.

Building the React frontend exposed practical contract questions around:

- bootstrap data
- session-cookie behavior
- CSRF
- normalized errors
- permission-aware navigation
- tenant and branch context
- generated identifiers
- list/detail query shapes
- localization and preferences
- sensitive-field caching

The frontend therefore did more than add screens. It tested whether the backend was actually consumable as an application platform.

## 8. Tamper-Evident Does Not Mean Immutable

Audit hash chains can help detect application-level modification, deletion, or ordering problems. They do not make the underlying database impossible to change.

The correct claim is tamper-evident, not tamper-proof.

Stronger guarantees require operational controls such as protected backups, external log export, object-lock storage, SIEM integration, or external anchoring.

## 9. Documentation Should Separate Implemented Work From Planned Work

A portfolio project becomes less credible when architecture decisions are written as if they are already complete implementation.

The frontend made this especially important because some parts of the selected stack and target architecture are intentionally staged.

The public case study now distinguishes:

- what is implemented today
- what is selected architecture still being completed
- what is intentionally deferred
- what would require external production evidence

This is more useful than a feature list that treats every plan as finished work.

## 10. AI Assistance Still Requires Ownership

Using AI does not remove the need to understand requirements, inspect behavior, challenge assumptions, and validate results.

The parts that created real engineering learning were:

- deciding what should be built
- asking why a design is safe or unsafe
- turning suspected bugs into reproducible checks
- reviewing generated implementation rather than accepting it automatically
- reading validation output
- comparing frontend behavior with backend contracts
- documenting tradeoffs and limits

A useful AI role is closer to implementation partner, reviewer, test designer, tutor, and debugging assistant than an unquestioned code generator.

## 11. A Foundation Becomes More Valuable When A Real Product Surface Uses It

The backend foundation was valuable on its own, but the project changed when a real frontend began consuming it.

The operational UI forced abstract platform decisions to become concrete:

- auth had to work in a browser
- permissions had to shape UX without replacing authorization
- tenant/branch context had to flow through queries and routes
- API errors had to become usable UI states
- domain contracts had to support real forms and lists

The strongest portfolio signal is now the connection between the reusable backend foundation and the evolving user-facing application.

## 12. The Best Portfolio Framing Changes As The Project Changes

The old framing—"private backend architecture and security-hardening case study"—was accurate when the project was backend-only.

The current framing is broader and more accurate:

> A private, AI-assisted, active-development full-stack ERP/SaaS engineering case study, with a security-focused multi-tenant backend foundation and an operational React frontend that is progressively exercising the real backend contracts.

That framing preserves the backend depth without hiding the real frontend work or pretending the overall product is complete.
