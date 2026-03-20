![챕터 05: 스킬 시스템](images/chapter-header.png)

> **만약 Copilot이 매번 설명하지 않아도 팀의 모범 사례를 자동으로 적용할 수 있다면 어떨까요?**

이 장에서는 *에이전트 스킬(Agent Skills)*을 배웁니다. 에이전트 스킬은 Copilot이 작업과 관련 있다고 판단할 때 **자동으로 로드하는 지침 폴더**입니다. 에이전트가 Copilot이 *어떻게* 생각하는지를 바꾼다면, 스킬은 Copilot이 *특정 작업을 완료하는 구체적인 방식*을 가르칩니다. 여러분은 “보안”을 묻는 순간마다 Copilot이 적용하는 보안 감사 스킬을 만들고, 일관된 코드 품질을 보장하는 팀 표준 리뷰 기준을 구축하며, Copilot CLI, VS Code, Copilot 코딩 에이전트 전반에서 스킬이 어떻게 동작하는지 학습합니다.


## 🎯 학습 목표

이 장을 마치면 다음을 할 수 있습니다.

- 에이전트 스킬이 어떻게 동작하는지, 언제 사용해야 하는지 이해한다
- `SKILL.md` 파일로 커스텀 스킬을 만든다
- 공유 저장소의 커뮤니티 스킬을 활용한다
- 스킬 vs 에이전트 vs MCP를 언제 선택해야 하는지 안다

> ⏱️ **예상 소요 시간**: 약 55분 (읽기 20분 + 실습 35분)

---

## 🧩 현실 세계 비유: 전동 공구

범용 드릴은 유용하지만, 특수한 부착물(비트/어댑터)을 끼우면 훨씬 강력해집니다.

<img src="images/power-tools-analogy.png" alt="전동 공구 - 스킬은 Copilot의 기능을 확장합니다" width="800"/>

스킬도 같은 방식으로 동작합니다. 드릴 비트를 작업에 맞게 바꾸듯, Copilot에 다양한 작업용 스킬을 추가할 수 있습니다.

| 스킬 부착물 | 목적 |
|------------|------|
| `commit` | 일관된 커밋 메시지 생성 |
| `security-audit` | OWASP 취약점 점검 |
| `generate-tests` | 포괄적인 pytest 테스트 생성 |
| `code-checklist` | 팀 코드 품질 표준 적용 |


*스킬은 Copilot이 할 수 있는 일을 확장하는 특수 부착물입니다*

---

# 스킬은 어떻게 동작하나요?

<img src="images/how-skills-work.png" alt="Copilot 스킬을 나타내는 RPG 스타일의 빛나는 스킬 아이콘들이 별 배경 위에서 연결된 이미지" width="800"/>

스킬이 무엇인지, 왜 중요한지, 그리고 에이전트 및 MCP와 어떻게 다른지 알아봅니다.

---

## *스킬이 처음이라면?* 여기서 시작하세요!

1. **이미 어떤 스킬이 있는지 확인하기:**
   ```bash
   copilot
   > /skills list
   ```
   이 명령은 Copilot이 프로젝트 폴더와 개인 폴더에서 찾을 수 있는 모든 스킬을 보여줍니다.

2. **실제 스킬 파일 보기:** 제공된 [code-checklist SKILL.md](../.github/skills/code-checklist/SKILL.md)를 확인해 패턴을 보세요. YAML 프런트매터 + 마크다운 지침으로 구성됩니다.

3. **핵심 개념 이해하기:** 스킬은 프롬프트가 스킬의 `description`과 매칭될 때 Copilot이 *자동으로 로드하는* 작업별 지침입니다. 매번 활성화할 필요 없이 자연스럽게 질문하면 됩니다.


## 스킬 이해하기

에이전트 스킬은 지침, 스크립트, 리소스를 담고 있는 폴더로, Copilot이 **작업과 관련 있다고 판단하면 자동으로 로드**합니다. Copilot은 프롬프트를 읽고, 매칭되는 스킬이 있는지 확인한 뒤, 관련 지침을 자동으로 적용합니다.

```bash
copilot

> Check books.py against our quality checklist
# Copilot이 이것이 "code-checklist" 스킬과 매칭된다고 판단하고
# 해당 파이썬 품질 체크리스트를 자동으로 적용

> Generate tests for the BookCollection class
# Copilot이 "pytest-gen" 스킬을 로드하고
# 선호하는 테스트 구조를 적용

> What are the code quality issues in this file?
# Copilot이 "code-checklist" 스킬을 로드하고
# 팀 표준에 따라 점검
```

