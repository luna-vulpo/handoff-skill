---
description: Resume work from the newest handoff in session-logs/ (without loading the old session)
---

Use the `session-handoff` skill in **RESUME mode**.

Task:
1. Find the **newest** file with the `kind: handoff` frontmatter in `session-logs/` (if $ARGUMENTS points to a specific topic/path, use it).
2. Read **only that file**. Do not load the history of old sessions or other files until actually needed.
3. Take a light look at the real state (`git status` / `git diff --stat` for code, or `ls` of the working folder for content) to verify the handoff matches reality. Say so explicitly if it does not.
4. If the handoff has a **Suggested skills** section, invoke those skills with the Skill tool before starting work.
5. Summarize in ~3 lines: **where we are → next step → what to avoid**, and wait for confirmation before moving on.

Optional topic/path: $ARGUMENTS
