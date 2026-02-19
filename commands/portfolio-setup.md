---
description: Initial setup for dev-portfolio plugin. Configures repo path, language, and author.
allowed-tools: Bash, Read, Write, AskUserQuestion
---

# /portfolio setup

## Execution

1. Ask the user for configuration:
   - GitHub repo name (default: infer from gh CLI)
   - Local clone path (default: ~/projects/{repo-name})
   - Language (ko/en, default: ko)
   - Author name

2. Create config directory if needed:
   ```bash
   mkdir -p ~/.claude/portfolio/raw
   ```

3. Save config to `~/.claude/portfolio/config.json`:
   ```json
   {
     "repo_name": "{repo_name}",
     "local_path": "{local_path}",
     "language": "{language}",
     "author": "{author}",
     "raw_log_dir": "~/.claude/portfolio/raw",
     "auto_log_on_wrap": true
   }
   ```

4. Verify the local repo path exists and is a git repo.

5. Print confirmation:
   ```
   Portfolio setup complete!
   - Repo: {repo_name}
   - Path: {local_path}
   - Language: {language}
   - Raw logs: ~/.claude/portfolio/raw/
   ```
