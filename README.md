# opencode config

Global opencode configuration — commands, skills, and settings.

## Install

### Windows

```powershell
cd $HOME\.config\opencode
git init
git remote add origin https://github.com/YOUR_USERNAME/opencode.git
git pull origin main
```

### Linux/macOS

```bash
cd ~/.config/opencode
git init
git remote add origin https://github.com/YOUR_USERNAME/opencode.git
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
command/commit.md           # /commit command
command/document.md         # /document command
skills/document/SKILL.md    # Document processing skill
```

## Restart

After install or update, restart opencode for changes to take effect.
