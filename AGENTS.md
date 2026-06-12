# AGENTS.md

Conventions for **every** human and AI agent working in this repo.
Model-agnostic by design — read once, applies whether you're Claude Code, Codex, Cursor, Aider, Gemini, Continue, Cline, or a human.

This file is the **single source of truth** for repo conventions.
`CLAUDE.md`, `.cursorrules`, `.aider.conf.yml` are pointers — do not duplicate content into them.

<!-- ASSEMBLED — DO NOT EDIT AGENTS.md DIRECTLY.
     Per-repo header (§1–2) = this repo's AGENTS.header.md (hand-edited, repo-specific).
     Shared canon (§3–13)  = anders-dotfiles/context-sync/agents-canon.md (ONE source, all repos).
     Regenerate:  bash <dotfiles>/context-sync/assemble-agents.sh <repo-dir>
     Drift-check: bash <dotfiles>/context-sync/assemble-agents.sh --check <repo-dir>  (0 ok · 1 stale · 2 err) -->

---

## 1. Repo identity

- **Repo:** `prompt-translator`
- **Purpose:** Public **product case study** for PromptTranslator — a simultaneous-interpretation-inspired prompt optimizer (chunking, anticipation, compression, reformulation). This repo is documentation only: the production Worker, UI, interpreter prompt, telemetry, and user data stay private.
- **Owner:** @sterngold
- **Status:** active — public case study, low churn.
- **Stack:** Markdown only (`README.md` + `docs/architecture.md` + `docs/validation.md`). No application code, no build.

---

## 2. Build, test, lint

Documentation repo — **nothing to build or test**. Quality gates are the secret scan and the assembled-context check:

```bash
# Verify this repo's AGENTS.md is in sync with its header + the shared canon
bash ~/anders-dotfiles/context-sync/assemble-agents.sh --check .   # 0 ok · 1 stale · 2 error

gitleaks detect --config .gitleaks.toml   # secret scan (also runs in CI + pre-commit)
pre-commit run --all-files                # file hygiene (after `pre-commit install`)
```

**Never commit** anything from the private production side (Worker code, interpreter prompt, telemetry, user data) — this repo's whole premise is the public/private split.

**Agents:** edits here are prose edits. Match the existing case-study voice; keep the public/private boundary intact.

---

## 3. Commit messages — Conventional Commits 1.0

Format: `<type>(<scope>): <subject>`

| Type | When |
|---|---|
| `feat` | New user-facing capability |
| `fix` | Bug fix |
| `refactor` | Code change, no behaviour change |
| `perf` | Performance |
| `docs` | Docs only |
| `test` | Tests only |
| `chore` | Tooling, deps, config |
| `ci` | CI/CD only |
| `revert` | Revert prior commit |

**Scope** = ticket ID when available (Linear/Jira/GitHub issue).

✅ `feat(AND-1146): add prompt route normalization`
✅ `fix: handle empty payload in /api/chat`
✅ `chore(deps): bump ruff to 0.6.9`
❌ `update stuff`
❌ `WIP`
❌ `clip: Staff Engineer. (retry)`

Breaking changes: append `!` and add `BREAKING CHANGE:` footer.
`feat(api)!: drop /v1 endpoints`

---

## 4. Branch naming

Format: `<type>/<TICKET>-<kebab-slug>`

`<type>` = same as commit types (`feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ci`).
`<TICKET>` = ticket ID in UPPER-CASE, or omit if no ticket.
`<kebab-slug>` ≤ 50 chars, lowercase, hyphens.

✅ `feat/AND-1146-prompt-normalize`
✅ `fix/AND-1150-empty-payload-crash`
✅ `chore/bump-ruff`
❌ `vsterngold/and-1146` (no username prefix)
❌ `chore/169ea4-anders-config-env` (no commit hashes)
❌ `codex/foo` (no agent-name prefix — agent identity is in commit trailer, not branch)

**Agent attribution** lives in commit trailers, not branch names:
```
Co-authored-by: Claude <noreply@anthropic.com>
```

