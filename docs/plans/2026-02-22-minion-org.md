# Gru Plugin Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Gru(총괄) + Minion(실행 세션) 체계로 다중 Claude Code 세션을 관리하는 `gru` 플러그인 구축

**Architecture:** `~/.claude/org/` 에 공유 상태(backlog/active/reports)를 마크다운 파일로 저장. 독립 플러그인 `gru` 를 `~/Developer/gru/` 에 생성하고 로컬 플러그인으로 등록. SessionStart hook으로 세션 시작 시 담당 작업 컨텍스트 자동 주입.

**Tech Stack:** Bash (hook script), Markdown (state files), Claude Code plugin skill system (SKILL.md + command .md)

**Skills:**
- `gru:status` — 전체 현황
- `gru:plan` — 백로그에 작업 추가
- `gru:assign` — 작업 배정
- `gru:review` — 완료 보고서 검토
- `gru:done` — 작업 완료 보고 (Minion이 사용)

**Plugin install note:**
- Source: `~/Developer/gru/`
- Local install: `~/.claude/plugins/local/gru/` 에 symlink
- settings.json `enabledPlugins` 에 `gru@local` 추가

---

### Task 1: Initialize org state directory

**Files:**
- Create: `~/.claude/org/backlog.md`
- Create: `~/.claude/org/active.md`
- Create: `~/.claude/org/projects.md`
- Create: `~/.claude/org/reports/` (directory)

**Step 1: Create directories**

```bash
mkdir -p ~/.claude/org/reports
```

**Step 2: Write backlog.md**

```markdown
## Backlog

<!-- 우선순위 순으로 정렬. /gru:plan 으로 추가하거나 직접 편집 -->
```

**Step 3: Write active.md**

```markdown
## Active Minions

<!-- /gru:assign 으로 자동 기록됨 -->
```

**Step 4: Write projects.md**

```markdown
## Projects

| 프로젝트 | 경로 | 설명 |
|---------|------|------|
| podo-budget | ~/Developer/podo-budget | AI 가계부 |
| podo-bookshelf | ~/Developer/podo-bookshelf | 독서 기록 |
| podo-auth | ~/Developer/podo-auth | SSO 인증 서비스 |
```

**Step 5: Verify**

```bash
ls ~/.claude/org/
```
Expected: `active.md  backlog.md  projects.md  reports/`

---

### Task 2: Create gru plugin scaffold

**Files:**
- Create: `~/Developer/gru/.claude-plugin/plugin.json`
- Create: `~/Developer/gru/skills/` (directory)
- Create: `~/Developer/gru/commands/` (directory)
- Create: `~/Developer/gru/README.md`

**Step 1: Create plugin directory**

```bash
mkdir -p ~/Developer/gru/.claude-plugin
mkdir -p ~/Developer/gru/skills
mkdir -p ~/Developer/gru/commands
```

**Step 2: Write plugin.json**

```json
{
  "name": "gru",
  "description": "Multi-session task management for Claude Code — Gru assigns, Minions execute",
  "version": "0.1.0",
  "author": {
    "name": "YYong91"
  },
  "keywords": ["task-management", "session", "workflow", "gru", "minion"]
}
```

**Step 3: Write README.md**

```markdown
# gru

Multi-session task management plugin for Claude Code.

Gru assigns tasks. Minions execute them.

## Commands

| Command | Description |
|---------|-------------|
| `/gru:status` | Overall status — active sessions, recent completions, backlog |
| `/gru:plan` | Add a task to the backlog |
| `/gru:assign` | Assign backlog tasks to active sessions |
| `/gru:review` | Review completed session reports |
| `/gru:done` | Mark current task as done and write report |

## State Files

All state lives in `~/.claude/org/`:
- `backlog.md` — task queue
- `active.md` — in-progress sessions
- `reports/` — completion reports
- `projects.md` — project registry
```

**Step 4: Register as local plugin**

```bash
# Symlink source into local plugins directory
mkdir -p ~/.claude/plugins/local
ln -s ~/Developer/gru ~/.claude/plugins/local/gru
```

**Step 5: Enable in settings.json**

Read `~/.claude/settings.json`. Add `"gru@local": true` to `enabledPlugins`:

