# dev-portfolio

개발 세션을 자동으로 포트폴리오 콘텐츠로 변환하는 **Claude Code 플러그인**입니다.
작업 내용을 raw log로 캡처하고, AI 에이전트가 블로그 포스트 / TIL / 프로젝트 요약을 생성한 뒤 GitHub Pages 사이트에 PR을 올립니다.

## 설치

### 방법 1: 로컬 디렉토리에서 설치 (권장)

```bash
git clone https://github.com/YYong91/dev-portfolio.git
```

Claude Code 설정(`~/.claude/settings.json`)에 플러그인 디렉토리 등록:
```json
{
  "pluginDirectories": ["/path/to/dev-portfolio"]
}
```

Claude Code를 재시작하면 `/portfolio:*` 커맨드가 활성화됩니다.

### 방법 2: 개발 중 테스트

```bash
claude --plugin-dir /path/to/dev-portfolio
```

## 커맨드

| 커맨드 | 설명 |
|--------|------|
| `/portfolio:setup` | 초기 설정 (포트폴리오 레포 경로, 언어, 작성자) |
| `/portfolio:log` | 현재 세션을 raw log로 캡처 |
| `/portfolio:wrap` | `/wrap` + `/portfolio:log` 통합 실행 |
| `/portfolio:generate` | raw log → 콘텐츠 생성 → PR 생성 |
| `/portfolio:plan` | 새 블로그 포스트 인터랙티브 기획 |
| `/portfolio:status` | raw log 목록 (처리 전/후) 확인 |
| `/portfolio:help` | 사용 가이드 출력 |

## 동작 방식

```
세션 종료 → /portfolio:log → raw log 저장
                                    ↓
                         /portfolio:generate
                                    ↓
              ┌─────────────────────┴──────────────────────┐
              ↓                     ↓                      ↓
        블로그 포스트            TIL 포스트           프로젝트 요약
     (claude-exp-writer)      (til-writer)      (project-summarizer)
              └─────────────────────┬──────────────────────┘
                                    ↓
                           GitHub Pages PR 생성
```

## 프로젝트 구조

```
dev-portfolio/
├── skills/         # Claude Code 스킬 파일 (커맨드 동작 정의)
│   ├── portfolio-log.md
│   ├── portfolio-generate.md
│   ├── portfolio-plan.md
│   ├── portfolio-wrap.md
│   ├── portfolio-setup.md
│   ├── portfolio-status.md
│   └── portfolio-help.md
├── agents/         # 콘텐츠 생성 에이전트
│   ├── claude-exp-writer.md   # Claude 경험 블로그 포스트 작성
│   ├── til-writer.md          # TIL 포스트 작성
│   ├── project-summarizer.md  # 프로젝트 요약 업데이트
│   ├── session-extractor.md   # 세션 데이터 추출
│   └── resume-crafter.md      # 이력서 bullet 생성
├── commands/       # 커맨드 진입점
└── README.md
```

## 요구 사항

- Claude Code (claude.ai/code)
- 포트폴리오 GitHub Pages 레포 (콘텐츠 PR 대상)
- GitHub CLI (`gh`) — PR 생성에 사용

## 관련 링크

- 포트폴리오 사이트: [YYong91.github.io](https://YYong91.github.io)
