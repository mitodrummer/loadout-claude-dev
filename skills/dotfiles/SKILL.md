---
name: dotfiles
description: Use when changing the user's personal config in a Minimal session (editor, shell, git, Claude Code settings, skills, memories) and the change should outlive the session, or when asked to commit, push, track or untrack a dotfile. The session home is a checkout of the user's dotfiles repo, driven by `dgit`.
---

# Dotfiles in this session

The claude-dev loadout cloned the user's dotfiles repo into this session:

- git dir: `~/$DOTFILES_DIR` (default `~/.dotfiles`), work tree: `$HOME`
- `dgit` is `git --git-dir=<git dir> --work-tree=$HOME` (an alias in
  `~/.bashrc`; in a non-interactive shell, spell it out)
- settings: `~/.config/claude-dev/config` (`DOTFILES_REPO`, `DOTFILES_REF`,
  `DOTFILES_PATHS`)

**The session home is thrown away with the session.** A change to a tracked
file survives only once it is committed and pushed. Say so whenever you edit
one, and offer to commit.

## Common tasks

- What changed: `dgit status` (untracked files are hidden on purpose; ask
  about a path with `dgit status <path>`).
- Is a file tracked: `dgit ls-files --error-unmatch <path>`.
- Save a change: `dgit add <path> && dgit commit -m "<imperative subject>"`,
  then `dgit push`. Follow the repo's own branch rules: if `main` is
  protected, push a branch and open a PR with `gh pr create`.
- Track a new file: `dgit add <path>`. When `DOTFILES_PATHS` is set the
  checkout is sparse; add the path to it as well
  (`dgit sparse-checkout add <pattern>`) and to `DOTFILES_PATHS` in the
  user's host copy of the config, or the next session won't check it out.
- Stop tracking: `dgit rm --cached <path>` keeps the file in this session.
- Get the newest commits: `dgit pull --ff-only`.

## Don'ts

- Never `dgit sparse-checkout disable`, `dgit clean`, or `dgit add -A` /
  `dgit add .`: the work tree is the whole home directory.
- Never commit credentials. Before committing, read the diff
  (`dgit diff --cached`) for tokens, keys and machine-specific paths.
- Never rewrite pushed history (`--amend`, `rebase`) on a shared branch.