```json
{
  "enabledPlugins": {
    "frontend-design@claude-plugins-official": true,
    "code-review@claude-plugins-official": true,
    "commit-commands@claude-code-plugins": true,
    "feature-dev@claude-code-plugins": true,
    "claude-mem@thedotmack": true,
    "superpowers@superpowers-marketplace": true,
    "dev-portfolio@YYong91-plugins": true,
    "gru@local": true
  },
  "model": "sonnet"
}
```

**Step 6: Verify plugin is recognized**

Restart Claude Code. Run `/gru:status`. Should invoke the skill.

---

### Task 3: Create /gru:status skill

**Files:**
- Create: `~/Developer/gru/skills/status/SKILL.md`
- Create: `~/Developer/gru/commands/status.md`

**Step 1: Write SKILL.md**

```markdown
---
name: status
description: Use when the user wants to see overall project status — active sessions, recent completions, and top backlog items. Invoke at the start of a Gru session to get oriented.
---

# Gru Status

전체 조직 현황을 한눈에 보여줍니다.

## Process

### Step 1: Read active.md

Read `~/.claude/org/active.md`. Parse each `### [project] task` section.

### Step 2: Read recent reports

List files in `~/.claude/org/reports/`. Sort by filename descending. Take top 3.
For each, read and extract: 작업명, 프로젝트, 완료일시, 결과 한 줄 요약.

### Step 3: Read backlog top 3

Read `~/.claude/org/backlog.md`. Find first 3 unchecked items (`- [ ]`).

### Step 4: Output formatted status

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

If backlog is empty: suggest `/gru:plan` to add tasks.
If active is empty: suggest `/gru:assign` to start work.
```

**Step 2: Write command file**

```markdown
---
description: Show overall status — active sessions, recent completions, top backlog. Run this at the start of a session to orient yourself.
allowed-tools: Read, Bash, Glob
---

# /gru:status

Follow the status skill (skills/status/SKILL.md).
```

---

### Task 4: Create /gru:plan skill

**Files:**
- Create: `~/Developer/gru/skills/plan/SKILL.md`
- Create: `~/Developer/gru/commands/plan.md`

**Step 1: Write SKILL.md**

```markdown
---
name: plan
description: Use when the user wants to add a new task to the backlog. Supports inline args or interactive mode.
---

# Gru Plan

새 작업을 backlog에 추가합니다.

## Process

### Step 1: Parse input

If user provided args (e.g. `/gru:plan "구글 OAuth 연동" podo-auth high`):
- description = first quoted string or full arg text
- project = second arg (optional, match against projects.md)
- priority = third arg: high/medium/low (default: medium)

If no args, ask interactively (plain text, not AskUserQuestion):
1. "어떤 작업인가요?" → description
2. Read `~/.claude/org/projects.md`, show project list → "어느 프로젝트인가요?"
3. "우선순위는? (high/medium/low, 기본값: medium)"

### Step 2: Format and append to backlog.md

Entry format:
```
- [ ] [{project}] {description} #{priority}
```

Read `~/.claude/org/backlog.md`. Append entry. Write back.

### Step 3: Confirm

"✅ 백로그에 추가됨: [{project}] {description} #{priority}"
"작업을 바로 시작하려면 `/gru:assign` 을 실행하세요."
```

**Step 2: Write command file**

```markdown
---
description: Add a new task to the backlog. Usage: /gru:plan "task" [project] [priority] — or run without args for interactive mode.
allowed-tools: Read, Write
---

# /gru:plan

Follow the plan skill (skills/plan/SKILL.md).
```

---

### Task 5: Create /gru:assign skill

**Files:**
- Create: `~/Developer/gru/skills/assign/SKILL.md`
- Create: `~/Developer/gru/commands/assign.md`

**Step 1: Write SKILL.md**

```markdown
---
name: assign
description: Use when the user wants to assign backlog tasks to active Minion sessions. Picks top items from backlog and writes them to active.md.
---

# Gru Assign

백로그 작업을 Active Minion으로 등록합니다.

## Process

### Step 1: Read backlog

Read `~/.claude/org/backlog.md`. Find all unchecked items (`- [ ]`).
If empty: "백로그가 비어있습니다. `/gru:plan` 으로 작업을 추가하세요."

### Step 2: Show options and get selection

Display unchecked items numbered. Use AskUserQuestion (multiSelect: true) to let user pick one or more.

