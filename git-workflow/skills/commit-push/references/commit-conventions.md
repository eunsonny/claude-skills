# Conventional Commits 상세 규칙

## 커밋 메시지 구조

```
[Jira Issue ID] {type}[optional scope]: {description}

[optional body]

[optional footer(s)]
```

## 타입별 상세 가이드

### feat
새로운 기능을 추가할 때 사용한다.

```
PI-1234 feat: 소셜 로그인 기능 추가
PI-1234 feat(auth): 카카오 OAuth 연동 구현
```

### fix
버그를 수정할 때 사용한다.

```
CS-567 fix: 목록 페이지 무한 스크롤 중복 로딩 수정
CS-567 fix(api): 토큰 만료 시 자동 갱신 실패 수정
```

### docs
문서만 변경할 때 사용한다.

```
docs: API 엔드포인트 문서 업데이트
docs(readme): 설치 가이드 추가
```

### style
코드 동작에 영향을 주지 않는 변경 (포맷팅, 세미콜론 등).

```
style: 코드 포맷팅 통일
style(lint): ESLint 규칙 적용
```

### refactor
기능 변경 없이 코드 구조를 개선할 때 사용한다.

```
refactor: 사용자 서비스 레이어 분리
refactor(hooks): useAuth 훅 로직 단순화
```

### test
테스트 코드를 추가하거나 수정할 때 사용한다.

```
test: 결제 모듈 단위 테스트 추가
test(e2e): 로그인 플로우 E2E 테스트 작성
```

### chore
빌드, 설정, 패키지 관련 변경 시 사용한다.

```
chore: 패키지 의존성 업데이트
chore(ci): GitHub Actions 워크플로우 수정
```

### perf
성능을 개선할 때 사용한다.

```
perf: 이미지 lazy loading 적용
perf(query): N+1 쿼리 문제 해결
```

### hotfix
긴급 수정 시 사용한다.

```
LIVE-89 hotfix: 결제 API 타임아웃 임계값 조정
```

## Scope (선택사항)

괄호 안에 변경 범위를 명시할 수 있다.

```
feat(auth): ...
fix(api): ...
refactor(hooks): ...
```

일반적인 scope 예시:
- `auth` - 인증 관련
- `api` - API 관련
- `ui` - UI 컴포넌트 관련
- `db` - 데이터베이스 관련
- `config` - 설정 관련

## Body (선택사항)

변경 이유나 상세 설명이 필요한 경우 본문을 추가한다. 제목과 본문 사이에 빈 줄을 넣는다.

```
PI-1234 feat: 비밀번호 정책 강화

보안 감사 결과에 따라 비밀번호 최소 길이를 8자에서 12자로 변경하고,
특수문자 필수 포함 조건을 추가함.
```

## Breaking Changes

하위 호환성이 깨지는 변경은 `!`를 타입 뒤에 붙이거나 footer에 명시한다.

```
PI-1234 feat!: 인증 API 응답 구조 변경

BREAKING CHANGE: /api/auth/login 응답에서 token 필드가
accessToken과 refreshToken으로 분리됨.
```

## 한국어 작성 규칙

1. **명사형 종결**: "~구현", "~수정", "~추가", "~제거", "~변경", "~개선"
2. **간결하게**: 제목은 50자 이내
3. **구체적으로**: "버그 수정" ❌ → "목록 정렬 시 null 값 처리 수정" ✅
4. **현재형**: "수정했음" ❌ → "수정" ✅
