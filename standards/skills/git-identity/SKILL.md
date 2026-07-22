---
name: git-identity
description: Set up directory-scoped git identity so all repos under a folder use a different GitHub account (e.g. personal vs work) — commit identity + a separate SSH key. Use when configuring per-directory git user/email, switching GitHub accounts by directory, or wiring a second account's SSH key.
---

# Directory-scoped git identity

Goal: every repo under a chosen directory commits **and** pushes as a different identity than the global default — automatically, with no per-repo config.

## Inputs to gather first
- **Target directory** (e.g. `~/Personal_Workspace`)
- **Identity** for that directory: `name` + `email`
- **Is that host account a SEPARATE account** from the global default? (if yes → it needs its own SSH key)

## Setup

**1. Scoped config file** — create `<dir>/.gitconfig`:
```gitconfig
[user]
    name  = <name>
    email = <email>
```

**2. Conditional include** in the global `~/.gitconfig`, placed AFTER the default `[user]` block so it wins:
```gitconfig
[includeIf "gitdir:<dir>/"]
    path = <dir>/.gitconfig
```
- The **trailing slash** on the `gitdir:` path matters — it matches all repos under the directory recursively.
- `~` expands in `includeIf gitdir:` and in `path`.

**3. Separate account → separate SSH key** (skip if same account):
```bash
mkdir -p <dir>/.ssh && chmod 700 <dir>/.ssh
ssh-keygen -t ed25519 -C "<email>" -f <dir>/.ssh/id_ed25519_<label> -N ""   # -N "" = no passphrase
chmod 600 <dir>/.ssh/id_ed25519_<label>
pbcopy < <dir>/.ssh/id_ed25519_<label>.pub   # then add it in GitHub → Settings → SSH and GPG keys → New SSH key
```
Wire the key into the **scoped** `.gitconfig` (so it only applies under `<dir>`):
```gitconfig
[core]
    sshCommand = ssh -i <dir>/.ssh/id_ed25519_<label> -o IdentitiesOnly=yes
```
Use normal remotes (`git@github.com:<user>/<repo>.git`) — the key, not a host alias, selects the account. `IdentitiesOnly=yes` stops git from offering the default key first.

**4. Signing (optional):** add `signingkey = <id>` under `[user]` in the scoped file.

## Verify — the #1 gotcha

`includeIf "gitdir:…"` fires based on **where the repo's `.git` lives**, not the folder you're standing in. It only activates **inside an actual repo** under the directory.

- ❌ `cd <dir> && git config user.email` in the **bare folder** shows the GLOBAL default — this is expected, *not* a misconfiguration (the folder isn't a repo, so there's no gitdir to match).
- ✅ Inside a repo under `<dir>`: `git config --show-origin user.email` → shows the scoped file and the right email.
- ✅ SSH: `ssh -i <dir>/.ssh/id_ed25519_<label> -o IdentitiesOnly=yes -T git@github.com` → greets the intended username. (`Permission denied (publickey)` just means the public key isn't on that GitHub account yet.)

## Hygiene
- **Never commit the private key.** Keep repos in subdirectories; if the directory root itself ever becomes a repo, add `.ssh/` and `.gitconfig` to `.gitignore`.
- Key file `600`, `.ssh` dir `700`.
