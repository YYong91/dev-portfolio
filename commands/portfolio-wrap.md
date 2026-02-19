---
description: Combined session wrap-up — runs session-wrap analysis then captures portfolio raw log. Use instead of /wrap when you want both.
allowed-tools: Bash, Read, Write, Glob, Grep, Task, AskUserQuestion, Skill
---

# /portfolio wrap

## Execution

This command combines session-wrap and portfolio logging into a single workflow.

### Steps

1. **Run session-wrap first**:
   Invoke the `session-wrap:wrap` skill to perform the standard session wrap-up
   (doc updates, automation suggestions, learning extraction, follow-up tasks).

2. **Then capture portfolio log**:
   After session-wrap completes, follow the `/portfolio log` steps:
   - Check config exists (`~/.claude/portfolio/config.json`)
   - Collect git context
   - Launch session-extractor agent
   - Show preview to user
   - Save raw log

3. **Summary**:
   ```
   Session wrap + portfolio log complete!
   - Session wrap: {actions taken}
   - Raw log saved: ~/.claude/portfolio/raw/{filename}.json
   ```

## Notes

- If `/portfolio setup` hasn't been run yet, skip the portfolio log step and suggest running setup.
- The session-wrap and portfolio log are independent — if one fails, the other should still work.
