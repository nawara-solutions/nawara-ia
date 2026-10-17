# IA-ADR-006: Core-authoritative hybrid authorization, tenant isolation and fail-closed security

- **Status:** Accepted
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Nawara Core is the authority for identity, permissions, organizational hierarchy and tenant
scope across Nawara products. Each product backend owns its own domain resources (for example
documents or bookings) and the permissions on them. Nawara IA serves several products and
tenants and handles their data. It needs to make authorization decisions quickly, without
becoming a second source of truth that can drift from Core or the products.

## Options considered

1. **Nawara IA keeps its own users, roles and tenants**: independent, but duplicates Core and
   will drift from it.
2. **Ask Core for every authorization decision**: always current, but adds latency, makes Core
   availability a hard dependency for every call, and ignores product-owned resource
   permissions.
3. **Hybrid**: Core asserts identity, tenant and shared permissions; product backends remain
   authoritative for their domain resources; Nawara IA verifies these assertions and applies
   its own capability policies on top, failing closed when anything is missing or invalid.

## Decision

We chose **Option 3**:

- **Core** owns shared identity and permission authority: identity, permissions,
  organizational hierarchy, tenant scope, correlation and audit context.
- **Product backends** remain authoritative for permissions on their own domain resources.
- **Nawara IA enforces** all three layers: Core-authorized access, product-specific resource
  restrictions, and Nawara IA's own capability policies (for example which capabilities a
  product may call, or the limits in IA-ADR-004).
- Nawara IA **must not create an independent, competing role system**. Its capability policies
  are configuration about Nawara IA capabilities, not a parallel model of users or roles. It
  never reads Core's or products' databases.
- **Revocation-sensitive decisions require fresh authority checks** rather than relying only on
  an earlier assertion.
- Any request with missing, invalid or ambiguous identity, tenant or resource context is
  rejected.

## Rationale

- Each kind of authority stays with its owner, with no drift.
- Local verification keeps latency low for ordinary requests; fresh checks protect the
  sensitive ones.
- Failing closed means configuration mistakes deny access rather than leak data.

## Consequences

- Nawara IA depends on Core's and products' contracts and must track their changes.
- Each capability must document which decisions are revocation-sensitive.
- Capability policies must be documented per capability.

## Security and isolation

- Tenant scope is taken only from the verified Core assertion, never from request bodies or
  client-supplied parameters.
- Every data access, retrieval index, cache entry and prompt context is scoped to that tenant.
- Cross-tenant data must never reach a model or a response.
- Failures to verify identity, tenant or resource context are logged without sensitive content
  (IA-ADR-009).

## Open questions and verification

- **The real Core contracts remain unverified until separately certified.** Their format,
  signing, expiry and revocation behaviour are defined by Core and are not reproduced here.
- How product backends express resource permissions to Nawara IA.
- Which decisions count as revocation-sensitive, and the freshness each one requires.
- Service-to-service authentication between products and Nawara IA.
- Automated tests that prove cross-tenant and unauthorized-resource access is denied for every
  capability.
