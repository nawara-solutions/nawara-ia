# IA-ADR-001: Internal-first, external-ready multi-tenant intelligence platform

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Several Nawara Solutions products (Nawara Drive, Daycare, ERP and future apps) need AI
capabilities such as document question answering and scheduling optimization. Building these
separately inside each product would duplicate provider integrations, prompt handling, security
controls and cost management, and would let each product drift into its own AI conventions.

The first consumers are Nawara's own products. Exposing the platform to third parties may be
worth doing later, but there is no approved external offering today.

## Options considered

1. **Per-product AI features**: each product embeds its own AI integration. Fastest for one
   product, but duplicates effort and security work, and gives no shared governance.
2. **Shared platform for internal products only, single-tenant assumptions**: simpler at first,
   but tenant isolation would have to be retrofitted before any wider use.
3. **Shared multi-tenant platform, internal consumers first, designed so it can serve external
   consumers later**: tenant isolation and API contracts from the start; external access stays
   out of scope until explicitly approved.

## Decision

We chose **Option 3**. Nawara IA is a shared, multi-tenant intelligence platform. Its first
and only approved consumers are Nawara products. Its contracts, tenant model and security
boundaries are designed so that external consumers could be supported later. Opening it to
external consumers requires a separate decision.

**Product identity and tenant identity are separate dimensions.** Every request identifies
both the consuming product (which Nawara application is calling) and the tenant (whose data
is in scope). Neither is derived from the other: one tenant may use several products, and one
product serves many tenants. Authorization, isolation, usage accounting and policies may
depend on either dimension or both.

## Rationale

- One place to implement provider access, data policies, cost limits and audit hooks.
- Multi-tenancy is cheap to design in and expensive to retrofit.
- Starting internal-only limits exposure while the platform matures.
- Keeping product and tenant separate avoids baking one product's tenancy model into a shared
  platform.

## Consequences

- Capabilities must be product-agnostic. Product-specific logic stays in product repositories.
- Every capability carries both product and tenant context from the first version.
- Consumers integrate only over the network API (see IA-ADR-003), which allows independent
  release cycles.
- Some generality costs more up front than a single-product feature would.

## Security and isolation

- Tenant identity and scope are asserted by Nawara Core (IA-ADR-006). This platform never
  becomes a source of truth for users, roles, memberships or tenants.
- Cross-tenant data must never reach a model, cache, index or response.

## Open questions and verification

- The mapping between Core organizations and Nawara IA tenant identifiers is not decided here.
- How the consuming product is identified and authenticated (see IA-ADR-006).
- Criteria and approval process for any future external (non-Nawara) consumer.
- "External-ready" is a design goal, not a verified property. Verify it when an external
  consumer is first considered.
- Verify tenant isolation with automated tests before the first capability is released.
