---
description: Scaffold .wiggum/ loop files into the current repo (strict monorepo defaults)
---

You are scaffolding a Ralph/Wiggum-style loop into the user's current repository.

## Requirements
- Create a `.wiggum/` folder at the repo root.
- Put all loop artifacts under `.wiggum/`.
- Do not modify application code. Only create/update `.wiggum/*`.
- If files already exist, update them conservatively (do not delete user content).

## Default assumptions
Assume this repo is **usually a monorepo** with:
- backend: Python (Poetry) + pytest
- frontend: Node (pnpm) with lint/build

However, you MUST auto-detect and write defaults based on the actual repo:
- If `pyproject.toml` exists: treat backend as present.
- If `poetry.lock` exists: use poetry commands.
- If `pnpm-lock.yaml` exists: treat frontend as present.
- If neither backend nor frontend markers exist, scaffold config with placeholders.

## Create or update these files

### 1) `.wiggum/AGENTS.md`
Write a short operational file with sections for:
- how to run tests
- how to lint/typecheck (if applicable)
- how to run the app

Include both backend and frontend placeholders.

### 2) `.wiggum/config.json`
Create a small, editable config that the runner and prompts can rely on.

It MUST include:
- a completion promise string (exact literal)
- backend verify commands (array of strings)
- frontend verify commands (array of strings)
- an execution engine configuration (Claude vs Codex) so the loop can run on a laptop OR a VPS
- a boolean (or string policy) indicating that some manual verification may remain, but automated tests are preferred

Engine fields (portable runner):
- `engine.name`: one of `claude` or `codex`
- `engine.model`: optional model identifier (e.g., `gpt-5.2`)
- `engine.command`: an array of strings representing the command to run the agent in non-interactive mode
  - The runner will append the prompt file via stdin (recommended), so the command should read from stdin.

Auto-detect and populate sensible defaults:
- backend (poetry + pytest):
  - `poetry run pytest`
- backend (no poetry.lock but pyproject exists):
  - `python -m pytest`
- frontend (pnpm):
  - `pnpm lint`
  - `pnpm build`

Engine defaults:
- If `claude` is available, default to:
  - name: `claude`
  - command: `["claude","-p"]`
- If `codex` is available (e.g. on a VPS), default to:
  - name: `codex`
  - model: `gpt-5.2`
  - command: a placeholder that the user may need to adjust, e.g. `["codex", "--model", "gpt-5.2"]`

If you cannot confirm the exact Codex flags in this repo, write a safe placeholder and clearly label it as user-editable.

### 3) `.wiggum/IMPLEMENTATION_PLAN.md`
Initialize as an empty plan with a heading and a short instruction that it is disposable and should be kept current.

### 4) `.wiggum/TEST_PLAN.md`
Create a template that the agent will keep updated. It should include:
- “Automated tests” section (expected to grow)
- “Manual checks (remaining)” section (allowed to be non-empty, but should be short and justified)

### 5) `.wiggum/specs/00_overview.md`
Create a starter spec that explains how specs should be written:
- JTBD style
- “one sentence without ‘and’”
- each concern gets its own spec file

### 6) `.wiggum/PROMPT_plan.md`
Write a planning prompt that:
- studies `.wiggum/specs/*`
- studies relevant repo code (do not assume missing)
- updates ONLY:
  - `.wiggum/IMPLEMENTATION_PLAN.md`
  - `.wiggum/TEST_PLAN.md` (update planned tests/checks)
- does not implement any code
- uses `.wiggum/AGENTS.md` and `.wiggum/config.json` as operational context

### 7) `.wiggum/PROMPT_build.md`
Write a building prompt that:
- studies `.wiggum/specs/*`, `.wiggum/IMPLEMENTATION_PLAN.md`, and `.wiggum/TEST_PLAN.md`
- chooses the single most important incomplete plan item
- searches code before changing anything ("don't assume not implemented")
- implements the task
- adds/updates AUTOMATED tests for the new behavior whenever feasible
- updates `.wiggum/IMPLEMENTATION_PLAN.md` and `.wiggum/TEST_PLAN.md` immediately with learnings

Do NOT automatically push to git remotes.
Include a short reminder about sandboxing/safety when granting tool permissions.

### 8) `.wiggum/PROMPT_verify.md`
Write a verification prompt that:
- reads `.wiggum/config.json` for verify commands
- runs backend verify commands if configured
- runs frontend verify commands if configured
- checks that automated tests exist for the core feature behavior (not only a plan)
- allows a small “Manual checks (remaining)” list in `.wiggum/TEST_PLAN.md`, but only if justified

When ALL verify commands pass AND verification is acceptable:
- output exactly the completion promise string from `.wiggum/config.json`

Otherwise:
- do NOT output the promise
- update `.wiggum/TEST_PLAN.md` and/or `.wiggum/IMPLEMENTATION_PLAN.md` with what failed and what to do next

Note: PROMPT files should be engine-agnostic (no references to Claude-specific flags). The runner chooses Claude vs Codex.

### 9) `.wiggum/loop.sh`
Create an executable bash script that:
- accepts `plan`, `build`, or `verify` and an optional max-iterations number
- is PORTABLE: it must not hardcode Claude
  - read `.wiggum/config.json` to decide which engine to run (Claude vs Codex)
  - execute the configured engine command (array) and feed the prompt file via stdin
- writes logs under `.wiggum/runs/<timestamp>/iter-###/<phase>.log`
- after each iteration, checks logs for the completion promise and stops if found
- prints a short usage message

If you need to parse JSON in bash, use a tiny embedded python snippet (preferred) rather than adding a dependency on `jq`.

Do NOT default to `--dangerously-skip-permissions`. If you include it, make it opt-in via an env var.

## After scaffolding
Print:
- which files were created/updated
- the exact commands the user should run next, e.g.:
  - `bash .wiggum/loop.sh plan 1`
  - `bash .wiggum/loop.sh build`
  - `bash .wiggum/loop.sh verify`

Be concise.
