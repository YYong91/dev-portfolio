---
name: minion-done
description: Use when a Minion session has completed its assigned task. Writes a completion report and clears the task from active.md.
---

# Minion Done

작업 완료 보고서를 작성하고 active 목록에서 제거합니다.

## Process

### Step 1: Find current task

Read `~/.claude/org/active.md`. Find task whose `project_path` matches current working directory.
If not found: "현재 디렉토리에 배정된 작업이 없습니다. `/gru:assign` 으로 작업을 배정받으세요."

### Step 2: Summarize work done

Ask Claude (self): "이 세션에서 무슨 작업을 했나요? 완료된 것, 테스트 결과, 생성된 PR을 요약해주세요."
Based on conversation history, generate a concise summary.

If uncertain, ask user:
- "어떤 결과물이 나왔나요? (PR 번호, 브랜치명 등)"
- "남은 작업이 있나요?"
- "Gru 리뷰가 필요한가요? (머지/배포 결정)"

### Step 3: Write report

Create `~/.claude/org/reports/{YYYY-MM-DD}-{project}-{task-slug}.md`:

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

Read active.md. Remove the matching `### [project] task` section.
Write back.

### Step 5: Confirm

Show: "✅ 보고서 저장됨: reports/{filename}"
If 리뷰 필요 yes: "Gru에게 알림: `/gru:review` 로 확인 가능합니다."
