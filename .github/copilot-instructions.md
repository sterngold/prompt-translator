# GitHub Copilot instructions

## Repository contract

- Read the repository-root `AGENTS.md`, `CLAUDE.md`, `README.md`, and `SECURITY.md` before changing anything. Treat `AGENTS.md` as the canonical repository contract and follow only repository-relative context in cloud work.
- This repository is a public, documentation-only case study. The production Worker, UI, interpreter prompt, telemetry, sessions, user data, and private examples are out of scope and must never be inferred, requested for cloud execution, or committed.
- Stop when required local-only context is unavailable; do not invent it. This includes private implementation details, shared local dotfiles, client context, or source material absent from the checkout.

## Workflow and Git

- Work in an isolated task checkout: use a new worktree for local Copilot work or the provider's isolated sandbox for cloud work. Follow the task-based branch and Conventional Commit formats in `AGENTS.md`; do not use an agent name as the branch prefix.
- Keep the prose diff narrow, preserve the existing case-study voice and public/private boundary, verify all affected links, and stage only intended paths.
- Never push directly to `main`, force-push `main`, merge a pull request, or bypass hooks. Open a pull request and wait for the required `ci` check before merge.
- Resolve every review thread or explain the evidence for rejecting it. Copilot review is advisory and does not replace CI or owner approval.

## Dependencies and security

- This repository has no application dependency manifest. Do not add a top-level dependency, package manifest, lockfile, build system, or lifecycle script without explicit approval from the owner and a repository-local security rationale in the pull request.
- Never commit secrets, credentials, telemetry, user prompts, production code, private validation examples, or generated artifacts.

## Repository commands

There is no application build or unit-test suite. Use the repository's pre-commit lint/hygiene and documentation checks when their tools are already installed:

```bash
gitleaks detect --config .gitleaks.toml
pre-commit run --all-files
git diff --check
```

The assembled-context drift check documented in `AGENTS.md` depends on shared local dotfiles outside this standalone repository. In a cloud sandbox, report that gate as unavailable rather than substituting a path or claiming it passed. GitHub Actions runs commit-message validation and the secret scan in `.github/workflows/ci.yml`; the aggregate required check is `ci`.

## Review priorities

Prioritize silent failures, boundary validation, tests when present (otherwise the documented checks and CI), security, unresolved review threads. Also review accidental private disclosure, unsupported product claims, broken links, and prose that erodes the public/private boundary.