> 💡 **핵심 인사이트**: 스킬은 프롬프트가 스킬의 설명과 매칭되면 **자동으로 트리거**됩니다. 자연스럽게 요청하면 Copilot이 뒤에서 관련 스킬을 적용합니다. 다음에는 스킬을 직접 호출하는 방법도 배웁니다.

> 🧰 **바로 쓸 수 있는 템플릿**: 간단히 복사/붙여넣기 해서 사용해볼 수 있는 스킬이 [.github/skills](../.github/skills/) 폴더에 있습니다.

### 슬래시 명령으로 직접 호출하기

자동 트리거가 스킬의 기본 동작 방식이지만, **스킬 이름을 슬래시 명령으로 직접 호출**할 수도 있습니다.

```bash
> /generate-tests 사용자 인증 모듈에 대한 테스트를 만들어줘

> /code-checklist books.py에서 코드 품질 문제를 확인해줘

> /security-audit API 엔드포인트의 취약점을 점검해줘
```

특정 스킬이 반드시 사용되길 원할 때, 이렇게 명시적으로 제어할 수 있습니다.

> 📝 **스킬 호출 vs 에이전트 호출**: 스킬 호출과 에이전트 호출을 혼동하지 마세요.
> - **스킬**: `/skill-name <prompt>` 예: `/code-checklist Check this file`
> - **에이전트**: `/agent`(목록에서 선택) 또는 `copilot --agent <name>`(CLI)
>
> 스킬과 에이전트가 같은 이름(예: `code-reviewer`)을 공유한다면, `/code-reviewer`는 **에이전트가 아니라 스킬**을 호출합니다.

### 스킬이 사용됐는지 어떻게 알 수 있나요?

Copilot에게 직접 물어볼 수 있습니다.

```bash
> What skills did you use for that response?

> What skills do you have available for security reviews?
```

### 스킬 vs 에이전트 vs MCP

스킬은 GitHub Copilot 확장성 모델의 한 부분입니다. 에이전트와 MCP 서버와 비교하면 다음과 같습니다.

> *MCP는 아직 걱정하지 않아도 됩니다. [챕터 06](../06-mcp-servers/)에서 다룹니다. 여기서는 스킬이 전체 그림에서 어디에 위치하는지 보기 위해 포함했습니다.*

<img src="images/skills-agents-mcp-comparison.png" alt="에이전트, 스킬, MCP 서버의 차이와 워크플로에서의 결합 방식을 비교한 다이어그램" width="800"/>

| 기능 | 무엇을 하나요 | 언제 쓰나요 |
|------|---------------|------------|
| **에이전트(Agents)** | AI가 생각하는 방식을 바꿈 | 다양한 작업 전반에 걸친 전문성이 필요할 때 |
| **스킬(Skills)** | 작업별 구체 지침 제공 | 반복 가능한 특정 작업에 대한 상세한 절차가 필요할 때 |
| **MCP** | 외부 서비스 연결 | API에서 실시간 데이터가 필요할 때 |

광범위한 전문성은 에이전트, 구체적인 작업 지침은 스킬, 외부 데이터는 MCP가 담당합니다. 에이전트는 대화 중 하나 이상의 스킬을 함께 사용할 수 있습니다. 예를 들어 코드를 점검해달라고 요청하면, `security-audit` 스킬과 `code-checklist` 스킬을 모두 자동으로 적용할 수 있습니다.

> 📚 **더 알아보기**: 스킬 포맷과 모범 사례의 전체 참조는 공식 문서 [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)를 참고하세요.

---

## 수동 프롬프트에서 자동 전문성으로

스킬을 만드는 방법을 배우기 전에, *왜* 스킬이 가치 있는지 먼저 보겠습니다. 일관성 향상을 체감하면 “방법”이 더 쉽게 이해됩니다.

### 스킬 이전: 들쑥날쑥한 리뷰

코드 리뷰를 할 때마다 무언가를 놓치기 쉽습니다.

```bash
copilot

> Review this code for issues
# 일반적인 리뷰 - 팀의 구체적인 관심사를 놓칠 수 있음
```

혹은 매번 긴 프롬프트를 작성합니다.

```bash
> Review this code checking for bare except clauses, missing type hints,
> mutable default arguments, missing context managers for file I/O,
> functions over 50 lines, print statements in production code...
```

시간: 타이핑 **30초+**. 일관성: **기억에 따라 들쑥날쑥**.

### 스킬 이후: 모범 사례 자동 적용

