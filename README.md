# Opencode config

Global opencode configuration for commands, skills, and settings.

## Install

### Windows

```powershell
cd $HOME\.config\opencode
git init
git remote add origin https://github.com/nghiaxh/opencode.git
git pull origin main
```

### Linux/macOS

```bash
cd ~/.config/opencode
git init
git remote add origin https://github.com/nghiaxh/opencode.git
git pull origin main
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
command/commit.md           # /commit command
skills/                     # Skills (SKILL.md per skill)
```

## Restart

After install or update, restart opencode for changes to take effect.
