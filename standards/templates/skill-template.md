<!--
TEMPLATE — how to add a new standard as a skill.

1. Copy this file to:  standards/skills/<name>/SKILL.md
   (e.g. standards/skills/go/SKILL.md  →  invoked as /standards:go)
2. Fill in the frontmatter `name` + `description` (the description is the
   trigger Claude uses to auto-apply the skill — be specific about WHEN).
3. Replace the body with the actual standard.
4. Bump `version` in standards/.claude-plugin/plugin.json and in the
   marketplace entry, then commit. Users get the update on /plugin update.

This file lives under templates/ (not a recognized plugin component dir),
so it is ignored by the plugin loader — it will not load as a skill.
-->
---
name: <skill-name>
description: <One line. State the standard AND when to apply it — e.g. "Go coding style. Use when writing or reviewing Go code: package layout, error handling, naming, testing.">
---

# <Title> standard

Apply these rules when <situation>.

## <Section>
- rule
- rule

## <Section>
- rule