`code-checklist` 스킬이 설치되어 있다면, 자연스럽게 이렇게만 요청하면 됩니다.

```bash
copilot

> Check the book collection code for quality issues
```

**뒤에서 일어나는 일**:
1. Copilot이 프롬프트에서 "code quality"와 "issues" 같은 키워드를 감지
2. 스킬 설명을 확인하고 `code-checklist` 스킬이 매칭됨을 발견
3. 팀의 품질 체크리스트를 자동 로드
4. 목록을 직접 나열하지 않아도 모든 점검 항목을 적용

<img src="images/skill-auto-discovery-flow.png" alt="스킬 자동 트리거 - Copilot이 프롬프트를 적절한 스킬과 자동으로 매칭하는 4단계 흐름" width="800"/>

*자연스럽게 질문하기만 하면 됩니다. Copilot이 프롬프트를 적절한 스킬과 매칭해 자동으로 적용합니다.*

**출력 예시**:
```
## 코드 체크리스트: books.py

### 코드 품질
- [PASS] 모든 함수에 타입 힌트가 있음
- [PASS] bare except 절이 없음
- [PASS] mutable 기본 인자가 없음
- [PASS] 파일 I/O에 컨텍스트 매니저 사용
- [PASS] 함수가 50줄 이하
- [PASS] 변수/함수 이름이 PEP 8을 따름

### 입력 검증
- [FAIL] 사용자 입력이 검증되지 않음 - add_book()이 어떤 연도 값이든 받음
- [FAIL] 엣지 케이스가 완전히 처리되지 않음 - 제목/저자에 빈 문자열 허용
- [PASS] 오류 메시지가 명확하고 도움이 됨

### 테스트
- [FAIL] 해당 pytest 테스트가 없음

### 요약
병합 전에 3개 항목을 수정해야 함
```

**차이점**: 팀의 표준이 매번, 자동으로, 프롬프트를 길게 쓰지 않아도 적용됩니다.

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![Skill Trigger Demo](images/skill-trigger-demo.gif)

*데모 출력은 상황에 따라 달라질 수 있습니다. 모델/도구/응답은 예시와 다를 수 있습니다.*

</details>

---

## 규모 있는 일관성: 팀 PR 리뷰 스킬

팀에 10개 항목의 PR 체크리스트가 있다고 해봅시다. 스킬이 없다면 모든 개발자가 10개를 외워야 하고, 누군가는 항상 하나를 빼먹습니다. `pr-review` 스킬이 있다면, 팀 전체가 일관된 리뷰를 얻습니다.

```bash
copilot

> Can you review this PR?
```

Copilot이 팀의 `pr-review` 스킬을 자동으로 로드하고 10개 항목을 모두 점검합니다.

```
PR 리뷰: feature/user-auth

## 보안 ✅
- 하드코딩된 시크릿 없음
- 입력 검증 존재
- bare except 절 없음

## 코드 품질 ⚠️
- [WARN] 45행의 print 문 - 병합 전 제거
- [WARN] 78행의 TODO에 이슈 참조가 없음
- [WARN] 공개 함수에 타입 힌트 누락

## 테스트 ✅
- 새 테스트 추가됨
- 엣지 케이스 포함

## 문서 ❌
- [FAIL] BREAKING CHANGE가 CHANGELOG에 기록되지 않음
- [FAIL] API 변경에 OpenAPI 스펙 업데이트 필요
```

**효과**: 모든 팀원이 동일한 기준을 자동으로 적용합니다. 신규 입사자도 체크리스트를 외울 필요가 없습니다. 스킬이 대신해주니까요.

---

# 커스텀 스킬 만들기

<img src="images/creating-managing-skills.png" alt="스킬 생성 및 관리 - 빛나는 LEGO 블록처럼 스킬을 쌓는 사람과 로봇 손" width="800"/>

`SKILL.md` 파일로 스킬을 직접 만들어봅니다.

---

## 스킬 위치

스킬은 `.github/skills/`(프로젝트 단위) 또는 `~/.copilot/skills/`(사용자 단위)에 저장됩니다.

### Copilot이 스킬을 찾는 방법

Copilot은 다음 위치를 자동으로 스캔해 스킬을 찾습니다.

| 위치 | 범위 |
|------|------|
| `.github/skills/` | 프로젝트 단위(깃으로 팀과 공유) |
| `~/.copilot/skills/` | 사용자 단위(개인 스킬) |

### 스킬 구조

