---
description: Generate draft portfolio content from accumulated raw logs and create a PR to the personal repo.
allowed-tools: Bash, Read, Write, Glob, Grep, Task, AskUserQuestion
---

# /portfolio generate

## Execution

Follow the draft-generation skill (skills/draft-generation/SKILL.md).

### Steps

1. **Load config**: Read `~/.claude/portfolio/config.json`. Exit if missing.

2. **Scan raw logs**: Read all `~/.claude/portfolio/raw/*.json` files.
   Filter for `processed != true`.
   If none found: "미처리 raw log가 없어요. `/portfolio log`로 먼저 세션을 기록해주세요."

3. **Analyze and present options** (AskUserQuestion):
   Show unprocessed log count and detected content hints.
   Let user select which content types to generate (multiSelect).

4. **Run writer agents** (parallel Task calls):
   - For each selected content type, launch the appropriate agent
   - Pass relevant raw log data as context
   - Each agent returns generated markdown content

5. **Anti-AI review**: Review all generated content.
   Check for banned phrases. If found, flag and offer to regenerate.

6. **Preview**: Show generated content to user for review.
   Ask if they want to proceed or edit.

7. **Create PR**:
   ```bash
   cd {config.local_path}
   git checkout main
   git pull origin main
   git checkout -b content/$(date +%Y-%m-%d)-batch
   # Copy generated files to appropriate content/ subdirectories
   git add content/
   git commit -m "content: add {N} new posts from recent sessions"
   git push origin content/$(date +%Y-%m-%d)-batch
   gh pr create --title "Add {N} new posts" --body "..."
   ```

8. **Mark processed**: Update raw log files with `"processed": true`.

9. **Done**: Print PR URL and summary.
