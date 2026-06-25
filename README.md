# skills

A collection of Claude skills that automatically syncs to `~/.claude/skills` on every `git pull`.

## Setup

Clone the repository and run the one-time setup command to configure the git hook:

```bash
git clone <repo-url> && cd skills
git config core.hooksPath .hooks
```

After that, every `git pull` will copy the latest skills to `~/.claude/skills` automatically.

## How it works

The `.hooks/post-merge` hook runs after each successful `git pull` (merge) and copies all non-hidden files from the repository root into `~/.claude/skills/`, creating the directory if it does not exist.
