---
name: commit
description: Recommend a commit message, then commit. Use when the user wants to commit changes, write or improve a commit message, or just says "commit" — proposes Conventional Commits candidates, lets them pick or edit, and commits only after explicit confirmation.
---

# Commit helper

A recommend → pick/edit → confirm → commit flow. The message follows **Conventional Commits**. **Never commits or pushes without explicit confirmation.**

## Flow

1. **Inspect what will be committed.**
   - Run `git status --short` and `git diff --staged`.
   - If **nothing is staged**, show the unstaged/untracked changes and **ask what to include**, then stage exactly those with `git add <paths>`. Never run `git add -A` (or `.`) without asking.

2. **Propose 3 candidate messages** in Conventional Commits format:
   - **A — concise:** subject line only.
   - **B — standard:** subject + a short body explaining the *why*.
   - **C — detailed:** subject + body with bullets when several distinct changes are bundled.
   Base them on the actual diff, not guesses.

3. **Let the user pick or revise.** Accept "use A", "make it a fix", "shorten the subject", "mention the migration", etc. Revise and re-show. Loop until approved.

4. **Confirm, then commit.** Show the final message + the exact file list and wait for an explicit yes. Then:
   - `git commit -m "<subject>"` (add a second `-m "<body>"` only if there's a body).
   - **Do NOT add a `Co-Authored-By` trailer.**
   - Don't override the repo's configured `user.name` / `user.email`.

5. **Push is separate and opt-in.** After committing, offer to push. Only run `git push` on an explicit yes. If the branch has no upstream, use `git push -u origin <branch>` and say so first.

## Conventional Commits reference

`type(optional-scope): subject`

- **Subject:** imperative mood ("add", not "added"), ~50 chars (max 72), no trailing period.
- **Types:** `feat` (new feature), `fix` (bug fix), `docs`, `style` (formatting), `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- **Body (optional):** blank line after subject, wrap ~72 cols, explain *why* not *how*.
- **Breaking change:** add `!` after type/scope (`feat!:`) or a `BREAKING CHANGE:` footer.

Examples:
```
feat(auth): add phone-number login
fix: guard against null waypoint on first sync
refactor(account): extract profile fields into their own view
chore: bump standards plugin to 0.3.0
```

## Guardrails
- The confirm-before-commit gate is non-negotiable — always show the message first.
- Commit only what the user intends; when in doubt about scope, ask.
