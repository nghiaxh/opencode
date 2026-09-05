# Opencode config

Global opencode configuration for commands, skills, and settings.

## Install

```bash
cd ~/.config/opencode
git init
git remote add origin https://github.com/nghiaxh/opencode.git
git fetch origin
git reset --hard origin/main
```

## Update

Edit config directly at `~/.config/opencode/`, then commit and push:

```powershell
cd $HOME\.config\opencode
git add -A
git commit -m "chore: update config"
git push
```

## Structure

```
opencode.jsonc              # Main config
AGENTS.md                   # Global agent instructions
LAZY.md                     # Lazy dev mode instructions (loaded always-on)
command/lazy-commit.md      # /lazy-commit command
command/lazy-audit.md       # /lazy-audit command
command/lazy-review.md      # /lazy-review command
skills/                     # Skills (SKILL.md per skill)
```

## Restart

After install or update, restart opencode for changes to take effect.