---

## 5. Pull requests

- **All changes** to `main` go through a PR. No direct pushes.
- PR title MUST follow Conventional Commits (CI enforces).
- PR description MUST fill the template (`.github/pull_request_template.md`).
- **Squash-merge only.** Linear history required.
- Required passing checks: `lint`, `test`, `gitleaks`, `commitlint`.
- Solo flow: 0 required human reviewers. CodeRabbit / Copilot Review = required reviewer.

---

## 6. Versioning & releases

- **SemVer 2.0.** `MAJOR.MINOR.PATCH`.
- Releases managed by [release-please](https://github.com/googleapis/release-please) — opens a release PR that bumps version + updates `CHANGELOG.md` from Conventional Commits.
- Tags: `v<MAJOR>.<MINOR>.<PATCH>` (e.g. `v1.4.2`).
- Pre-1.0 repos: breaking changes allowed in `MINOR` per SemVer §4.

---

## 7. Secrets & sensitive data

- **Never** commit secrets, API keys, tokens, `.env` files, credentials.
- `gitleaks` runs pre-commit AND in CI. Both must pass.
- `.env` is gitignored. Use `.env.example` for templates.
- For vault repos (medical, financial, personal): hybrid pattern — text tracked, blobs in `.gitignore` under `vault/blobs/`.
- If a secret leaks: rotate first, then `git filter-repo` to scrub history, then force-push (one of the few times force-push is allowed — to a non-protected branch).

---

## 8. Signed commits

All commits MUST be signed (SSH or GPG). CI verifies. Setup:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```
Then add the same SSH key as a **Signing Key** in GitHub → Settings → SSH and GPG keys.

---

## 9. Code style

- `.editorconfig` is canonical for indent, EOL, charset, final newline.
- Language-specific formatters configured per repo (ruff/black for Python, prettier for JS/TS, swift-format for Swift).
- Pre-commit runs them. Do not bypass with `--no-verify` unless you are unblocking a hot fix and will follow up with a `chore: re-apply formatter` PR.

---

## 10. Dependencies

- **Python:** `uv` for env + lockfile. `pyproject.toml` is source of truth.
- **JS/TS:** `npm` or `pnpm`. Lockfile committed.
- **Swift:** SwiftPM. `Package.resolved` committed.
- Dependabot runs weekly, groups patch + minor PRs.

---

## 11. Documentation expectations

Repos must contain:
- `README.md` — what it is, how to run it, how to test it
- `AGENTS.md` — this file
- `CHANGELOG.md` — auto-maintained by release-please
- `docs/` — design notes, ADRs (Architecture Decision Records) for non-trivial choices

ADR format: `docs/adr/NNNN-short-title.md`. One per decision. Date + context + decision + consequences.

---

## 12. Working with AI agents in this repo

**For the agent reading this:** these rules apply to YOU.

- Read this file in full before making changes.
- Follow Section 3 (commit format) and Section 4 (branch naming) exactly.
- Run `make lint && make test` before opening a PR. If they fail, fix or stop.
- Never commit secrets. Never bypass pre-commit hooks.
- Sign commits if possible; otherwise note in PR description so the human can amend.
- Add yourself as co-author trailer.
- If this file is unclear or contradicts another instruction, ask in the PR description rather than guessing.

**For the human:** treat AI commits the same as human commits — they pass the same gates or they don't merge.

---

## 13. Anti-patterns (don't do this)

| ❌ | ✅ |
|---|---|
| Force-push to `main` | Open a PR. Force-push only on your own feature branch. |
| `git commit --no-verify` | Fix the hook violation. |
| Direct commit to `main` | PR + squash-merge. |
| `update README` as a commit | `docs: clarify install steps` |
| Branch named after yourself or your tool | Branch named after the work (`feat/AND-1234-…`) |
| Storing secrets in `config.py` "just for now" | `.env` + `python-decouple` / `os.getenv`. |
| Manual CHANGELOG edits | release-please owns CHANGELOG. |
