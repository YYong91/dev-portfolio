---
name: draft-generation
description: Use when generating portfolio content from accumulated raw logs. Guides writer agents and PR creation. Triggered by /portfolio generate command.
---

# Draft Generation

## Overview

Transforms accumulated raw session logs into draft portfolio content and creates a PR.

## Process

1. Load config from `~/.claude/portfolio/config.json`
2. Scan raw logs: `~/.claude/portfolio/raw/*.json` where `processed != true`
3. Group by content_hints → determine which agents to run
4. Present options to user via AskUserQuestion
5. Run selected agents in parallel (Task tool)
6. Show generated content preview
7. Clone/update personal repo → create branch → add files → PR
8. Mark raw logs as processed

## Anti-AI Quality Gate

Before creating the PR, review ALL generated content for AI-smell:
- Check for banned phrases from the Writing Style Guide
- Verify specific details from raw logs are included
- Ensure varied sentence structure
- If any content smells like AI, rewrite that section

## PR Template

Title: "Add {N} new posts from recent sessions"
Body:
- List of new files added
- Content type breakdown
- Source session dates
