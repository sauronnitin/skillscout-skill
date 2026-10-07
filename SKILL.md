---
name: skillscout
description: Recommends AI skills, MCP servers, plugins and tools that fit how the user actually works. Use when the user asks what skills, MCP servers or AI tools they should install, wants to keep their AI setup current, or says "skillscout" / "/skillscout".
---

# SkillScout

Builds a profile of the user from what this agent can already see, then scans the live web for AI tools, skills, MCP servers and plugins that fit it, and ranks them with a reason for each.

The profile has the same shape as the one the SkillScout website (https://skillscout-eight.vercel.app) asks any AI for, so a profile made here also works there, and the other way round.

## 1. Build the profile

Read what is available across the user's whole setup, not only the current project. Skip anything that does not exist; never ask the user to create files for this. Read only.

- `~/.claude/CLAUDE.md`, and `CLAUDE.md` / `AGENTS.md` / `.cursorrules` in the current project and its parents
- every project they have used Claude Code in: the folders in `~/.claude/projects` (each name is a path); for the 15 most recent, their `CLAUDE.md` / `AGENTS.md` and the start of the README, noting what each is for
- their past prompts: the last 500 lines of `~/.claude/history.jsonl` (one prompt per line, with the project it was typed in); look for repeated requests and work that is not coding
- memory files (`~/.claude/projects/*/memory/*.md`, `~/.claude/memory/*.md`)
- installed skills (`~/.claude/skills/*/SKILL.md` names), agents, commands and plugins (`~/.claude/plugins/installed_plugins.json`)
- MCP servers from `claude mcp list`, `~/.claude.json`, `.mcp.json` or `settings.json` (names only)
- other AI tools on the machine: `~/.codex`, `~/.cursor`, `~/.gemini` (skill, rule and MCP server names only)
- `package.json`, `pyproject.toml`, `requirements.txt`, `go.mod` and the README of the current project
- what you know from this conversation

Count each project toward the area of work or life it serves (a job tracker is job search, a portfolio site is design), so one project does not fill the whole profile.

Rules:
- Only use what you actually saw. Do not guess. Leave a list empty if you don't know.
- Never include secrets, API keys, tokens, passwords, emails, phone numbers, client or company names, or file contents. Describe the work, not the people. MCP server and skill names are fine; their config values are not.

Write it as JSON in exactly this shape:

```json
{
  "role": "one line: what they do",
  "summary": "2-3 sentences on the kind of work they do and what they want to get better at",
  "domains": ["areas they work in"],
  "daily_workflows": ["things they do most days"],
  "tech_stack": ["languages, frameworks, apps and platforms they use"],
  "tools_installed": ["AI tools, skills, plugins and MCP servers they already have"],
  "gaps": ["tasks that look slow, repetitive or painful for them"],
  "search_keywords": ["5 to 8 short search phrases for finding tools that would help"]
}
```

## 2. Show it, and ask before it leaves the machine

Show the user the profile and ask them to confirm or correct it. Do not send it anywhere until they say yes.

## 3. Scan

Save the confirmed profile to a temp file as `{"profile": { ...the JSON above... }}`, then send it to the hosted SkillScout. Never send the profile to any other address.

```bash
curl -sN -X POST "https://skillscout-eight.vercel.app/api/scan"   -H "Content-Type: application/json"   --data @/path/to/profile-request.json
```

The response is one JSON object per line. Progress lines have `"type":"source"`; the line with `"type":"results"` holds `tools` (ranked) and `stats`. A line with `"type":"error"`, or an HTTP error with `{"error": ...}`, means the scan did not finish: tell the user the message as given. HTTP 429 means they hit the hourly limit.

## 4. Report

Show the top 10 as a short list: name, score, category, the `why` line, and the install command if there is one. Group by category if that reads better. Then ask which ones to install.

Never install anything without the user picking it. When they do, run its `install_cmd` and report what happened.

## Source

Website: https://skillscout-eight.vercel.app
