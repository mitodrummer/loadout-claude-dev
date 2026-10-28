# loadout-claude-dev

A [Minimal](https://minimal.dev) loadout for coding with Claude Code. Attach
to a session and you get a zellij layout: Claude Code on the left, a shell on
the right. Vim, fzf, ripgrep, fd and bat come with it. Claude Code is the
version Minimal's catalog pins, like every other package in the session.

Bring your own dotfiles: point it at a git repo and every session starts with
your config checked out at the newest commit, and a `dgit` command to commit
and push changes you make in the session. Claude gets a `dotfiles` skill so it
knows how.

## Install

Needs min 0.6.0 or later (`min --version`).

```sh
git clone https://github.com/mitodrummer/loadout-claude-dev \
  ~/.config/minimal/loadouts/claude-dev
~/.config/minimal/loadouts/claude-dev/setup
```

`setup` writes your settings to `local/config` (gitignored), asks for your
dotfiles repo, and adds the loadout to your `default_loadouts`. Then, in any
project:

```sh
min session activate --attach
```

At attach you sign in to GitHub (skipped when `GH_TOKEN` is set), and commits
use that account's name and email unless your dotfiles or `GIT_USER_*` set
them.

The directory name is the loadout's name, so keep the clone target. The
loadout copies its own files into the session, passes through the variables
below, and runs two activation hooks; read `loadout.toml` and `bin/` before
installing, they're short.

## Settings

`local/config` (from [`config.example`](config.example)):

| Setting | Default | |
| --- | --- | --- |
| `DOTFILES_REPO` | none | `owner/name` or a clone URL. Unset: no dotfiles. |
| `DOTFILES_DIR` | `.dotfiles` | Where the git dir lives in the session home. |
| `DOTFILES_REF` | default branch | Branch to check out. |
| `DOTFILES_PATHS` | everything | Sparse-checkout patterns, space-separated. |
| `CLAUDE_ARGS` | none | Extra arguments for claude. |
| `SHELL_RCFILE` | `~/.bashrc` | The shell pane's rcfile, relative to the project. |
| `GIT_USER_NAME`, `GIT_USER_EMAIL` | dotfiles, else GitHub | Your git identity for commits. |

From your host shell, each optional: `GH_TOKEN` (GitHub, and private dotfiles
repos; otherwise you sign in at attach), `CLAUDE_CODE_OAUTH_TOKEN` or
`ANTHROPIC_API_KEY` (otherwise Claude asks you to log in).

## Dotfiles

The repo is cloned with its git dir at `~/$DOTFILES_DIR` and its work tree at
`$HOME`, the usual bare-repo dotfiles layout. Your tracked files replace the
loadout's defaults, so a tracked `.vimrc` wins over the one here. Edits made
in a session last only as long as the session: commit and push them with
`dgit`. A clone failure is a warning, not a failed activation.

## Using it

| | Shell (fzf) | | Vim (`<Space>` is the leader) |
| --- | --- | --- | --- |
| Ctrl-R | search history | `<Space>f` | find a file |
| Ctrl-T | insert a file path | `<Space>g` | ripgrep the repo |
| Alt-C | cd into a directory | `<Space>w` | ripgrep the word under the cursor |
| `**<Tab>` | fuzzy-complete a path | `<Space>b` | switch buffer |

Attaching again rejoins the layout; `zellij delete-session --force claude-dev`
resets it. Don't combine this with another loadout that sets `PROMPT_COMMAND`.

## Files

| File | Purpose |
| --- | --- |
| `loadout.toml` | Packages, patches, hooks and variables |
| `setup` | Writes `local/config` and adds the loadout to your defaults |
| `bin/claude-dev-session` | At attach: GitHub sign-in and git identity, then the zellij layout |
| `bin/dotfiles-sync` | Clones `DOTFILES_REPO` at activation |
| `skills/dotfiles/` | Claude's instructions for `dgit` |
| `home/` | `.bashrc`, `.vimrc`, and fzf's vim plugin (MIT, junegunn/fzf) |
