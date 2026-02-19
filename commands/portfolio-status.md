---
description: Show raw log inventory — pending vs processed, content hints distribution
allowed-tools: Bash, Read, Glob
---

# /portfolio status

## Execution

1. Read config from `~/.claude/portfolio/config.json`. If not found, tell user to run `/portfolio setup` first.

2. Scan `~/.claude/portfolio/raw/*.json` for all raw log files.

3. For each file, read and categorize:
   - **Unprocessed**: `processed` is false or missing
   - **Processed**: `processed` is true

4. Aggregate content_hints across unprocessed logs:
   - TIL-worthy count
   - Project update count
   - Resume-worthy count
   - Claude experience count

5. Display summary:
   ```
   Portfolio Raw Log Status
   ========================
   Total logs:      12
   Unprocessed:      5
   Processed:        7

   Unprocessed Content Hints:
   - TIL posts:          3
   - Project updates:    2
   - Resume bullets:     1
   - Claude experiences: 2

   Oldest unprocessed: 2026-02-15
   Latest unprocessed: 2026-02-19
   ```
