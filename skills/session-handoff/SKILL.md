---
name: session-handoff
description: Save and resume work across sessions without loading the whole previous conversation. Use when the user wants to "save a handoff", "wrap up the session", "summarize the session to a file", "start from the last handoff", "pick up where we left off", or types /handoff or /pickup. Works in ANY project (code and content); sections adapt to the project. Triggers - handoff, pickup, wrap session, save session, clean session, pass the context, zapisz handoff, czysta sesja, przekaz kontekst, wznow z handoffu.
---

# Session Handoff

Carry context between sessions with the **"document & clear"** method: near the end of a session — while the model still remembers everything sharply — save a **short, curated file**, then start the next, clean session by reading **only that file**, not the whole old conversation.

Why this beats auto-`/compact`: compaction fires when the context is already FULL (the model is dulled) and produces summaries-of-summaries that distort decisions. A handoff is written consciously, earlier, in one selective pass — and it is a plain file you can review and correct.

## Two modes

- **SAVE mode** (`/handoff`): write or update the handoff file for the current session.
- **RESUME mode** (`/pickup`): read the newest handoff and get up to speed without loading history.

## Where files live and how to name them

- Folder: `session-logs/` in the project root (create it if missing).
- Name: `YYYY-MM-DD-slug.md`, where `slug` is 2-4 words describing what the session was about.
- Each thread gets its own file; nothing is overwritten. If **today's file on the same topic** already exists, **update it** instead of creating a second one.
- `session-logs/` may also hold plain logs and brainstorms; handoffs are distinguished by the `kind: handoff` frontmatter (`/pickup` filters on it).
- **Decide once whether `session-logs/` is committed or ignored** — do not leave it to chance. Committed: the team shares context, but every handoff is public repo history forever (see Redaction below). Ignored: private and disposable. If the repo has no policy yet, ask the user once and add the `.gitignore` line or the commit, then stop asking.

## Redaction (mandatory, both modes)

Handoffs land in the project tree and often in git history. Before writing:

- **Never** copy API keys, tokens, passwords, connection strings, private URLs with credentials, or personal data into the file.
- If a secret is load-bearing for the next step, name **where it lives** ("token in `.env.local` as `FOO_TOKEN`"), never its value.
- Redact PII from pasted logs and error output — keep the error, drop the identity.

## Link, do not duplicate

The handoff is a **pointer document**, not an archive. Do not restate content that already lives in a durable artifact — specs, plans, ADRs, issues, commits, diffs, test output. Reference them by path, hash, or URL instead. A handoff that re-explains a committed design doc is both longer and staler than the doc.

Rule of thumb: if a fact survives `git log`, link it. If it exists only in the conversation, write it down.

## File format (adaptive)

The frontmatter header is fixed; the sections adapt to the project. **Skip sections that do not fit**: no branch in a writing project, no "published link" in a code project.

```markdown
---
kind: handoff
date: YYYY-MM-DD
topic: <slug>
status: in-progress | done | blocked
---

# <Header: one sentence on what the session was about>

## Summary
2-4 sentences: what we did and why.

## Key takeaways / decisions
- decision + **why** (this is the know-how that led to the goal)
- ...

## State
- done: ...
- in progress: ...
- blocked / waiting for: ...

## Artifacts
- paths to files we created or changed (link, do not restate)
- [code] branch / PR / commit / failing test
- [content] link to the published piece, draft folder

## Next step
Exactly where to start in the new session (one concrete move).

## Suggested skills
Skills the next agent should invoke with the Skill tool, and why:
- `<skill-name>` - <one-line reason it applies to the next step>
Omit this section when no skill clearly applies. Do not guess names - list only skills you saw available this session.

## What NOT to do
Dead ends and rejected approaches, so the new session does not walk back into them.
```

Adaptation rule: always keep **Summary, Takeaways, State, Next step**; add the rest only when it earns its place.

## When to save (discipline)

- **Do it at ~50-60% context usage, not at 90%.** Earlier means a sharper, better summary.
- Natural moments: closing a thread, before switching tasks, end of the day on a topic.
- **Do not interrupt a working implementation halfway** just because the context meter grew; finish the micro-step, then save.
- Rough thresholds: small windows ~100k tokens; a 200k window is already the risk zone at ~120k; a 1M window ~300-400k. The percentage rule beats the absolute number.

## Persistent memory vs handoff (keep them separate)

- **`session-logs/<...>.md` (handoff)** = the ephemeral state of ONE task. Deletable once the task is closed.
- **Persistent memory** (`MEMORY.md` / Claude Code memory) = durable facts that outlive the task — preferences, standing project decisions, references.
- During `/handoff`: if a **durable fact** surfaced this session (not just task state), offer to add it to persistent memory, not to the handoff.

## SAVE mode (/handoff): what to do

1. Check whether **today's** handoff on the same topic already exists in `session-logs/` — if yes, update it rather than creating a second file.
2. Write the file per the format above: adaptive sections, redaction applied, artifacts linked not restated.
3. If a durable fact came up, offer to add it to persistent memory.
4. Report the path you wrote, in one line.

## RESUME mode (/pickup): what to do

1. Find the **newest** file with `kind: handoff` in `session-logs/` (or the topic/path the user pointed to).
2. Read **only that file**. Do not load the history of old sessions or neighbouring files until actually needed.
3. Take a light look at reality: `git status` / `git diff --stat` (code) or `ls` of the working folder (content), to verify the handoff still matches the real state. **Say so explicitly if it does not** — a stale handoff is worse than none.
4. If the handoff lists **Suggested skills**, invoke them with the Skill tool before starting work.
5. Summarize in ~3 lines: **where we are -> next step -> what to avoid**. Wait for confirmation before moving on.

## Emergency: when auto-compact caught you anyway

If the context got compacted before you saved a handoff, salvage on these rules:
- **KEEP**: current goal, changed files, decisions, errors/tests, next step.
- **SUMMARIZE**: exploration, debugging paths, general discussion.
- **DROP**: repetitive logs, dead ends (but record them under "What NOT to do").

Then immediately run `/handoff` to persist it to disk.

---

Derived from [simplybychris/handoff-skill](https://github.com/simplybychris/handoff-skill) (MIT, detechtive.pl) with the suggested-skills handoff, redaction rule, and link-do-not-duplicate rule adapted from [mattpocock/skills](https://github.com/mattpocock/skills) `productivity/handoff` (MIT, Matt Pocock).
