# IA-ADR-008: Progressive hybrid deployment, local container-based development and independent hosting when justified

- **Status:** Accepted
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Nawara IA has no application code or production traffic yet. Local inference (IA-ADR-004) may
need hardware that differs from ordinary application hosting. Nawara IA is built and deployed
independently of Core and the products. Committing to production infrastructure now would be
premature.

## Options considered

1. **Production infrastructure up front**: ready for scale, but costly and based on guesses.
2. **Run inside an existing product's or Core's hosting**: no new environment, but couples
   lifecycles and blurs ownership.
3. **Progressive hybrid**: local container-based (Docker) development first; independent
   Nawara IA hosting introduced when real workloads justify it; local and cloud resources
   combined where data policies allow.

## Decision

We chose **Option 3** as the approved deployment direction. Local development is intended to
be container-based (Docker). Nawara IA is to get its own independent hosting when a capability
is ready for real use and its requirements are known. Deployment may combine local and cloud
resources, within the data policies of IA-ADR-004. Nawara IA is never deployed as part of Core
or a product.

This sets a direction only. Creating containers, environments or any infrastructure requires
separate owner approval.

## Rationale

- Infrastructure spend follows proven need.
- Independent hosting keeps the separate lifecycle of IA-ADR-001.

## Consequences

- A later deployment decision (and probably an ADD) is needed before production use.
- Local environments must stay close enough to production to avoid surprises.

## Security and isolation

- Secrets are provided by environment configuration; only `*.example` templates are tracked.
- Environment details and addresses are not published in this public repository.

## Open questions and verification

- Criteria that trigger independent hosting.
- Hosting model for local inference hardware.
