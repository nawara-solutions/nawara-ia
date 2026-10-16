# IA-ADR-009: OpenTelemetry, privacy-safe diagnostics, usage accounting and focused evaluations

- **Status:** Proposed
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Operating AI capabilities requires knowing what happened on a request, what it cost, and
whether quality is acceptable. AI requests often contain sensitive content, so ordinary
"log everything" diagnostics would leak data. Cost limits (IA-ADR-004) need reliable usage
data.

## Options considered

1. **Vendor-specific observability**: quick to adopt, but locks in a vendor and may ship
   content to a third party.
2. **Ad-hoc logging**: no standard, hard to correlate across Core, products and Nawara IA.
3. **OpenTelemetry with privacy-safe defaults, usage accounting and focused evaluations**: an
   open standard for traces, metrics and logs; content excluded by default; usage recorded per
   tenant; evaluations for the capabilities that ship.

## Decision

We chose **Option 3**. Nawara IA uses OpenTelemetry for traces, metrics and logs, carrying
correlation context from the calling request (IA-ADR-006). Usage, telemetry and evaluations
are all privacy-aware:

- **Raw prompts, documents and model responses are not stored by default** in logs, traces,
  metrics or usage records.
- Usage (for example token counts, solver time and provider) is accounted per tenant, product
  and capability, as metadata without content.
- Each capability has focused evaluations for quality and safety, including prompt-injection
  cases (IA-ADR-007), run on data that contains no real customer content.

## Rationale

- An open standard keeps the backend choice open.
- Privacy by default fits multi-tenant AI data.
- Usage accounting is required to enforce cost limits.

## Consequences

- Diagnosing content-related problems is harder without stored content.
- Evaluation datasets must be built and maintained without real customer data.

## Security and isolation

- Telemetry and usage records are tenant-scoped and contain no cross-tenant data.
- No customer content is written to logs or traces by default.

## Open questions and verification

- Whether any exception to the no-content default is ever allowed, and if so under which
  approval and safeguards.
- Telemetry backend selection.
- Retention periods for telemetry and usage data.
- Evaluation pass thresholds per capability.
