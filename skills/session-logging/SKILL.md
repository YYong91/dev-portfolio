---
name: session-logging
description: Use when capturing session data for portfolio. Guides session-extractor agent and raw log storage. Triggered by /portfolio log command.
---

# Session Logging

## Overview

Captures the current development session as a structured raw log for later content generation.

## Process

1. Read config from `~/.claude/portfolio/config.json`
2. Launch `session-extractor` agent (Task tool, subagent_type: general-purpose)
3. Agent analyzes git context + conversation → returns JSON
4. Validate JSON has required fields
5. Save to `~/.claude/portfolio/raw/YYYY-MM-DD-{project}-{ticket}.json`
   - If ticket is null, use branch short name
   - If file already exists, append `-2`, `-3`, etc.
6. Print summary to user

## Validation Rules

Required fields: date, project, summary, topics, content_hints
Optional but recommended: commits, lessons, key_decisions, tools_used
