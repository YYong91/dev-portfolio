# dev-portfolio

Claude Code plugin that captures development session data and generates portfolio content for your GitHub Pages site.

## Quick Start

1. Test locally: `claude --plugin-dir ~/.claude/plugins/local/dev-portfolio`
2. Setup: `/portfolio setup`
3. After a session: `/portfolio wrap` (or `/portfolio log`)
4. Generate content: `/portfolio generate`

## Commands

| Command | Description |
|---------|-------------|
| `/portfolio setup` | Initial configuration (repo path, language, author) |
| `/portfolio log` | Capture current session as raw log |
| `/portfolio generate` | Generate draft content from raw logs → PR |
| `/portfolio status` | Show raw log inventory |
| `/portfolio wrap` | /wrap + /portfolio log combined |
| `/portfolio help` | Show usage guide |
