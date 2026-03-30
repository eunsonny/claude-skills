---
name: commit-push
description: This skill should be used when the user asks to "커밋해줘", "커밋하고 푸시해줘", "변경사항 올려줘", "코드 올려줘", "푸시해줘", "commit", "push", "commit and push", "git push", or wants to commit and/or push their changes to the remote repository. Generates Korean-language commit messages following Conventional Commits format with automatic Jira ticket detection.
version: 0.1.0
---

# Commit & Push

개발한 변경사항을 분석하여 Conventional Commits 형식의 한국어 커밋 메시지를 자동 생성하고, 커밋 및 푸시를 수행하는 스킬.

## Workflow

### 1. 변경사항 분석

커밋 전 현재 상태를 파악한다.

```bash
git status
git diff --staged
git diff
```

변경사항 요약을 사용자에게 표시하여 커밋 범위를 확인받는다.

### 2. 지라 티켓 번호 감지

현재 브랜치 이름에서 지라 티켓 번호를 자동 추출한다.

```bash
git branch --show-current
```

브랜치 이름에서 `[A-Z]+-\d+` 패턴(대문자 접두사 + 하이픈 + 숫자)을 추출한다. 예시:
- `feature/login-PI-1234` → `PI-1234`
- `fix/bug-CS-567` → `CS-567`
- `hotfix/LIVE-89` → `LIVE-89`

PI, CS, LIVE는 예시이며, 모든 대문자 프로젝트 접두사를 자동 감지한다.

### 3. 커밋 메시지 생성

#### 형식

**지라 티켓이 있는 경우:**
```
{Jira Issue ID} {type}: {description in Korean}
```

예시:
```
PI-1234 feat: 로그인 페이지 UI 구현
CS-567 fix: 사용자 목록 정렬 오류 수정
LIVE-89 hotfix: 결제 실패 시 에러 핸들링 추가
```

**지라 티켓이 없는 경우:**
```
{type}: {description in Korean}
```

예시:
```
feat: 다크모드 지원 추가
fix: 날짜 포맷 변환 버그 수정
refactor: API 호출 로직 리팩토링
```

#### Conventional Commits 타입

| 타입 | 용도 |
|------|------|
| `feat` | 새로운 기능 추가 |
| `fix` | 버그 수정 |
| `docs` | 문서 변경 |
| `style` | 코드 포맷팅 (기능 변경 없음) |
| `refactor` | 리팩토링 (기능 변경 없음) |
| `test` | 테스트 추가/수정 |
| `chore` | 빌드, 설정 등 기타 변경 |
| `hotfix` | 긴급 수정 |
| `perf` | 성능 개선 |

#### 메시지 작성 규칙

- 설명은 한국어로 간결하게 작성한다 (50자 이내 권장)
- 명사형 종결을 사용한다 (예: "구현", "수정", "추가", "제거")
- 변경의 "무엇을"과 "왜"를 담는다
- 여러 파일이 변경된 경우 핵심 변경사항을 대표하는 메시지를 작성한다

커밋 메시지 작성 상세 규칙은 `references/commit-conventions.md` 참조.

### 4. 커밋 실행

변경사항을 스테이징하고 커밋한다.

```bash
git add <관련 파일들>
git commit -m "{커밋 메시지}"
```

- `git add .` 보다는 관련 파일을 명시적으로 스테이징한다
- `.env`, credentials 등 민감한 파일은 제외한다

### 5. 푸시 (확인 후)

커밋 완료 후 사용자에게 푸시 여부를 확인한다.

사용자에게 확인을 요청한다:
- "원격 저장소에 푸시할까요?"
- 옵션: 푸시 / 나중에

사용자가 승인하면:
```bash
git push origin {현재 브랜치}
```

원격 브랜치가 없는 경우:
```bash
git push -u origin {현재 브랜치}
```

## 주의사항

- force push는 절대 사용하지 않는다
- 커밋 전 반드시 diff 요약을 보여준다
- 스테이징 시 민감한 파일을 포함하지 않는다
- 사용자가 명시적으로 요청하지 않는 한 `--no-verify` 옵션을 사용하지 않는다

