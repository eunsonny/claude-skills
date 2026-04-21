# my-claude-skills

개발 워크플로우 자동화를 위한 Claude Code 플러그인.

## Structure

```
my-claude-skills/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── commit-push.md         # /commit-push 슬래시 커맨드
└── skills/
    └── commit-push/
        ├── SKILL.md            # 커밋 & 푸시 스킬
        └── references/
            └── commit-conventions.md
```

## Skills

### commit-push

변경사항을 분석하여 Conventional Commits 형식의 한국어 커밋 메시지를 자동 생성하고, 커밋 및 푸시를 수행합니다.

**주요 기능:**
- 브랜치 이름에서 지라 티켓 번호 자동 감지 (PI-, CS-, LIVE- 등)
- Conventional Commits 타입 자동 분류
- 한국어 커밋 메시지 생성
- 커밋 전 diff 요약 표시
- 푸시 전 사용자 확인

**커밋 메시지 예시:**
```
PI-1234 feat: 로그인 페이지 UI 구현
CS-567 fix: 사용자 목록 정렬 오류 수정
feat: 다크모드 지원 추가
```

## Installation

```bash
claude plugin add /path/to/my-claude-skills
```