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
