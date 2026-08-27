# Contributing to ScaleKit Studio

This is the shared engineering workflow. A repository README may add language-specific
commands or stronger checks, but it must not weaken these rules.

## Before starting

1. Read the repository README and identify which service owns the behavior.
2. Confirm the change has one concern and a clear acceptance condition.
3. Check existing issues and pull requests to avoid parallel implementations.
4. For schema, security-boundary, or cross-repository changes, record the rollout and
   compatibility plan before implementation.

Do not place roadmaps, agent memory, session handoffs, or temporary TODO lists in product
repositories. Keep durable developer documentation in READMEs and code-adjacent runbooks;
keep private planning state in the internal project record.

## Branches

Runtime repositories use `dev` as their integration branch:

```text
feature/* or fix/* -> PR to dev -> CI -> immutable nightly -> verified release -> PR to main
```

Create branches from current `dev`:

```bash
git fetch origin
git switch --create feature/short-description origin/dev
```

Use `fix/short-description` for defects. Utility repositories that document `main` as
their default branch follow the same PR discipline directly against `main`.

Never force-push a shared branch. Never push feature work directly to `dev` or `main`.

## Local validation

Run the exact commands in the repository README before opening a PR. At minimum:

- Format and lint changed code.
- Build the complete service or package.
- Run relevant tests.
- Exercise migrations against both an empty and current schema when the schema changes.
- Verify browser-visible behavior in a browser when UI or authentication changes.
- Check that no secret, generated credential, customer data, or local environment file is
  included in the diff.

Do not leave debug prints, temporary logging, generated archives, or local fixtures in a
commit.

## Commits and pull requests

Use conventional commit subjects:

- `feat:` new behavior
- `fix:` defect correction
- `chore:` maintenance and tooling
- `refactor:` behavior-preserving restructuring
- `docs:` documentation only
- `test:` test-only changes

Keep commits atomic and do not add co-author trailers. Pull requests must explain:

- What changed and why.
- The product or service boundary affected.
- Exact local validation performed.
- Any migration, deployment, rollback, security, or compatibility effect.
- Screenshots or recordings when behavior is visual.

Resolve review conversations and keep the branch current when required checks request it.
Repositories use squash merge so the PR becomes one integration commit.

## Integration and nightly review

A successful PR check proves the proposed change. After squash merge to `dev`, CI runs on
the actual integration commit. Only then does the repository publish an immutable
`n-<full-sha>` image.

The coordinator assembles one exact commit from each runtime repository, deploys them to
an isolated review environment, checks real TLS routes, and records
`nightly/burner=success` on every verified commit. A moving `nightly` tag is convenient
for humans but is never release evidence.

## Production promotion

`main` is the production release line, not a development branch. Runtime `dev` and `main`
histories are intentionally independent, so release gates compare trees rather than
ancestry.

Production promotion requires:

1. A reviewed and successful exact-manifest nightly.
2. A release snapshot whose tree exactly matches a verified `dev` commit.
3. A squash PR to `main` with CI and release guards passing.
4. Automatic deployment through the protected `production` environment.

Do not manually rebuild, retag, or deploy a different tree during promotion.

## Security

Do not discuss an unpatched vulnerability in a public issue or pull request. Follow
[`SECURITY.md`](SECURITY.md) for private reporting and coordination.
