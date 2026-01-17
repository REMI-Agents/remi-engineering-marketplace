---
description: Scaffold .wiggum/ loop files into the current repo
---

You are scaffolding a Ralph/Wiggum-style loop into the user's current git repository.

## Requirements
- Create a `.wiggum/` folder at the repo root.
- Put all loop artifacts under `.wiggum/`.
- Do not modify application code. Only create/update `.wiggum/*`.
- If files already exist, update them conservatively (do not delete user content).

## Create or update these files

### 1) `.wiggum/AGENTS.md`
Write a short operational file with sections for:
- how to run tests
- how to lint/typecheck (if applicable)
- how to run the app
Leave placeholders the user can edit.

### 2) `.wiggum/IMPLEMENTATION_PLAN.md`
Initialize as an empty plan with a heading and a short instruction that it is disposable and should be kept current.

### 3) `.wiggum/specs/00_overview.md`
Create a starter spec that explains how specs should be written (JTBD, "one sentence without 'and'"), and that each concern gets its own spec file.

### 4) `.wiggum/PROMPT_plan.md`
Write a planning prompt that:
- studies `.wiggum/specs/*`
- studies relevant repo code (do not assume missing)
- updates only `.wiggum/IMPLEMENTATION_PLAN.md`
- does not implement any code
- uses `.wiggum/AGENTS.md` as operational context

### 5) `.wiggum/PROMPT_build.md`
Write a building prompt that:
- studies `.wiggum/specs/*` and `.wiggum/IMPLEMENTATION_PLAN.md`
- chooses the single most important plan item
- searches code before changing anything ("don't assume not implemented")
- implements the task
- runs appropriate tests (use `.wiggum/AGENTS.md` for exact commands)
- updates `.wiggum/IMPLEMENTATION_PLAN.md` immediately with learnings
- commits and pushes when tests pass
- includes an explicit reminder about sandboxing when using `--dangerously-skip-permissions`

### 6) `.wiggum/loop.sh`
Create an executable bash script that:
- accepts `plan` or `build` and optional max iterations
- runs `claude -p` with the corresponding `.wiggum/PROMPT_*.md`
- defaults to using `--dangerously-skip-permissions`
- runs `git push` after each iteration
- prints a short usage message

## After scaffolding
Print:
- which files were created/updated
- the exact commands the user should run next, e.g.:
  - `bash .wiggum/loop.sh plan 1`
  - `bash .wiggum/loop.sh build`

Be concise.
