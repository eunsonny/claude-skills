---
name: create-pr
description: This skill should be used when the user asks to "PR 올려줘", "PR 만들어줘", "풀리퀘스트 생성해줘", "코드 리뷰 올려줘", "create pr", "open pr", "pull request", or wants to create a GitHub pull request. Automatically detects uncommitted changes and uses commit-push skill first if needed. Generates PR title with Jira ticket detection and summarized description in Korean.
version: 0.1.0
---

# Create PR

현재 브랜치의 변경사항을 확인하여 커밋/푸시 후 GitHub PR을 생성하는 스킬.

## Workflow

### 1. 미커밋 변경사항 확인

```bash
git status
git diff --staged
git diff
```

- staged 또는 unstaged 변경사항이 있으면 → commit-push 스킬을 호출하여 커밋 및 푸시를 먼저 수행한다
- 변경사항이 없으면 → 다음 단계로 진행한다

### 2. 현재 브랜치 확인

```bash
git branch --show-current
```

- main 또는 master 브랜치인 경우 → 중단하고 사용자에게 안내한다: "PR은 피처 브랜치에서 생성해야 합니다"
- 피처 브랜치인 경우 → 다음 단계로 진행한다

### 3. base 브랜치 자동 감지

```bash
git remote show origin
```

원격 저장소의 HEAD 브랜치(main 또는 master)를 자동으로 감지하여 base 브랜치로 사용한다.

### 4. 리모트 동기화 확인

로컬에만 있는 커밋이 있는지 확인한다.

```bash
git log origin/{현재 브랜치}..HEAD --oneline
```

- 푸시되지 않은 커밋이 있으면 → 푸시를 실행한다
- 원격 브랜치가 없는 경우:
  ```bash
  git push -u origin {현재 브랜치}
  ```
- 원격 브랜치가 있는 경우:
  ```bash
  git push origin {현재 브랜치}
  ```

### 5. 지라 티켓 감지

현재 브랜치 이름에서 `[A-Z]+-\d+` 패턴(대문자 접두사 + 하이픈 + 숫자)을 추출한다.

예시:
- `feature/login-PI-1234` → `PI-1234`
- `fix/bug-CS-567` → `CS-567`
- `hotfix/LIVE-89` → `LIVE-89`

### 6. PR 제목 생성

base 브랜치 이후의 커밋 히스토리를 분석하여 PR 제목을 생성한다.

```bash
git log {base}..HEAD --oneline
```

**지라 티켓이 있는 경우:**
```
{Jira Issue ID} {커밋 기반 설명}
```

**지라 티켓이 없는 경우:**
```
{커밋 기반 설명}
```

제목 생성 규칙:
- 커밋이 하나면 해당 커밋 메시지를 기반으로 생성한다
- 커밋이 여러 개면 전체 변경의 핵심을 요약하여 생성한다
- 한국어로 간결하게 작성한다

### 7. PR 본문 생성

base 브랜치 이후의 커밋과 diff를 분석하여 변경사항을 요약한다.

```bash
git diff {base}...HEAD
git log {base}..HEAD --oneline
```

본문 형식:
```markdown
## Summary
- 주요 변경사항 요약 (bullet points)

## Changes
- 세부 변경사항 나열
```

### 8. PR 생성

```bash
gh pr create --title "{PR 제목}" --body "{PR 본문}" --base {base 브랜치}
```

PR URL을 사용자에게 반환한다.

## 주의사항

- `gh` CLI가 설치 및 인증되어 있어야 한다
- force push는 절대 사용하지 않는다
- main/master 브랜치에서는 PR을 생성하지 않는다
- draft PR은 기본 제공하지 않되, 사용자가 명시적으로 요청하면 `--draft` 플래그를 추가한다
- 이미 해당 브랜치에 열린 PR이 있는지 확인하고, 있으면 사용자에게 알린다