### Step 3: For each selected task, append to active.md

```
### [{project}] {description}
- assigned: {YYYY-MM-DD HH:MM}
- project_path: ~/Developer/{project}
- priority: {priority}
- status: in_progress
```

### Step 4: Mark as assigned in backlog.md

Change `- [ ]` → `- [x]` for assigned items.

### Step 5: Confirm

"✅ {N}개 작업 배정 완료. 해당 프로젝트 세션을 열면 자동으로 컨텍스트가 주입됩니다."
```

**Step 2: Write command file**

```markdown
---
description: Assign top backlog tasks to active sessions. Shows unassigned tasks, lets you pick one or more, writes to active.md.
allowed-tools: Read, Write, AskUserQuestion
---

# /gru:assign

Follow the assign skill (skills/assign/SKILL.md).
```

---

### Task 6: Create /gru:review skill

**Files:**
- Create: `~/Developer/gru/skills/review/SKILL.md`
- Create: `~/Developer/gru/commands/review.md`

**Step 1: Write SKILL.md**

```markdown
---
name: review
description: Use when the user wants to review completed session reports and decide on merge/deploy actions.
---

# Gru Review

완료 보고서를 검토하고 머지/배포 여부를 결정합니다.

## Process

### Step 1: Find pending reviews

List `~/.claude/org/reports/`. Read each file.
Filter to those containing `리뷰 필요: yes`.
Sort by filename (date) descending.

If none: "검토할 보고서가 없습니다. Minions이 열심히 일하는 중!"

### Step 2: For each pending report, show summary

```
## 📋 검토 대기: [{project}] {task}
완료일: {date}
결과: {result}
PR: {pr_link or "없음"}
```

Use AskUserQuestion:
- 머지/배포 승인
- 재작업 요청
- 나중에 검토

### Step 3: Update report based on decision

Approved → `리뷰 필요: yes` → `리뷰 필요: no`
Rework → append `## Gru 피드백\n{memo}`, keep `리뷰 필요: yes`
Later → skip
```

**Step 2: Write command file**

```markdown
---
description: Review completed session reports. Shows pending reviews, lets you approve/reject/defer each.
allowed-tools: Read, Write, Glob, AskUserQuestion
---

# /gru:review

Follow the review skill (skills/review/SKILL.md).
```

---

### Task 7: Create /gru:done skill

**Files:**
- Create: `~/Developer/gru/skills/done/SKILL.md`
- Create: `~/Developer/gru/commands/done.md`

**Step 1: Write SKILL.md**

```markdown
---
name: done
description: Use when a Minion session has completed its assigned task. Writes a completion report and clears the task from active.md. Run this before closing a work session.
---

# Gru Done

작업 완료 보고서를 작성하고 active 목록에서 제거합니다.

## Process

### Step 1: Find current task

Read `~/.claude/org/active.md`.
Find the section whose `project_path` matches the current working directory (expand `~`).
If not found: "현재 디렉토리에 배정된 작업이 없습니다. `/gru:assign` 으로 작업을 배정받으세요."

### Step 2: Summarize work done

Review the current session conversation history. Generate a concise summary of:
- 무엇을 완료했는지
- 테스트 결과 (통과한 테스트 수 등)
- 생성된 PR 또는 브랜치

If uncertain, ask user (plain text):
- "어떤 결과물이 나왔나요? (PR 번호, 브랜치명 등)"
- "남은 작업이 있나요?"
- "Gru 리뷰가 필요한가요? (머지/배포 결정 필요 시 yes)"

### Step 3: Write report

Create file: `~/.claude/org/reports/{YYYY-MM-DD}-{project}-{task-slug}.md`
(task-slug: description을 소문자, 공백→하이픈, 최대 30자)

```markdown
## 완료 보고서

**작업:** {task_description}
**프로젝트:** {project}
**완료일시:** {YYYY-MM-DD HH:MM}
**결과:** {summary}
**PR:** {pr_link or "없음"}
**남은 것:** {remaining or "없음"}
**리뷰 필요:** {yes/no}
```

### Step 4: Remove from active.md

Read active.md. Remove the matching `### [project] task` section (from `###` to next `###` or end of file).
Write back.

### Step 5: Confirm

