# IA-ADR-004: Local-first, cloud-capable inference with strict provider data policies, controlled fallback and cost limits

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Capabilities need language-model inference. Locally hosted models keep data in Nawara's control
and have predictable cost, but may be less capable. Cloud providers offer stronger models but
send data to a third party, bill per use, and apply their own terms for retention, training use
and processing location. Tenants' data may carry contractual or regulatory constraints on where
and how it can be processed.

## Options considered

1. **Cloud provider only**: strongest models, simplest start, but every request leaves Nawara's
   control and cost scales with use.
2. **Local models only**: maximum data control, but capability and hardware limits.
3. **Local-first, cloud-capable through a gateway**: local inference by default; cloud providers
   allowed only where data policies permit, with controlled fallback and cost limits.

## Decision

We chose **Option 3**. All inference goes through the AI gateway, and local inference is the
default. A cloud provider may be used only when **both** policy layers allow it:

1. **Nawara's internal policies**: tenant-level data classification and provider routing rules
   that state which providers may process which classes of data for each tenant.
2. **External provider terms**: the provider's terms for data retention, use of data for
   training, processing region and privacy must be acceptable for that data class.

Fallback from one provider to another is explicit and policy-checked, never silent.
**Sensitive data never automatically falls back to an unapproved cloud provider.** If no
approved provider is available, the request fails. Usage is subject to cost limits.

## Rationale

- Data control by default, with access to stronger models where allowed.
- One gateway is the single enforcement point for policy, fallback and cost.
- Products are insulated from provider changes.

## Consequences

- Policy and cost limits need a defined owner and configuration model.
- Provider terms must be reviewed before a provider is approved, and again when they change.
- Local inference requires hosting capacity (IA-ADR-008).
- Behaviour may differ between providers; evaluations must cover each one in use (IA-ADR-009).

## Security and isolation

- Requests are denied when no policy permits a provider (fail closed, IA-ADR-006).
- Fallback never moves data to a provider that its policy does not allow.
- Provider credentials are configuration secrets, never committed to this repository.

## Open questions and verification

- Data classes, how tenant-level classification is set, and who approves it.
- How external provider terms are reviewed, recorded and re-checked.
- Cost-limit granularity (per tenant, per product, per capability, per period) and behaviour
  when a limit is reached.
- Which local models and cloud providers are candidates. Calling external or paid providers
  needs separate approval.
