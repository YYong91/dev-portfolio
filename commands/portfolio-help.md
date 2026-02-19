---
description: Show usage guide for dev-portfolio plugin
allowed-tools: Read
---

# /portfolio help

## Execution

Display the following usage guide:

---

## dev-portfolio Usage Guide

### Commands

| Command | When to use | What it does |
|---------|------------|--------------|
| `/portfolio setup` | First time only | Configure repo path, language, author |
| `/portfolio log` | After each session | Capture session data as raw JSON |
| `/portfolio generate` | When you want new content | Generate draft posts → PR |
| `/portfolio status` | Check inventory | Show pending/processed raw logs |
| `/portfolio wrap` | Session end (recommended) | /wrap + /portfolio log combined |
| `/portfolio help` | Right now | This guide |

### Typical Workflow

1. Work on your project as usual
2. Before ending the session: `/portfolio wrap` (or `/portfolio log`)
3. When you have a few logs accumulated: `/portfolio generate`
4. Review the PR on GitHub → edit if needed → merge
5. Site auto-deploys via GitHub Actions

### Raw Log Location

`~/.claude/portfolio/raw/*.json`

### Config Location

`~/.claude/portfolio/config.json`

---
