---
description: Show current Wiggum loop status for this repo
---

You are reporting the current status of the Wiggum loop for the user's repository.

## What to read (if present)
- `.wiggum/IMPLEMENTATION_PLAN.md`
- `.wiggum/TEST_PLAN.md`
- `.wiggum/config.json`
- newest `.wiggum/runs/*/summary.log` (if any)

## Output requirements
Be concise and structured.

1) Scaffold status
- If `.wiggum/` does not exist: say it is not initialized and recommend running the init command.

2) Plan summary
- Summarize the top 1-3 remaining items from `.wiggum/IMPLEMENTATION_PLAN.md`.
- If the plan is empty, say so and recommend running a planning pass.

3) Test coverage summary
- Summarize what’s covered by automated tests vs what manual checks remain (from `.wiggum/TEST_PLAN.md`).

4) Last run summary (if available)
- Mention the most recent run folder timestamp.
- Whether completion promise was detected.
- Any obvious failures (brief).

5) Next action recommendation
Recommend exactly one of these (based on what you found):
- `bash .wiggum/loop.sh plan 1`
- `bash .wiggum/loop.sh build`
- `bash .wiggum/loop.sh verify`

Do not modify any files.