각 스킬은 `SKILL.md` 파일이 들어 있는 독립 폴더에 위치합니다. 선택적으로 스크립트/예제/리소스도 포함할 수 있습니다.

```
.github/skills/
└── my-skill/
    ├── SKILL.md           # 필수: 스킬 정의 및 지침
    ├── examples/          # 선택: Copilot이 참고할 예제 파일
    │   └── sample.py
    └── scripts/           # 선택: 스킬에서 사용할 스크립트
        └── validate.sh
```

> 💡 **팁**: 디렉터리 이름은 `SKILL.md`의 `name` 값(하이픈 사용 소문자)과 일치하는 것이 좋습니다.

### SKILL.md 형식

스킬은 YAML 프런트매터가 있는 간단한 마크다운 형식을 사용합니다.

```markdown
---
name: code-checklist
description: 보안, 성능, 유지보수성 점검을 포함한 포괄적인 코드 품질 체크리스트
license: MIT
---

# 코드 체크리스트

코드를 점검할 때 다음을 확인합니다.

## 보안
- SQL 인젝션 취약점
- XSS 취약점
- 인증/인가 이슈
- 민감 데이터 노출

## 성능
- N+1 쿼리 문제(항목마다 쿼리 1개가 아니라 전체를 위한 쿼리 1개를 쓰지 않는 경우)
- 불필요한 루프/계산
- 메모리 누수
- 블로킹 작업

## 유지보수성
- 함수 길이(50줄 초과 함수 플래그)
- 코드 중복
- 에러 처리 누락
- 불명확한 네이밍

## 출력 형식
심각도와 함께 번호 매긴 목록으로 이슈를 제공합니다.
- [CRITICAL] - 병합 전 반드시 수정
- [HIGH] - 병합 전 수정 권장
- [MEDIUM] - 곧 처리 필요
- [LOW] - 있으면 좋은 개선
```

**YAML 속성:**

| 속성 | 필수 | 설명 |
|------|------|------|
| `name` | **예** | 고유 식별자(소문자, 공백 대신 하이픈) |
| `description` | **예** | 스킬이 무엇을 하는지, 언제 로드되어야 하는지 |
| `license` | 아니오 | 이 스킬에 적용되는 라이선스 |

> 📖 **공식 문서**: [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)

### 첫 스킬 만들기

OWASP Top 10 취약점을 점검하는 보안 감사 스킬을 만들어봅시다.

```bash
# 스킬 디렉터리 생성
mkdir -p .github/skills/security-audit

# SKILL.md 파일 생성
cat > .github/skills/security-audit/SKILL.md << 'EOF'
---
name: security-audit
description: OWASP(오픈 웹 애플리케이션 보안 프로젝트) Top 10 취약점을 점검하는 보안 중심 코드 리뷰
---

# Security Audit

다음을 확인하는 보안 감사를 수행합니다.

## 인젝션 취약점
- SQL 인젝션(쿼리에 문자열을 이어붙이는 경우)
- 커맨드 인젝션(정제되지 않은 쉘 명령)
- LDAP 인젝션
- XPath 인젝션

## 인증 이슈
- 하드코딩된 자격 증명
- 약한 비밀번호 요구사항
- 레이트 리밋 미설정
- 세션 관리 결함

## 민감 데이터
- 평문 비밀번호
- 코드 내 API 키
- 민감 정보를 로깅
- 암호화 누락

## 접근 제어
- 인가 체크 누락
- IDOR(불안전한 직접 객체 참조)
- 경로 순회 취약점

## 출력
발견한 각 이슈에 대해 다음을 제공합니다.
1. 파일과 라인 번호
2. 취약점 유형
3. 심각도(CRITICAL/HIGH/MEDIUM/LOW)
4. 권장 수정 방법
EOF

# 스킬 테스트(스킬은 프롬프트에 따라 자동으로 로드됨)
copilot

> @samples/book-app-project/ Check this code for security vulnerabilities
# Copilot이 "security vulnerabilities"가 스킬과 매칭된다고 판단하고
# OWASP 체크리스트를 자동 적용
```

**예상 출력**(결과는 달라질 수 있음):
```
Security Audit: book-app-project

[HIGH] 하드코딩된 파일 경로 (book_app.py, 12행)
  파일 경로가 설정 가능하지 않고 하드코딩되어 있음
  수정: 환경 변수 또는 설정 파일 사용

[MEDIUM] 입력 검증 없음 (book_app.py, 34행)
  사용자 입력이 정제 없이 함수로 전달됨
  수정: 처리 전에 입력 검증 추가

✅ SQL 인젝션 없음
✅ 하드코딩된 자격 증명 없음
```

