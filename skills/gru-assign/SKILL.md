---
name: gru-assign
description: Use when Gru wants to assign backlog tasks to active sessions. Picks top items and writes to active.md.
---

# Gru Assign

백로그 작업을 Active Minion으로 등록합니다.

## Process

### Step 1: Read backlog

Read `~/.claude/org/backlog.md`. Find all unchecked items (`- [ ]`).
If empty: "백로그가 비어있습니다. `/gru:plan` 으로 작업을 추가하세요."

### Step 2: Show options and get selection

Display unchecked items numbered. Ask user which to assign (can pick multiple).
Use AskUserQuestion with the list.

### Step 3: For each selected task

Append to `~/.claude/org/active.md`:

```
### [{project}] {description}
- assigned: {YYYY-MM-DD HH:MM}
- project_path: ~/Developer/{project}
- priority: {priority}
- status: in_progress
```

### Step 4: Mark as assigned in backlog

In `backlog.md`, change `- [ ]` to `- [x]` for assigned items.

### Step 5: Confirm

Show assigned tasks and say:
"✅ {N}개 작업이 배정되었습니다. 해당 프로젝트 세션을 열면 자동으로 컨텍스트가 주입됩니다."
