---
description: Capture current session as a structured raw log for portfolio content generation. Use at session end or anytime during a session.
allowed-tools: Bash, Read, Write, Glob, Grep, Task, AskUserQuestion
---

# /portfolio log

## Execution

Follow the session-logging skill (skills/session-logging/SKILL.md).

### Steps

1. **Check config**: Read `~/.claude/portfolio/config.json`. If missing, tell user to run `/portfolio setup`.

2. **Collect git context**:
   ```bash
   git branch --show-current
   git log origin/develop..HEAD --oneline 2>/dev/null || git log --oneline -5
   git diff --stat origin/develop..HEAD 2>/dev/null || git diff --stat HEAD~3..HEAD
   ```

3. **Extract session data**: Launch `session-extractor` agent via Task tool.
   The agent has access to the full conversation context and will produce structured JSON.

4. **Let user review**: Show the extracted raw log summary:
   ```
   Session Log Preview
   ===================
   Date:     2026-02-19
   Project:  core_backend
   Branch:   feature/TD-416-billing-bugfix
   Summary:  organization_id 잔재 및 DI account_service 누락 버그 수정
   Topics:   billing, DDD, bugfix
   Lessons:  2 items
   Hints:    TIL ✓  Project ✗  Resume ✗  Claude ✓
   ```
   Ask: "이 내용으로 저장할까요? 수정할 부분이 있으면 알려주세요."

5. **Save raw log**: Write JSON to `~/.claude/portfolio/raw/YYYY-MM-DD-{project}-{ticket}.json`

6. **Confirm**: "Raw log 저장 완료: ~/.claude/portfolio/raw/2026-02-19-cubig-TD416.json"
