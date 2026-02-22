---
name: gru-status
description: Use when Gru (the user) wants to see overall project status across all sessions - active work, recent completions, and top backlog items.
---

# Gru Status

전체 조직 현황을 한눈에 보여줍니다.

## Process

### Step 1: Read active.md

Read `~/.claude/org/active.md`. Parse each `### [project] task` section.

### Step 2: Read recent reports

List files in `~/.claude/org/reports/`. Sort by name descending. Take top 3.
For each, read and extract: 작업명, 프로젝트, 완료일시, 결과 한 줄 요약.

### Step 3: Read backlog top 3

Read `~/.claude/org/backlog.md`. Find first 3 unchecked items (`- [ ]`).

### Step 4: Output formatted status

Format:
```
## Gru Status

### 🟡 Active Minions
- [project] task name (assigned: DATE)
(없으면: "진행 중인 세션 없음")

### ✅ Recently Completed
- [project] task name — RESULT_SUMMARY
(없으면: "최근 완료 없음")

### 📋 Backlog (top 3)
1. [project] task #priority
(없으면: "백로그 비어있음")
```

If backlog is empty, suggest running `/gru:plan` to add tasks.
If active is empty, suggest running `/gru:assign` to start work.
