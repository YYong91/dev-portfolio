---
name: portfolio-plan
description: Plan a new blog post interactively before writing. Guides through topic, target reader, key message, concrete examples, and structure.
allowed-tools: Read, Write, AskUserQuestion
---

# /portfolio plan

블로그 포스트를 바로 쓰지 않습니다. 먼저 기획합니다.

## Usage

```
/portfolio plan
/portfolio plan [topic hint]
```

## Execution

Use the **blog-planner** skill to conduct structured brainstorming with the user.

The skill will guide through:
1. 주제와 동기 파악
2. 핵심 메시지 확정
3. 독자 타겟 선택
4. 구체적 재료 수집
5. 구조 선택
6. 최종 확인 후 작성

After planning is complete and confirmed, proceed to write the post.
The output goes to the appropriate directory under `content/posts/` in the portfolio site.
