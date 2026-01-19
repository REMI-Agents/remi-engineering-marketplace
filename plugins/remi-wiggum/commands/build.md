---
description: Implement the top plan item, add tests, update plan/test plan (no auto-push)
---

Study `.wiggum/specs/*`, `.wiggum/IMPLEMENTATION_PLAN.md`, and `.wiggum/TEST_PLAN.md`. Pick the single most important incomplete item.

Before making changes, search the codebase ("don't assume not implemented"). Implement the item completely.

Testing expectations:
- Prefer adding/updating AUTOMATED tests for the new behavior.
- Run the appropriate baseline commands per `.wiggum/AGENTS.md` (and/or `.wiggum/config.json`) as a sanity check.
- Keep `.wiggum/TEST_PLAN.md` honest: move items from “planned” to “covered” only when tests exist or checks have been performed.

Always update:
- `.wiggum/IMPLEMENTATION_PLAN.md` with progress/learnings
- `.wiggum/TEST_PLAN.md` with what’s now covered and what remains

Do NOT automatically push to git remotes.
If you create a commit, it should be local-only unless the user explicitly asks to push.

Portability note: Do not assume a specific agent CLI. This loop may run under Claude CLI or Codex CLI depending on `.wiggum/config.json`.