---

## 좋은 스킬 설명 작성하기

`SKILL.md`의 `description` 필드는 매우 중요합니다. Copilot이 스킬을 로드할지 결정하는 기준이기 때문입니다.

```markdown
---
name: security-audit
description: 보안 리뷰, 취약점 스캐닝, SQL 인젝션, XSS, 인증 이슈,
  OWASP Top 10 취약점을 점검하는 보안 모범 사례 점검에 사용
---
```

> 💡 **팁**: 평소 질문할 때 쓰는 키워드를 설명에 넣으세요. "security review"라고 자주 말한다면 `description`에도 "security review"를 넣는 식입니다.

### 에이전트와 스킬 결합하기

스킬과 에이전트는 함께 작동합니다. 에이전트는 전문성을 제공하고, 스킬은 구체적인 지침을 제공합니다.

```bash
# code-reviewer 에이전트로 시작
copilot --agent code-reviewer

> Check the book app for quality issues
# code-reviewer 에이전트의 전문성과
# code-checklist 스킬의 체크리스트가 결합
```

---

# 스킬 관리 및 공유

설치된 스킬을 확인하고, 커뮤니티 스킬을 찾고, 직접 공유하는 방법을 알아봅니다.

<img src="images/managing-sharing-skills.png" alt="스킬 관리 및 공유 - CLI 스킬의 발견, 사용, 생성, 공유 사이클" width="800" />

---

## `/skills` 명령으로 스킬 관리하기

`/skills` 명령을 사용해 설치된 스킬을 관리할 수 있습니다.

| 명령 | 하는 일 |
|------|---------|
| `/skills list` | 설치된 모든 스킬 표시 |
| `/skills info <name>` | 특정 스킬 상세 정보 보기 |
| `/skills add <name>` | 스킬 활성화(저장소/마켓플레이스에서) |
| `/skills remove <name>` | 스킬 비활성화 또는 제거 |
| `/skills reload` | `SKILL.md` 수정 후 스킬 다시 로드 |

> 💡 **기억하세요**: 매 프롬프트마다 스킬을 "활성화"할 필요는 없습니다. 설치만 되어 있으면 프롬프트가 설명과 매칭될 때 **자동으로 트리거**됩니다. 이 명령들은 스킬을 사용하는 것이 아니라, *어떤 스킬을 사용할 수 있게 할지*를 관리하기 위한 것입니다.

### 예시: 내 스킬 보기

```bash
copilot

> /skills list

Available skills:
- security-audit: Security-focused code review checking OWASP Top 10
- generate-tests: Generate comprehensive unit tests with edge cases
- code-checklist: Team code quality checklist
...

> /skills info security-audit

Skill: security-audit
Source: Project
Location: .github/skills/security-audit/SKILL.md
Description: Security-focused code review checking OWASP Top 10 vulnerabilities
```

---

<details>
<summary>실제 동작 보기!</summary>

![List Skills Demo](images/list-skills-demo.gif)

*데모 출력은 상황에 따라 달라질 수 있습니다. 모델/도구/응답은 예시와 다를 수 있습니다.*

</details>

---

### `/skills reload`는 언제 쓰나요?

스킬의 `SKILL.md`를 생성/수정한 뒤에는 Copilot을 재시작하지 않고 변경 사항을 반영하기 위해 `/skills reload`를 실행합니다.

```bash
# 스킬 파일을 편집한 뒤
# Copilot에서:
> /skills reload
Skills reloaded successfully.
```

> 💡 **참고**: `/compact`로 대화 기록을 요약해도 스킬은 계속 유효합니다. compact 후에 reload 할 필요는 없습니다.

---

## 커뮤니티 스킬 찾기 및 사용하기

### 플러그인으로 스킬 설치하기

> 💡 **플러그인이란?** 플러그인은 스킬, 에이전트, MCP 서버 설정을 묶어서 배포할 수 있는 설치 가능한 패키지입니다. Copilot CLI의 “앱 스토어” 확장이라고 생각하면 됩니다.

`/plugin` 명령으로 패키지를 탐색하고 설치할 수 있습니다.

```bash
copilot

> /plugin list
# 설치된 플러그인 표시

> /plugin marketplace
# 사용 가능한 플러그인 탐색

> /plugin install <plugin-name>
# 마켓플레이스에서 플러그인 설치
```

