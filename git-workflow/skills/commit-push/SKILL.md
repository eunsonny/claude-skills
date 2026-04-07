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

### 2. 커밋 단위 분리

변경사항을 의미 단위로 그룹핑하여 여러 커밋으로 나눈다. 하나의 커밋에 모든 변경사항을 넣지 않는다.

#### 분리 기준

다음 기준에 따라 변경사항을 논리적 커밋 단위로 분류한다:

1. **기능 단위**: 하나의 기능을 구성하는 파일들을 하나의 커밋으로 묶는다
2. **타입 단위**: feat, fix, refactor, docs 등 서로 다른 타입의 변경은 별도 커밋으로 분리한다
3. **관심사 단위**: 비즈니스 로직, UI, 테스트, 설정 파일 등 관심사가 다르면 분리를 고려한다

#### 분리 예시

| 변경 내용 | 커밋 분리 |
|-----------|----------|
| 로그인 기능 구현 + 로그인 테스트 추가 | `feat: 로그인 기능 구현` → `test: 로그인 기능 테스트 추가` |
| 버그 수정 + 관련 리팩토링 | `fix: 정렬 오류 수정` → `refactor: 정렬 로직 함수 분리` |
| 새 컴포넌트 + 문서 업데이트 | `feat: 알림 컴포넌트 추가` → `docs: 알림 컴포넌트 사용법 문서 작성` |
| 설정 변경 + 기능 추가 | `chore: ESLint 규칙 추가` → `feat: 다크모드 지원 추가` |

#### 분리하지 않는 경우

- 변경사항이 단일 목적의 소규모 수정인 경우 (파일 1~3개, 동일 타입)
- 분리하면 중간 커밋에서 빌드가 깨지거나 의미가 불완전해지는 경우
- 긴밀하게 결합된 변경 (예: API 엔드포인트와 그에 맞춘 프론트엔드 호출부)


### 3. 지라 티켓 번호 감지

현재 브랜치 이름에서 지라 티켓 번호를 자동 추출한다.

```bash
git branch --show-current
```

브랜치 이름에서 `[A-Z]+-\d+` 패턴(대문자 접두사 + 하이픈 + 숫자)을 추출한다. 예시:
- `feature/login-PI-1234` → `PI-1234`
- `fix/bug-CS-567` → `CS-567`
- `hotfix/LIVE-89` → `LIVE-89`

PI, CS, LIVE는 예시이며, 모든 대문자 프로젝트 접두사를 자동 감지한다.

### 4. 커밋 메시지 생성

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

- **커밋 메시지는 반드시 한국어로 작성한다** (type 키워드만 영어, description은 항상 한국어)
- 설명은 간결하게 작성한다 (50자 이내 권장)
- 명사형 종결을 사용한다 (예: "구현", "수정", "추가", "제거")
- 변경의 "무엇을"과 "왜"를 담는다
- 여러 파일이 변경된 경우 핵심 변경사항을 대표하는 메시지를 작성한다
- 한글로 커밋 메세지를 작성한다.

커밋 메시지 작성 상세 규칙은 `references/commit-conventions.md` 참조.

### 5. 커밋 실행 (커밋 계획 순서대로)

커밋 계획에서 확인받은 순서대로 각 커밋을 순차 실행한다.

```bash
# 커밋 1
git add <커밋1 관련 파일들>
git commit -m "{커밋1 메시지}"

# 커밋 2
git add <커밋2 관련 파일들>
git commit -m "{커밋2 메시지}"

# ... 계획된 커밋 수만큼 반복
```

- 각 커밋마다 해당 커밋에 속하는 파일만 명시적으로 스테이징한다
- `git add .` 보다는 관련 파일을 명시적으로 스테이징한다
- `.env`, credentials 등 민감한 파일은 제외한다
- 각 커밋은 그 자체로 완결성을 가져야 한다 (중간 커밋에서 빌드가 깨지면 안 됨)

### 6. 푸시 (확인 후)

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

