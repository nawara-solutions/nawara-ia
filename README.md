# Nawara IA

Shared AI platform for Nawara Solutions products. It will provide reusable AI capabilities
(model gateway, knowledge retrieval, scheduling optimization) to Nawara applications over a
network API.

## Status

**Repository initialization only.** There is no application code yet, and none of the
capabilities above are implemented.

## Architecture principles

- **Planned stack:** Python, FastAPI, `src/` package layout, OpenAPI contracts, pytest.
- **Nawara Core is authoritative** for identity, permissions, organizational hierarchy and
  tenant scope. This service consumes Core's API and never becomes a separate source of truth
  for users, roles or tenants.
- **Tenant isolation:** every data access, retrieval index, cache and prompt context is
  scoped to the caller's tenant.
- **Product-agnostic:** product-specific logic belongs in the consuming product's repository.

## Contributing

### Branches

Branch off `main` and name the branch `<type>/<short-kebab-description>`, for example
`feat/model-gateway` or `docs/rag-design`. Keep branches short-lived and scoped to one pull
request.

### Commits

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <summary>
```

- **type:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`,
  `chore` or `revert`
- **scope** (optional): the affected area, e.g. `gateway`, `rag`
- **summary:** imperative, lowercase, no trailing period; whole subject line ≤ 100 characters

`commitlint.config.cjs` holds the rule set. A local commit hook is not wired up yet.

### Pull requests

- Open pull requests against `main`. The title follows the commit format, because it becomes
  the commit message.
- Fill in `.github/PULL_REQUEST_TEMPLATE.md` (Summary, Changes, Test plan).
- Pull requests are **squash-merged** after independent review.

### Design documents

Significant design work is recorded under [`docs/`](docs) before implementation:

| Folder | Document | Use it for |
| --- | --- | --- |
| [`docs/adr`](docs/adr) | Architecture Decision Record | One hard-to-reverse decision; immutable once accepted |
| [`docs/add`](docs/add) | Architecture Design Document | How a system boundary is composed |
| [`docs/sdd`](docs/sdd) | Software Design Document | Internal design of one module or service |
| [`docs/tdd`](docs/tdd) | Technical Design Document | Implementation plan for one feature |

Each folder's `README.md` explains naming and workflow.

### Internal tooling

Some files are symlinks into an internal Nawara Solutions standards repository that is not
public: `CONTRIBUTING.md`, `docs/README.md`, the `docs/*/template.md` files, and the Claude
Code commands, agents and hook under `.claude/`. In a standalone clone they do not resolve,
which is expected. The conventions above are the essentials you need to contribute.

## Security

Never commit secrets, tokens, `.env` files or private keys. Only `*.example` environment
templates may be tracked.
