---
name: gru-plan
description: Use when Gru wants to add a new task to the backlog. Supports inline args or interactive mode.
---

# Gru Plan

새 작업을 backlog에 추가합니다.

## Process

### Step 1: Parse input

If user provided args (e.g. `/gru:plan "구글 OAuth 연동" podo-auth high`):
- description = first quoted string
- project = second arg (optional)
- priority = third arg: high/medium/low (default: medium)

If no args: ask interactively (text, not AskUserQuestion):
1. "어떤 작업인가요?" → description
2. "어느 프로젝트인가요? (projects.md 참고)" → show projects list, get project
3. "우선순위는? (high/medium/low, 기본값: medium)" → priority

### Step 2: Format backlog entry

```
- [ ] [{project}] {description} #{priority}
```

### Step 3: Append to backlog.md

Read `~/.claude/org/backlog.md`.
Append the new entry under `## Backlog` section.
Write back.

### Step 4: Confirm

Show: "✅ 백로그에 추가됨: [{project}] {description} #{priority}"
Suggest: "작업을 바로 시작하려면 `/gru:assign` 을 실행하세요."
