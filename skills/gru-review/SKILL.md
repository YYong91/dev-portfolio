---
name: gru-review
description: Use when Gru wants to review completed session reports and decide on merge/deploy.
---

# Gru Review

완료 보고서를 검토하고 머지/배포 여부를 결정합니다.

## Process

### Step 1: Find pending reviews

List `~/.claude/org/reports/`. Read each file.
Filter to only those containing `리뷰 필요: yes`.
Sort by filename (date) descending.

If none: "검토할 보고서가 없습니다."

### Step 2: For each report, show summary

```
## 📋 검토 대기: [{project}] {task}
완료일: {date}
결과: {result}
PR: {pr_link if exists}
```

Ask: "어떻게 하시겠습니까?" with options:
- 머지/배포 승인
- 재작업 요청 (메모 남기기)
- 나중에 검토

### Step 3: Update report based on decision

If approved: change `리뷰 필요: yes` → `리뷰 필요: no` in report file.
If rework: append `## Gru 피드백\n{memo}` to report. Keep `리뷰 필요: yes`.
If later: skip.
