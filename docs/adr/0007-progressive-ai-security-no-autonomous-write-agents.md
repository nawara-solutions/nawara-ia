# IA-ADR-007: Progressive AI security, privacy, prompt-injection controls and no autonomous write-capable agents in V1

- **Status:** Accepted
- **Date:** 2026-10-16
- **Deciders:** Nawara IA Principal Architect; repository owner

> This ADR records an architectural decision only. It does not authorize implementation,
> dependencies, infrastructure, deployment or production integration; each requires separate
> owner approval.

## Context

Language-model features introduce risks that ordinary APIs do not have: prompt injection
through user input or retrieved documents, leakage of sensitive data into prompts, outputs or
logs, and models taking actions nobody approved. Applying every possible control from the
first day would delay delivery; applying none would be unsafe.

## Options considered

1. **Full control suite before any capability ships**: safest, but delays value and builds
   controls for risks not yet present.
2. **Minimal controls, add later**: fastest, but risks incidents in the first release.
3. **Progressive controls tied to each feature's risk**: controls grow with capabilities and
   risk, but every feature has the controls its risk level requires before release. In V1,
   models may not take autonomous actions that write to any system.

## Decision

We chose **Option 3**. Security and privacy controls are introduced progressively, but **the
controls required for a feature's risk level must be implemented before that feature is
released**. Progressive means later features may need more controls; it never means releasing
a feature first and securing it afterwards.

Across all features, user input and retrieved content are treated as untrusted, model output
is never executed, prompts contain only data the caller is authorized to use, and diagnostics
are privacy-safe (IA-ADR-009).

In V1 there are **no autonomous write-capable agents**: a model may propose, summarise or
answer, but any change to a product's data goes through that product's own approval and commit
flow.

## Rationale

- Proportionate effort, without releasing known risks.
- The no-autonomous-write rule removes the highest-impact failure mode from V1.

## Consequences

- Each feature needs a risk assessment that sets its required controls before release.
- Some useful automations are deferred until a later decision allows write-capable actions.

## Security and isolation

- Retrieved documents and user input are never trusted as instructions.
- Prompts contain only data from the caller's tenant and authorized resources (IA-ADR-006).
- Sensitive content is excluded or redacted from logs and traces (IA-ADR-009).

## Open questions and verification

- Risk levels, the controls each level requires, and where that mapping is maintained.
- How prompt-injection resistance is tested (IA-ADR-009 evaluations).
- Criteria for a future ADR allowing write-capable agents.
