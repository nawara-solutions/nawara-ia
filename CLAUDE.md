# CLAUDE.md

This file gives Claude (via Claude Code) context on the Nawara IA project. Read this first before making changes.

## Shared AI-Agent Workflow Standard

Branch naming, commit message format, PR conventions, and the ADR/ADD/SDD/TDD design-doc
process are defined once for all Nawara Solutions projects in an internal, private standards
repository (`ai-standard`), checked out as a sibling of this repo. This repo's `/branch`,
`/commit`, `/pr`, `/design-doc` commands, its `design-conformance`/`docs-writer`/`tech-lead`
agents, its design-doc hook, `CONTRIBUTING.md`, `docs/README.md` and the `docs/*/template.md`
files are symlinks into that shared source — editing one of them from here edits it for every
other Nawara project too, so don't.

The symlinks require the sibling layout (`ai-standard/` next to `nawara-ia/` under the same
parent folder). On a clone without that layout — including this repo's public view on GitHub —
they dangle; that is the standard's accepted tradeoff. The essential conventions are summarized
publicly in `README.md`, and the design-doc hook skips itself when its script is absent.

## Git Workflow Permissions

- NEVER create a branch, commit, push, or open a PR unless explicitly asked. Ask first, then act.
- Never merge a PR, force-push, or rewrite remote history.
- Commit subject lines must be <= 100 characters (Conventional Commits, `commitlint.config.cjs`).
- Use `/branch`, `/commit`, `/pr` for all version-control work rather than ad-hoc git commands
  — see `CONTRIBUTING.md` (or `README.md` § Contributing when the standard is not checked out).
- `/pr` uses the GitHub MCP server from `.mcp.json` (Docker `ghcr.io/github/github-mcp-server`,
  token read from the `GITHUB_PAT` environment variable). Never write a token into any file.

## What this project is

**Nawara IA** is the shared intelligence platform for **Nawara Solutions** products (Nawara Drive,
Daycare, ERP and future apps). The AI runtime — providers, models, prompts, inference and its
deployment — lives in this repository, not in Nawara Core. Core remains the authority for
identity, permissions and organizational hierarchy (see Architecture principles).

Status: repository initialization only. **No application code exists yet**, and no capability
described in planning documents should be presented as implemented.

## This repository is public

- Never commit secrets, tokens, `.env` files, private keys, production evidence, customer data,
  local absolute paths, or internal documentation copied from other Nawara repositories
  (especially Nawara Core).
- Only `*.example` environment templates may be tracked.

## Architecture principles

- **Python + FastAPI**, modular backend, `src/` package layout, type annotations and validation,
  OpenAPI-based API contracts, pytest-based tests.
- **Modular AI capabilities** (e.g. model gateway, knowledge/RAG, scheduling optimization) behind
  explicit module boundaries; no microservice split without an ADR.
- **Core-authoritative security.** Identity, authorization and permissions, organizational
  hierarchy, tenant scope, correlation and audit come from Nawara Core's platform contracts. This repo never becomes an independent source of truth for
  users, roles, memberships or tenants, and never reads Core's databases directly — API only.
- **Tenant isolation.** Every data access, retrieval index, cache and prompt context is scoped to the
  caller's tenant as asserted by Core; cross-tenant data must never reach a model or a response.
- **Independent lifecycle.** This repo is built, versioned and deployed independently of Core and
  of the consuming apps; consumers integrate over the network API only.
- **Product-agnostic.** Product-specific logic belongs in that product's repo; capabilities here must
  be reusable across Nawara apps.

## Explicit authorization required

Ask the owner first, and do not proceed without an explicit yes, before:

- Installing or pinning dependencies, downloading models, or creating containers/infrastructure.
- Calling external AI providers or any paid API.
- Accessing production systems, production data, secrets, or another repository's files.
- Changing GitHub settings, permissions, rulesets, secrets, or Actions.
- Starting a new roadmap phase or implementing application features.

## Repository isolation

Work only inside `nawara-ia`. Read sibling `ai-standard` only to follow the shared workflow
standard; never modify it from here. Do not modify `nawara-core` or any other Nawara repository.
Never access employer (non-Nawara) repositories or symlinks leading to them.

## Review

Non-trivial changes go through a PR against `main` (squash merge) with independent review. Run the
`design-conformance` agent before opening a PR for an architectural change; it runs automatically
after any write to `docs/{adr,add,sdd,tdd}/` via the `PostToolUse` hook in `.claude/settings.json`.
