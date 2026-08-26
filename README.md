# handoff-skill (merged)

`/handoff` + `/pickup` for Claude Code: instead of letting `/compact` decide what the model
remembers, you save a short curated note deliberately and start the next session with a clean
window, reading only that note.

The method matches what Anthropic calls **structured note-taking** in
[Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents):
persistent notes kept outside the context window and pulled back in later.

## How it works

1. The session gets heavy (~50-60% of the context meter) — you type **`/handoff`**. The agent writes
   `session-logs/YYYY-MM-DD-topic.md`: summary, decisions, state, artifacts, next step,
   **suggested skills**, and **what NOT to do**.
2. You clear the window (**`/clear`**) or open a new terminal.
3. In the fresh session you type **`/pickup`**. The agent reads **only the newest handoff**, verifies
   reality (`git status` / `ls`), invokes the suggested skills, and summarizes in three lines:
   where we are → next step → what to avoid.

## Provenance

This is a merge of two upstream skills, both MIT:

- **[simplybychris/handoff-skill](https://github.com/simplybychris/handoff-skill)** (MIT, detechtive.pl)
  — the base: two-way SAVE/RESUME design, the fixed file format and `kind: handoff` frontmatter,
  the reality-check step, "What NOT to do", the save-early discipline, the handoff-vs-persistent-memory
  split, and the post-compact emergency procedure.
- **[mattpocock/skills](https://github.com/mattpocock/skills)** `productivity/handoff` (MIT, Matt Pocock)
  — three additions grafted in: the **Suggested skills** section naming what the next agent should
  invoke, the **redaction** rule for secrets and PII, and **link-do-not-duplicate** for artifacts.

Added on top of both: an explicit decision point on whether `session-logs/` is committed or
gitignored (it holds session notes in the repo tree, so this should not be accidental), and
slightly narrowed trigger phrases so the skill does not fire on generic "continue work" / "resume".

## Installation

```bash
git clone https://github.com/luna-vulpo/handoff-skill.git
cd handoff-skill

mkdir -p ~/.claude/skills ~/.claude/commands
cp -R skills/session-handoff ~/.claude/skills/
cp commands/handoff.md commands/pickup.md ~/.claude/commands/
```

From now on `/handoff [optional-topic]` and `/pickup [optional-topic]` work in every project.

Prefer symlinks, so a `git pull` updates the installed copy in place:

```bash
ln -s "$PWD/skills/session-handoff" ~/.claude/skills/session-handoff
ln -s "$PWD/commands/handoff.md"    ~/.claude/commands/handoff.md
ln -s "$PWD/commands/pickup.md"     ~/.claude/commands/pickup.md
```

Skills and commands are loaded at session start, so open a new Claude Code session afterwards.

## When to hand off

- Universal rule: **~50-60% of the context meter, not 90%.**
- Rough absolutes: small windows ~100k tokens; a 200k window is risky by ~120k; a 1M window ~300-400k.
- Do not interrupt a working implementation mid-step just because the meter grew — finish the
  micro-step, then save.

Research ("Lost in the Middle"; Chroma's 18-model context-rot test) shows that the longer the
context, the more often a model loses information from the middle of the conversation.

## Structure

```
handoff-skill/
├── skills/session-handoff/SKILL.md   # the skill (mechanics, file format, rules)
├── commands/handoff.md               # /handoff, SAVE mode
└── commands/pickup.md                # /pickup, RESUME mode
```

## License

MIT — see LICENSE. Retains the copyright notices of both upstream projects.
