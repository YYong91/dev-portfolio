# Gru Plugin Separation Design

**Date:** 2026-02-22
**Goal:** gru/minion 스킬을 dev-portfolio에서 분리해 독립적인 `gru` 플러그인으로 만들기

## Context

현재 gru-status, gru-plan, gru-assign, gru-review, minion-done 5개 스킬이 dev-portfolio 플러그인 안에 있음. 용도가 전혀 다른 두 시스템(포트폴리오 자동화 vs 세션 오케스트레이션)이 하나의 플러그인에 섞여 있어 분리 필요.

## Design

### New Plugin: `gru@YYong91-gru`

**Location:** `~/Developer/gru/`
**GitHub:** `YYong91/gru`
**Marketplace:** `YYong91-gru` (local directory source)

### Directory Structure

```
~/Developer/gru/
  skills/
    gru-status/SKILL.md
    gru-plan/SKILL.md
    gru-assign/SKILL.md
    gru-review/SKILL.md
    minion-done/SKILL.md
  commands/
    gru-status.md
    gru-plan.md
    gru-assign.md
    gru-review.md
    minion-done.md
```

### Plugin Registration

- `known_marketplaces.json`: `YYong91-gru` → source: directory, path: `~/Developer/gru`
- `installed_plugins.json`: `gru@YYong91-gru` → installPath: `~/.claude/plugins/cache/YYong91-gru/gru/0.1.0`
- `settings.json`: `enabledPlugins["gru@YYong91-gru"] = true`
- Cache: `~/.claude/plugins/cache/YYong91-gru/gru/0.1.0/`

### Cleanup

- dev-portfolio에서 gru-* / minion-done 스킬+커맨드 삭제
- dev-portfolio 캐시에서도 동일하게 삭제
- dev-portfolio commit