플러그인은 여러 기능을 묶을 수 있습니다. 하나의 플러그인이 관련 스킬/에이전트/MCP 서버 설정을 함께 포함할 수도 있습니다.

### 커뮤니티 스킬 저장소

미리 만들어진 스킬은 커뮤니티 저장소에서도 찾을 수 있습니다.

- **[Awesome Copilot](https://github.com/github/awesome-copilot)** - 스킬 문서 및 예시를 포함한 공식 GitHub Copilot 리소스

### 커뮤니티 스킬을 수동으로 설치하기

GitHub 저장소에서 스킬을 찾았다면, 해당 폴더를 스킬 디렉터리로 복사하면 됩니다.

```bash
# awesome-copilot 저장소 클론
git clone https://github.com/github/awesome-copilot.git /tmp/awesome-copilot

# 특정 스킬을 프로젝트에 복사
cp -r /tmp/awesome-copilot/skills/code-checklist .github/skills/

# 또는 모든 프로젝트에 공통으로 쓰는 개인 스킬로 복사
cp -r /tmp/awesome-copilot/skills/code-checklist ~/.copilot/skills/
```

> ⚠️ **설치 전 검토**: 스킬을 복사하기 전에 반드시 `SKILL.md`를 읽어보세요. 스킬은 Copilot의 동작을 제어하며, 악성 스킬은 유해한 명령 실행이나 예상치 못한 코드 변경을 지시할 수 있습니다.

---

# 실습

<img src="../images/practice.png" alt="실습을 위한 따뜻한 책상 환경 - 코드가 보이는 모니터, 스탠드, 커피컵, 헤드폰" width="800"/>

배운 내용을 바탕으로 스킬을 직접 만들고 테스트해봅니다.

---

## ▶️ 직접 해보기

### 더 많은 스킬 만들기

아래 두 스킬은 서로 다른 패턴을 보여줍니다. 앞서 "첫 스킬 만들기"에서 했던 `mkdir` + `cat` 워크플로를 그대로 따라 하거나, 스킬을 적절한 위치에 복사/붙여넣기 하세요. 더 많은 예시는 [.github/skills](../.github/skills)에 있습니다.

### pytest 테스트 생성 스킬

코드베이스 전반에서 일관된 pytest 구조를 보장하는 스킬입니다.

```bash
mkdir -p .github/skills/pytest-gen

cat > .github/skills/pytest-gen/SKILL.md << 'EOF'
---
name: pytest-gen
description: 픽스처와 엣지 케이스를 포함한 포괄적인 pytest 테스트를 생성
---

# pytest 테스트 생성

다음을 포함하는 pytest 테스트를 생성합니다.

## 테스트 구조
- pytest 규칙 사용(test_ 접두사)
- 가능하면 테스트당 assertion 1개
- 기대 동작을 설명하는 명확한 테스트 이름
- setup/teardown을 위해 fixture 사용

## 커버리지
- 정상 시나리오
- 엣지 케이스: None, 빈 문자열, 빈 리스트
- 경계값
- pytest.raises()를 사용한 오류 시나리오

## 픽스처
- 재사용 가능한 테스트 데이터를 위해 @pytest.fixture 사용
- 파일 작업에는 tmpdir/tmp_path 사용
- pytest-mock으로 외부 의존성 모킹

## 출력
올바른 import를 포함한, 실행 가능한 전체 테스트 파일을 제공합니다.
EOF
```

### 팀 PR 리뷰 스킬

팀 전반에 일관된 PR 리뷰 기준을 적용하는 스킬입니다.

```bash
mkdir -p .github/skills/pr-review

cat > .github/skills/pr-review/SKILL.md << 'EOF'
---
name: pr-review
description: 팀 표준 PR 리뷰 체크리스트
---

# PR 리뷰

팀 표준에 따라 변경 사항을 리뷰합니다.

## 보안 체크리스트
- [ ] 하드코딩된 시크릿 또는 API 키 없음
- [ ] 모든 사용자 데이터에 입력 검증
- [ ] bare except 절 없음
- [ ] 로그에 민감 데이터 없음

## 코드 품질
- [ ] 함수 50줄 이하
- [ ] 프로덕션 코드에 print 문 없음
- [ ] 공개 함수에 타입 힌트
- [ ] 파일 I/O에 컨텍스트 매니저 사용
- [ ] 이슈 참조 없는 TODO 없음

## 테스트
- [ ] 새 코드에 테스트가 있음
- [ ] 엣지 케이스 포함
- [ ] 설명 없는 skipped 테스트 없음

## 문서
- [ ] API 변경 문서화
- [ ] BREAKING CHANGE 기록
- [ ] 필요 시 README 업데이트

## 출력 형식
결과를 다음과 같이 제공합니다.
- ✅ PASS: 문제가 없어 보이는 항목
- ⚠️ WARN: 개선 가능 항목
- ❌ FAIL: 병합 전 반드시 수정해야 하는 항목
EOF
```

### 더 해보기

1. **스킬 생성 챌린지**: 3가지 항목만 빠르게 점검하는 `quick-review` 스킬을 만들어보세요.
   - bare except 절
   - 타입 힌트 누락
   - 불명확한 변수 이름

   "Do a quick review of books.py"라고 요청해 테스트합니다.

2. **스킬 비교**: 수동으로 상세한 보안 리뷰 프롬프트를 작성하는 데 걸리는 시간을 재보세요. 그 다음 "Check for security issues in this file"이라고만 요청해 `security-audit` 스킬이 자동 로드되게 해보세요. 스킬이 시간을 얼마나 절약해줬나요?

3. **팀 스킬 챌린지**: 팀의 코드 리뷰 체크리스트를 떠올려 보세요. 스킬로 인코딩할 수 있을까요? 스킬이 항상 확인해야 할 3가지 항목을 적어보세요.

**셀프 체크**: `description` 필드가 왜 중요한지(스킬 로딩의 기준) 설명할 수 있나요?

---

## 📝 과제

### 메인 챌린지: 도서 요약 스킬 만들기

앞의 예시는 `pytest-gen`과 `pr-review` 스킬을 만들었습니다. 이제 완전히 다른 종류의 스킬, 즉 데이터로부터 형식화된 출력(포맷)을 만드는 스킬을 연습해봅니다.

1. 현재 스킬 목록 보기: Copilot을 실행하고 `/skills list`를 입력하세요. 또는 프로젝트 스킬은 `ls .github/skills/`, 개인 스킬은 `ls ~/.copilot/skills/`로 확인할 수 있습니다.
2. `.github/skills/book-summary/SKILL.md`에 `book-summary` 스킬을 만들어 도서 컬렉션의 형식화된 마크다운 요약을 생성하세요.
3. 스킬은 다음을 포함해야 합니다.
   - 명확한 name과 description(매칭을 위해 description이 매우 중요!)
   - 구체적인 포맷 규칙(예: 제목/저자/연도/읽음 여부를 포함한 마크다운 테이블)
   - 출력 규칙(예: 읽음 여부는 ✅/❌ 사용, 연도 기준 정렬)
4. 스킬 테스트: `@samples/book-app-project/data.json Summarize the books in this collection`
5. `/skills list`로 스킬 자동 트리거를 확인
6. `/book-summary Summarize the books in this collection`로 직접 호출도 시도

**성공 기준**: 도서 컬렉션을 물어볼 때 Copilot이 자동으로 적용하는 `book-summary` 스킬을 만들었다.

<details>
<summary>💡 힌트(클릭하여 펼치기)</summary>

**스타터 템플릿**: `.github/skills/book-summary/SKILL.md` 만들기

```markdown
---
name: book-summary
description: 도서 컬렉션의 형식화된 마크다운 요약을 생성
---

# 도서 요약 생성기

다음 규칙에 따라 도서 컬렉션의 요약을 생성합니다.

1. Title, Author, Year, Status 컬럼을 가진 마크다운 테이블 출력
2. 읽은 책은 ✅, 안 읽은 책은 ❌ 사용
3. 연도 기준 정렬(오래된 순)
4. 마지막에 총 개수 포함
5. 데이터 문제(저자 누락, 잘못된 연도 등)를 플래그

예:
| Title | Author | Year | Status |
|-------|--------|------|--------|
| 1984 | George Orwell | 1949 | ✅ |
| Dune | Frank Herbert | 1965 | ❌ |

**총계: 2권(읽음 1, 안 읽음 1)**
```

**테스트:**
```bash
copilot
> @samples/book-app-project/data.json Summarize the books in this collection
# description 매칭에 따라 스킬이 자동 트리거되어야 함
```

**트리거되지 않으면:** `/skills reload` 후 다시 질문하세요.

</details>

### 보너스 챌린지: 커밋 메시지 스킬

1. conventional commit 메시지를 일관된 형식으로 생성하는 `commit-message` 스킬 만들기
2. 변경 사항을 stage 한 뒤 "Generate a commit message for my staged changes"로 테스트
3. 스킬을 문서화하고 `copilot-skill` 토픽으로 GitHub에 공유

---

<details>
<summary>🔧 <strong>자주 하는 실수 & 문제 해결</strong> (클릭하여 펼치기)</summary>

### 흔한 실수

| 실수 | 발생하는 일 | 해결 |
|------|------------|------|
| 파일 이름을 `SKILL.md`가 아닌 다른 이름으로 만듦 | 스킬이 인식되지 않음 | 파일 이름은 정확히 `SKILL.md`여야 함 |
| `description`이 모호�� | 자동으로 로드되지 않음 | description은 *주요 발견 메커니즘*. 트리거 키워드를 구체적으로 포함 |
| 프런트매터에 `name` 또는 `description` 누락 | 스킬 로드 실패 | YAML에 둘 다 추가 |
| 폴더 위치가 잘못됨 | 스킬을 찾지 못함 | `.github/skills/skill-name/`(프로젝트) 또는 `~/.copilot/skills/skill-name/`(개인) 사용 |

### 트러블슈팅

**스킬이 사용되지 않을 때** - Copilot이 기대한 스킬을 쓰지 않는다면:

1. **description 확인**: 내가 묻는 방식과 매칭되나요?
   ```markdown
   # 나쁨: 너무 모호함
   description: Reviews code

   # 좋음: 트리거 키워드 포함
   description: Use for code reviews, checking code quality,
     finding bugs, security issues, and best practice violations
   ```

2. **파일 위치 확인**:
   ```bash
   # 프로젝트 스킬
   ls .github/skills/

   # 개인 스킬
   ls ~/.copilot/skills/
   ```

3. **SKILL.md 형식 확인**: 프런트매터는 필수입니다.
   ```markdown
   ---
   name: skill-name
   description: What the skill does and when to use it
   ---
   
   # Instructions here
   ```

**스킬이 목록에 안 보일 때** - 폴더 구조를 확인하세요.
```
.github/skills/
└── my-skill/           # 폴더 이름
    └── SKILL.md        # SKILL.md(대소문자 구분, 정확히)
```

`/skills reload`를 실행해 수정 사항이 반영되게 하세요.

**스킬이 로드되는지 테스트하기** - Copilot에게 직접 물어보세요.
```bash
> What skills do you have available for checking code quality?
# Copilot이 찾은 관련 스킬을 설명함
```

**스킬이 실제로 작동하는지 어떻게 알까요?**

1. **출력 형식 확인**: 스킬이 `[CRITICAL]` 같은 출력 규칙을 지정했다면 응답에 나타나는지 확인
2. **직접 질문**: 응답 후 "Did you use any skills for that?"라고 질문
3. **비교 테스트**: 동일한 프롬프트를 `--no-custom-instructions`로 실행해 차이를 확인
   ```bash
   # 스킬 적용
   copilot --allow-all -p "Review @file.py for security issues"

   # 스킬 미적용(베이스라인 비교)
   copilot --allow-all -p "Review @file.py for security issues" --no-custom-instructions
   ```
4. **특정 체크 확인**: 예를 들어 "functions over 50 lines" 같은 체크가 응답에 포함되는지 확인

</details>

---

# 요약

## 🔑 핵심 정리

1. **스킬은 자동**: 프롬프트가 스킬의 description과 매칭되면 Copilot이 자동 로드
2. **직접 호출 가능**: `/skill-name`으로 슬래시 명령 호출도 가능
3. **SKILL.md 형식**: YAML 프런트매터(name, description, optional license) + 마크다운 지침
4. **위치가 중요**: 팀 공유는 `.github/skills/`, 개인용은 `~/.copilot/skills/`
5. **description이 핵심**: 자연스럽게 묻는 방식과 맞는 키워드를 description에 포함

> 📋 **빠른 참고**: 전체 명령 및 단축키는 [GitHub Copilot CLI 명령 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참고하세요.

---

## ➡️ 다음은?

스킬은 자동 로드 지침으로 Copilot의 능력을 확장합니다. 그렇다면 외부 서비스에 연결하는 것은 어떨까요? 그게 바로 MCP입니다.

**[챕터 06: MCP 서버](../06-mcp-servers/README.md)**에서 다음을 배웁니다.

- MCP(Model Context Protocol)가 무엇인지
- GitHub, 파일 시스템, 문서 서비스 연결
- MCP 서버 구성
- 멀티 서버 워크플로

---

**[← 챕터 04로 돌아가기](../04-agents-custom-instructions/README.md)** | **[챕터 06으로 계속 →](../06-mcp-servers/README.md)"