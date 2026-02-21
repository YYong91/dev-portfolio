# dev-portfolio

Claude Code plugin that captures development session data and generates portfolio content for your GitHub Pages site.

## Installation

### Method 1: Install from local marketplace (Recommended)

1. Clone this repository:
   ```bash
   git clone https://github.com/YYong91/dev-portfolio.git
   cd dev-portfolio
   ```

2. In Claude Code, add the marketplace and install:
   ```bash
   /plugin marketplace add /path/to/dev-portfolio
   /plugin install dev-portfolio@YYong91-plugins
   ```

3. Restart Claude Code to load the plugin

### Method 2: Test without installation

For testing during development:
```bash
claude --plugin-dir /path/to/dev-portfolio
```

## Quick Start

1. Setup: `/portfolio:setup`
2. After a session: `/portfolio:wrap` (or `/portfolio:log`)
3. Generate content: `/portfolio:generate`

## Commands

| Command | Description |
|---------|-------------|
| `/portfolio:setup` | Initial configuration (repo path, language, author) |
| `/portfolio:log` | Capture current session as raw log |
| `/portfolio:generate` | Generate draft content from raw logs → PR |
| `/portfolio:status` | Show raw log inventory |
| `/portfolio:wrap` | /wrap + /portfolio:log combined |
| `/portfolio:help` | Show usage guide |

Note: Commands are namespaced with `portfolio:` prefix after installation.