"✅ 보고서 저장됨: reports/{filename}"
If 리뷰 필요 yes: "Gru 세션에서 `/gru:review` 로 확인 가능합니다."
```

**Step 2: Write command file**

```markdown
---
description: Mark current session's assigned task as done. Summarizes work, writes completion report to reports/, removes from active.md. Run before closing a work session.
allowed-tools: Read, Write, Glob, Bash, AskUserQuestion
---

# /gru:done

Follow the done skill (skills/done/SKILL.md).
```

---

### Task 8: Create SessionStart hook

**Files:**
- Create: `~/.claude/hooks/gru-context.sh`
- Modify: `~/.claude/settings.json`

**Step 1: Write hook script**

```bash
#!/bin/bash
# ~/.claude/hooks/gru-context.sh
# Injects active task context when opening a Minion session

ACTIVE_FILE="$HOME/.claude/org/active.md"
[ -f "$ACTIVE_FILE" ] || exit 0

python3 - "$(pwd)" "$ACTIVE_FILE" << 'PYEOF'
import sys, re, os

current_dir = sys.argv[1]
active_file = sys.argv[2]

with open(active_file) as f:
    content = f.read()

sections = re.split(r'\n(?=### )', content)
for section in sections:
    if not section.startswith('###'):
        continue
    lines = section.strip().split('\n')
    title = lines[0].replace('### ', '')
    project_path = ''
    for line in lines:
        if 'project_path:' in line:
            project_path = line.split('project_path:')[1].strip()

    expanded = os.path.expanduser(project_path.rstrip('/'))
    if current_dir.startswith(expanded):
        print("## 🍌 Minion Context")
        print("")
        print(f"현재 담당 작업: **{title}**")
        for line in lines[1:]:
            if line.strip():
                print(line)
        print("")
        print("> 작업 완료 시 `/gru:done` 을 실행하세요.")
        break
PYEOF
```

**Step 2: Make executable**

```bash
mkdir -p ~/.claude/hooks
chmod +x ~/.claude/hooks/gru-context.sh
```

**Step 3: Add hook to settings.json**

Read `~/.claude/settings.json`. Add `hooks` key:

```json
{
  "enabledPlugins": { ... },
  "model": "sonnet",
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash $HOME/.claude/hooks/gru-context.sh"
          }
        ]
      }
    ]
  }
}
```

**Step 4: Test hook manually**

First assign a test task via active.md (add a test entry manually), then:

```bash
cd ~/Developer/podo-budget
bash ~/.claude/hooks/gru-context.sh
```

Expected output: prints the 🍌 Minion Context block if matching task exists.

---

### Task 9: Initialize git repo and smoke test

**Step 1: Init git in gru plugin**

```bash
cd ~/Developer/gru
git init
git add .
git commit -m "feat: initial gru plugin — session management for Claude Code"
```

**Step 2: End-to-end smoke test**

1. Open Claude Code in `~/Developer` (Gru session)
2. `/gru:status` → shows empty state gracefully (no errors)
3. `/gru:plan "테스트 작업" podo-budget medium` → appends to backlog.md
4. Verify: `cat ~/.claude/org/backlog.md` shows new entry
5. `/gru:assign` → shows task, select it → writes to active.md
6. Verify: `cat ~/.claude/org/active.md` shows assigned task
7. Open new Claude Code session in `~/Developer/podo-budget`
8. SessionStart hook should inject "🍌 Minion Context: 테스트 작업"
9. `/gru:done` → writes report, clears active.md
10. Back in Gru session: `/gru:review` → shows the report
11. Verify: `ls ~/.claude/org/reports/` shows the report file

**Step 3: Update MEMORY.md**

Add to `~/.claude/projects/-Users-yyong-Developer/memory/MEMORY.md`:

```markdown
## Gru Plugin (세션 관리)
- 플러그인: `gru@local` (`~/Developer/gru/`)
- 스킬: /gru:status, /gru:plan, /gru:assign, /gru:review, /gru:done
- 상태 파일: ~/.claude/org/ (backlog.md, active.md, reports/, projects.md)
- Hook: ~/.claude/hooks/gru-context.sh → SessionStart에 담당 작업 자동 주입
- Gru 세션: ~/Developer 에서 열어서 전체 관리
- Minion 세션: 프로젝트 디렉토리에서 열면 자동으로 담당 작업 표시
```
