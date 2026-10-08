# SkillScout skill

A Claude Code skill that finds AI tools, skills, plugins and MCP servers that fit how you work.

It reads your Claude Code setup (CLAUDE.md files, memory, installed skills and MCP servers), drafts a profile of you, and shows it to you first. Nothing leaves your machine until you say yes. Then it scans 44 free sources and ranks what fits, with a reason and an install command for each pick.

Not on Claude Code? Use the website instead, with any AI: https://skillscout.si

## Install

macOS / Linux:

```bash
git clone https://github.com/sauronnitin/skillscout-skill ~/.claude/skills/skillscout
```

Windows (PowerShell):

```powershell
git clone https://github.com/sauronnitin/skillscout-skill "$HOME\.claude\skills\skillscout"
```

Then in Claude Code, type `/skillscout` or ask "what AI tools should I install?"
