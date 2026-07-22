# engineering-standards

A Claude Code **plugin marketplace** holding shared, stack-agnostic engineering standards as **auto-applying skills**. Install once; Claude then applies the relevant standard automatically across every project (e.g. pulls in the Postgres rules whenever you design a schema), and you can invoke any of them manually.

## What's inside

```
engineering-standards/                  ← this repo = the marketplace
├── .claude-plugin/marketplace.json      ← the catalog
└── standards/                           ← one plugin, many skills
    ├── .claude-plugin/plugin.json
    ├── skills/
    │   └── postgres/SKILL.md            ← /standards:postgres
    └── templates/skill-template.md      ← copy to add a new standard
```

Current skills: **postgres** (schema & naming). Planned: `go`, `api-design`, `git-workflow`.

## Install (teammates)

```
/plugin marketplace add <owner>/engineering-standards   # or a full git URL
/plugin install standards@engineering-standards
/reload-plugins
```

After that, in any project: skills auto-apply by context, and you can run `/standards:postgres` to print a standard on demand.

## Local development / testing

```
claude --plugin-dir ./standards      # load without installing
/reload-plugins                      # pick up edits in the same session
claude plugin validate               # validate manifests before committing
```

## Add a new standard

1. `cp standards/templates/skill-template.md standards/skills/<name>/SKILL.md`
2. Fill in the frontmatter `name` + `description` (the description is the auto-apply trigger — be specific about *when* to use it) and the body.
3. Bump `version` in `standards/.claude-plugin/plugin.json` **and** the marketplace entry in `.claude-plugin/marketplace.json`.
4. Commit and push — teammates get it via `/plugin update`.

## Versioning

Set an explicit `version`; users only receive updates when it's bumped. (If omitted, the git commit SHA is used and every commit counts as a new version.)
