# Gru Plugin Migration Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** gru/minion 스킬 5개를 dev-portfolio에서 분리해 독립적인 `gru` 플러그인(`~/Developer/gru`)으로 이전

**Architecture:** 새 디렉토리 `~/Developer/gru` 생성 → 스킬/커맨드 파일 복사 → `claude plugin` CLI로 마켓플레이스/플러그인 등록 → dev-portfolio에서 제거 → GitHub 리포 생성 및 push

**Tech Stack:** Bash, `claude plugin` CLI, gh CLI (GitHub)

---

### Task 1: Create gru repo and copy skill files

**Files:**
- Create: `~/Developer/gru/skills/gru-status/SKILL.md`
- Create: `~/Developer/gru/skills/gru-plan/SKILL.md`
- Create: `~/Developer/gru/skills/gru-assign/SKILL.md`
- Create: `~/Developer/gru/skills/gru-review/SKILL.md`
- Create: `~/Developer/gru/skills/minion-done/SKILL.md`
- Create: `~/Developer/gru/commands/gru-status.md`
- Create: `~/Developer/gru/commands/gru-plan.md`
- Create: `~/Developer/gru/commands/gru-assign.md`
- Create: `~/Developer/gru/commands/gru-review.md`
- Create: `~/Developer/gru/commands/minion-done.md`

**Step 1: Create directories**

```bash
mkdir -p ~/Developer/gru/skills ~/Developer/gru/commands
```

**Step 2: Copy skill files from dev-portfolio**

```bash
cp -r ~/Developer/dev-portfolio/skills/gru-status ~/Developer/gru/skills/
cp -r ~/Developer/dev-portfolio/skills/gru-plan ~/Developer/gru/skills/
cp -r ~/Developer/dev-portfolio/skills/gru-assign ~/Developer/gru/skills/
cp -r ~/Developer/dev-portfolio/skills/gru-review ~/Developer/gru/skills/
cp -r ~/Developer/dev-portfolio/skills/minion-done ~/Developer/gru/skills/
cp ~/Developer/dev-portfolio/commands/gru-status.md ~/Developer/gru/commands/
cp ~/Developer/dev-portfolio/commands/gru-plan.md ~/Developer/gru/commands/
cp ~/Developer/dev-portfolio/commands/gru-assign.md ~/Developer/gru/commands/
cp ~/Developer/dev-portfolio/commands/gru-review.md ~/Developer/gru/commands/
cp ~/Developer/dev-portfolio/commands/minion-done.md ~/Developer/gru/commands/
```

**Step 3: Verify**

```bash
ls ~/Developer/gru/skills/ && ls ~/Developer/gru/commands/
```
Expected: 5개 skills 디렉토리, 5개 command 파일

---

### Task 2: Register plugin via CLI

**Step 1: Add marketplace**

```bash
claude plugin marketplace add /Users/yyong/Developer/gru --name YYong91-gru
```

Expected: marketplace `YYong91-gru` registered successfully

**Step 2: Install plugin**

```bash
claude plugin install gru@YYong91-gru
```

Expected: plugin `gru@YYong91-gru` installed

**Step 3: Enable plugin**

```bash
claude plugin enable gru@YYong91-gru
```

**Step 4: Verify**

```bash
claude plugin list
```
Expected: `gru@YYong91-gru` 항목이 enabled 상태로 표시됨

---

### Task 3: Remove gru files from dev-portfolio

**Files:**
- Delete: `~/Developer/dev-portfolio/skills/gru-*/`
- Delete: `~/Developer/dev-portfolio/skills/minion-done/`
- Delete: `~/Developer/dev-portfolio/commands/gru-*.md`
- Delete: `~/Developer/dev-portfolio/commands/minion-done.md`
- Delete (cache): `~/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0/skills/gru-*/`
- Delete (cache): `~/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0/skills/minion-done/`
- Delete (cache): `~/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0/commands/gru-*.md`
- Delete (cache): `~/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0/commands/minion-done.md`

**Step 1: Remove from dev-portfolio source**

```bash
rm -rf ~/Developer/dev-portfolio/skills/gru-status
rm -rf ~/Developer/dev-portfolio/skills/gru-plan
rm -rf ~/Developer/dev-portfolio/skills/gru-assign
rm -rf ~/Developer/dev-portfolio/skills/gru-review
rm -rf ~/Developer/dev-portfolio/skills/minion-done
rm ~/Developer/dev-portfolio/commands/gru-status.md
rm ~/Developer/dev-portfolio/commands/gru-plan.md
rm ~/Developer/dev-portfolio/commands/gru-assign.md
rm ~/Developer/dev-portfolio/commands/gru-review.md
rm ~/Developer/dev-portfolio/commands/minion-done.md
```

**Step 2: Remove from dev-portfolio cache**

```bash
CACHE="$HOME/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0"
rm -rf "$CACHE/skills/gru-status" "$CACHE/skills/gru-plan" "$CACHE/skills/gru-assign"
rm -rf "$CACHE/skills/gru-review" "$CACHE/skills/minion-done"
rm -f "$CACHE/commands/gru-status.md" "$CACHE/commands/gru-plan.md"
rm -f "$CACHE/commands/gru-assign.md" "$CACHE/commands/gru-review.md"
rm -f "$CACHE/commands/minion-done.md"
```

**Step 3: Commit dev-portfolio cleanup**

```bash
cd ~/Developer/dev-portfolio
git add -A
git commit -m "refactor: move gru/minion skills to separate gru plugin"
git push
```

---

### Task 4: Initialize git and push to GitHub

**Step 1: Init git in gru repo**

```bash
cd ~/Developer/gru
git init
git add .
git commit -m "feat: initial gru plugin — minion-org skill system"
```

**Step 2: Create GitHub repo and push**

```bash
cd ~/Developer/gru
gh repo create YYong91/gru --public --description "Gru + Minion session orchestration skill system for Claude Code" --source=. --push
```

**Step 3: Verify**

```bash
cd ~/Developer/gru && git log --oneline -3
```

---

### Task 5: Update MEMORY.md

**Files:**
- Modify: `~/.claude/projects/-Users-yyong-Developer/memory/MEMORY.md`

**Step 1: Update Minion Org System section**

Find the line:
```
- Plugin cache 동기화 필요: source(`~/Developer/dev-portfolio/`) → cache(`~/.claude/plugins/cache/YYong91-plugins/dev-portfolio/0.1.0/`)
```

Replace with:
```
- Plugin: `gru@YYong91-gru` (source: `~/Developer/gru/`, cache: `~/.claude/plugins/cache/YYong91-gru/gru/0.1.0/`)
- GitHub: github.com/YYong91/gru
```
