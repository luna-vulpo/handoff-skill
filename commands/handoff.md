---
description: Save a handoff of the current session to session-logs/ (so you can resume in a clean window)
---

Use the `session-handoff` skill in **SAVE mode**.

Task: write or update the handoff file for the current session in `session-logs/`, following the format and rules from the skill (frontmatter `kind: handoff`, name `YYYY-MM-DD-slug.md`, adaptive sections, skip the irrelevant ones).

Before creating a new file: check whether **today's** handoff file on the same topic already exists. If it does, update it instead of creating a second one.

Apply the skill's **redaction** rule — no keys, tokens, passwords, or PII in the file; name where a secret lives, never its value.

**Link, do not duplicate**: reference specs, plans, ADRs, issues, commits, and diffs by path or URL rather than restating them.

Fill the **Suggested skills** section with skills the next agent should invoke, and why. Omit the section if none clearly applies; never invent skill names.

If a durable fact came up in the session (not just task state), offer to add it to persistent memory instead of the handoff.

Optional topic/slug from the user: $ARGUMENTS
