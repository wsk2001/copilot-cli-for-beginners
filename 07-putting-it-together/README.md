![Chapter 07: Putting It All Together](images/chapter-header.png)

**지금까지 배운 모든 것을 여기서 활용하세요. 아이디어 구상부터 PR 병합까지 단 한 번의 세션으로 완료할 수 있습니다.**

이 장에서는 지금까지 배운 모든 내용을 통합하여 완벽한 워크플로우를 구축합니다. 다중 에이전트 협업을 사용하여 기능을 개발하고, 커밋 전에 보안 문제를 감지하는 사전 커밋 훅을 설정하고, Copilot을 CI/CD 파이프라인에 통합하고, 기능 아이디어 구상부터 PR 병합까지 단 한 번의 터미널 세션으로 완료하는 방법을 배웁니다. GitHub Copilot CLI가 진정한 시너지 효과를 발휘하는 부분입니다.

💡 **참고**: 이 장에서는 지금까지 배운 모든 내용을 통합하는 방법을 보여줍니다. **생산성을 확보하기 위해 에이전트, 스킬 또는 MCP가 반드시 필요한 것은 아닙니다(물론 매우 유용할 수 있습니다).** 핵심 워크플로우(설명, 계획, 구현, 테스트, 검토, 배포)는 0장부터 3장까지의 내장 기능만으로도 작동합니다.

## 🎯 학습 목표

이 장을 마치면 다음을 수행할 수 있게 됩니다.

- 에이전트, 스킬 및 MCP(모델 컨텍스트 프로토콜)를 통합 워크플로로 결합
- 다양한 도구를 활용하여 완벽한 기능 구축
- 훅을 사용하여 기본적인 자동화 설정
- 전문성 개발을 위한 모범 사례 적용

> ⏱️ **예상 소요 시간**: 약 75분 (읽기 15분 + 실습 60분)
---

## 🧩 실제 사례: 오케스트라

<img src="images/orchestra-analogy.png" alt="오케스트라 비유 - 통합 워크플로" width="800"/>

심포니 오케스트라는 여러 파트로 구성됩니다.
- **현악기**는 기반을 제공합니다(핵심 워크플로와 유사).
- **금관악기**는 힘을 더합니다(전문 지식을 갖춘 에이전트와 유사).
- **목관악기**는 색채를 더합니다(역량을 확장하는 스킬과 유사).

- **타악기**는 리듬을 유지합니다(MCP가 외부 시스템과 연결되는 것과 유사).

각 파트는 개별적으로는 한계가 있지만, 잘 지휘될 때 비로소 웅장한 소리를 만들어냅니다.

**이 장에서 바로 그 방법을 알려드립니다!**<br>
*지휘자가 오케스트라를 지휘하듯, 에이전트, 스킬, MCP를 통합 워크플로로 조율합니다.*

코드 수정, 테스트 생성, 코드 검토, 그리고 PR 생성까지 모든 작업을 한 번에 수행하는 시나리오를 살펴보면서 시작해 보겠습니다.

---

## 아이디어 구상부터 PR 병합까지 한 번의 세션으로 완료하기

편집기, 터미널, 테스트 러너, GitHub UI를 번갈아 가며 사용하고 매번 작업 내용을 잃어버리는 대신, 모든 도구를 하나의 터미널 세션에서 통합하여 사용할 수 있습니다. 이 패턴에 대한 자세한 설명은 아래 [통합 패턴](#the-integration-pattern-for-power-users) 섹션에서 다루겠습니다.

```bash
# Copilot을 대화형 모드로 시작
copilot

> 읽지 않은 책 목록 보기 기능을 도서 앱에 추가하고 싶습니다.
> 읽은 책이 False인 책만 표시되도록 하려면 어떤 파일을 수정해야 하나요?

# Copilot이 상위 수준 계획을 생성합니다...

# PYTHON-REVIEWER 에이전트로 전환
> /agent
# "python-reviewer" 선택

> @samples/book-app-project/books.py 파일에서 읽지 않은 책 수를 가져오는 get_unread_books 메서드를 설계하세요.
> 가장 좋은 접근 방식은 무엇일까요?

# Python-reviewer 에이전트 출력 결과:
# - 메서드 시그니처 및 반환 타입
# - 리스트 컴프리헨션을 사용한 필터 구현
# - 빈 컬렉션에 대한 예외 처리

# PYTEST-HELPER 에이전트로 전환
> /agent
# "pytest-helper" 선택

> @samples/book-app-project/tests/test_books.py 읽지 않은 책 필터링에 대한 테스트 케이스를 설계합니다.

# Pytest-helper 에이전트는 다음을 생성합니다.
# - 빈 컬렉션에 대한 테스트 케이스
# - 읽은 책과 읽지 않은 책이 혼합된 경우의 테스트 케이스
# - 모든 책을 읽은 경우의 테스트 케이스

# 구현
> books.py의 BookCollection에 get_unread_books 메서드 추가
> book_app.py에 "읽지 않은 책 목록 보기" 명령 옵션 추가
> show_help 함수의 도움말 텍스트 업데이트

# 테스트
> 새 기능에 대한 포괄적인 테스트 생성

# 다음과 유사한 여러 테스트가 생성됩니다.
# - 정상 경로(3개 테스트) — 필터링이 올바르게 작동하고, 읽은 책은 제외하고, 읽지 않은 책은 포함합니다.
# - 예외 상황(4개 테스트) — 빈 컬렉션, 모든 책 읽음, 읽지 않은 책 없음, 책 한 권만 있는 경우
# - 매개변수화된 경우(5개 테스트) — @pytest.mark.parametrize를 통해 읽은 책과 읽지 않은 책의 비율을 변경합니다.
# - 통합 테스트(4개 테스트) — mark_as_read, remove_book, add_book 및 데이터 무결성과의 상호 작용

# 변경 사항 검토
> /review

# 검토가 통과되면 /pr을 사용하여 제출하세요. 현재 브랜치에 대한 풀 리퀘스트를 작업합니다.
> /pr [보기|생성|수정|자동]

# 또는 Copilot이 터미널에서 풀 리퀘스트를 생성하도록 하려면 자연스럽게 요청할 수도 있습니다.
> "Feature: Add list unread books command"라는 제목의 풀 리퀘스트를 생성합니다.
```

**기존 방식**: 에디터, 터미널, 테스트 러너, 문서, GitHub UI 사이를 계속 전환해야 했습니다. 전환할 때마다 맥락을 놓치고 불편함을 느꼈습니다.

**핵심 통찰**: 마치 건축가처럼 전문가들을 이끌었습니다. 그들은 세부 사항을 처리했고, 당신은 비전을 제시했습니다.

> 💡 **더 나아가** 이와 같은 대규모 다단계 계획의 경우, Copilot이 독립적인 하위 작업을 병렬로 실행할 수 있도록 `/fleet`을 사용해 보세요. 자세한 내용은 [공식 문서](https://docs.github.com/copilot/concepts/agents/copilot-cli/fleet)를 참조하십시오.

---

# 추가 워크플로

<img src="images/combined-workflows.png" alt="People assembling a colorful giant jigsaw puzzle with gears, representing how agents, skills, and MCP combine into unified workflows" width="800"/>

4장부터 6장까지 완료한 고급 사용자라면, 이 워크플로를 통해 Agent, 스킬, 그리고 MCP가 어떻게 업무 효율성을 극대화하는지 확인할 수 있습니다.

## 통합 패턴

모든 것을 결합하는 개념적 모델은 다음과 같습니다.

<img src="images/integration-pattern.png" alt="The Integration Pattern - A 4-phase workflow: Gather Context (MCP), Analyze and Plan (Agents), Execute (Skills + Manual), Complete (MCP)" width="800"/>

---

## 워크플로 1: 버그 조사 및 수정

실제 환경에서 버그를 수정하고 모든 도구를 통합적으로 활용합니다.

```bash
copilot

# 1단계: GitHub에서 버그 파악 (MCP 제공)
> 이슈 #1의 세부 정보 가져오기

# 학습 내용: "find_by_author 함수가 부분 이름 일치에 문제가 있습니다"

# 2단계: 모범 사례 조사 (웹 및 GitHub 자료를 활용한 심층 조사)
> /research Python 대소문자 구분 없는 문자열 일치 모범 사례

# 3단계: 관련 코드 찾기
> @samples/book-app-project/books.py find_by_author 메서드 표시

# 4단계: 전문가 분석 받기
> /agent
# "python-reviewer" 선택

> 부분 이름 일치 관련 문제 분석

# 에이전트 분석 결과: 메서드가 부분 문자열 일치 대신 정확한 동일성 비교를 사용합니다

# 5단계: 에이전트의 안내에 따라 수정
> 소문자 비교 및 ​​'in' 연산자를 사용하여 수정 구현

# 6단계: 테스트 생성
> /agent
# "pytest-helper" 선택

> 생성 pytest에서 find_by_author 함수에 대한 부분 일치 테스트 수행
> 테스트 케이스 추가: 부분 이름, 대소문자 변형, 일치 항목 없음

# 7단계: 커밋 및 풀 리퀘스트 생성
> 이 수정 사항에 대한 커밋 메시지 생성

> 이슈 #1에 연결되는 풀 리퀘스트 생성
```

---

## 워크플로 2: 코드 검토 자동화 (선택 사항)

> 💡 **이 섹션은 선택 사항입니다.** 커밋 전 훅은 팀 작업에 유용하지만 생산성을 위해 필수적인 것은 아닙니다. 처음 시작하는 경우 이 부분을 건너뛰세요.

> ⚠️ **성능 참고**: 이 훅은 스테이징된 각 파일에 대해 `copilot -p`를 호출하며, 파일당 몇 초가 소요됩니다. 대규모 커밋의 경우 중요한 파일에만 적용하거나 `/review`를 사용하여 수동으로 리뷰를 실행하는 것을 고려해 보세요.

**Git 훅**은 Git이 특정 시점(예: 커밋 직전)에 자동으로 실행하는 스크립트입니다. 이를 통해 코드에 대한 자동화된 검사를 실행할 수 있습니다. 다음은 커밋에 대한 Copilot 자동 리뷰를 설정하는 방법입니다.

```bash
# 사전 커밋 훅 생성
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# 스테이징된 파일 가져오기 (파이썬 파일만 해당)
STAGED=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.py$')

if [ -n "$STAGED" ]; then
  echo "스테이징된 파일에 대해 Copilot 검토를 실행 중입니다..."

  for file in $STAGED; do
    echo "Reviewing $file..."

    # 멈춤 현상을 방지하기 위해 타임아웃을 사용합니다(파일당 60초).
    # --allow-all 옵션은 파일 읽기/쓰기 권한을 자동으로 승인하여 후크가 자동으로 실행되도록 합니다.
    # 자동화된 스크립트에서만 사용하십시오. 대화형 세션에서는 Copilot이 권한을 요청하도록 하십시오.
        REVIEW=$(timeout 60 copilot --allow-all -p "Quick security review of @$file - critical issues only" 2>/dev/null)
    
    # 타임아웃 발생 여부 확인
    if [ $? -eq 124 ]; then
      echo "Warning: Review timed out for $file (skipping)"
      continue
    fi

    if echo "$REVIEW" | grep -qi "CRITICAL"; then
      echo "Critical issues found in $file:"
      echo "$REVIEW"
      exit 1
    fi
  done

  echo "검토 통과"
fi
EOF

chmod +x .git/hooks/pre-commit
```

> ⚠️ **macOS 사용자**: `timeout` 명령어는 macOS에 기본적으로 포함되어 있지 않습니다. `brew install coreutils` 명령어로 설치하거나, ​​`timeout 60` 대신 타임아웃 가드가 없는 간단한 명령어를 사용하세요.

> 📚 **공식 문서**: 전체 훅 API에 대한 자세한 내용은 [훅 사용 방법](https://docs.github.com/copilot/how-tos/copilot-cli/use-hooks) 및 [훅 구성 참조](https://docs.github.com/copilot/reference/hooks-configuration)를 참조하세요.

>
> 💡 **내장 대안**: Copilot CLI에는 pre-commit과 같은 이벤트에서 자동으로 실행되는 내장 훅 시스템(`copilot hooks`)도 있습니다. 위의 수동 Git 훅은 완벽한 제어를 제공하는 반면, 내장 시스템은 구성이 더 간단합니다. 어떤 방식이 워크플로에 적합한지 위의 문서를 참조하세요.

이제 모든 커밋은 빠른 보안 검토를 거칩니다.

```bash
git add samples/book-app-project/books.py
git commit -m "도서 수집 방법을 업데이트합니다"

# Output:
# Running Copilot review on staged files...
# Reviewing samples/book-app-project/books.py...
# Critical issues found in samples/book-app-project/books.py:
# - Line 15: File path injection vulnerability in load_from_file
#
# Fix the issue and try again.
```

---

## 워크플로 3: 새로운 코드베이스 온보딩

새로운 프로젝트에 참여할 때는 컨텍스트, 에이전트 및 MCP를 결합하여 빠르게 적응하세요.

```bash
# Copilot을 대화형 모드로 시작하세요
copilot

# PHASE 1: 맥락을 고려하여 전체적인 상황을 파악하세요.
> @samples/book-app-project/ 이 코드베이스의 상위 수준 아키텍처를 설명하십시오.

# PHASE 2: 특정 흐름을 이해하세요
> @samples/book-app-project/book_app.py 어떤 일이 발생하는지 설명해 주세요.
> 사용자가 "python book_app.py add"를 실행할 때

# PHASE 3: Agent와 전문가 분석을 받으세요
> /agent
# "python-reviewer"를 선택하세요

> @samples/book-app-project/books.py 파일에 디자인 문제,
> 누락된 오류 처리 또는 권장할 만한 개선 사항이 있습니까?

# PHASE 4: 작업할 주제를 찾아보세요 (MCP는 GitHub 접근 권한을 제공합니다)
> "첫 번째 이슈로 적합"이라고 표시된 미해결 이슈 목록을 확인하세요

# PHASE 5: 기여를 시작하세요
> 가장 간단한 미해결 문제를 선택하고 해결 계획을 세우세요
```

이 워크플로는 `@` 컨텍스트, 에이전트 및 MCP를 단일 온보딩 세션으로 결합하며, 이는 이 장 앞부분에서 설명한 통합 패턴과 정확히 일치합니다.

---

# 모범 사례 및 자동화

업무 효율성을 높이는 패턴과 습관.

---

## 모범 사례

### 1. 분석에 앞서 맥락 파악부터 시작하세요

분석을 요청하기 전에 항상 맥락을 파악하세요.

```bash
# Good
> 42번 이슈의 자세한 내용을 확인하세요
> /agent
# 파이썬 리뷰어를 선택하세요
> 이 문제를 분석하세요

# 효과가 떨어짐
> /agent
# 파이썬 리뷰어를 선택하세요
> 로그인 버그 수정
# Agent에 문제 컨텍스트가 없습니다.
```

### 2. 차이점을 알아두세요: 에이전트, 스킬, 그리고 사용자 지정 지침

각 도구에는 최적의 사용 지점이 있습니다.

```bash
# Agents: 사용자가 명시적으로 활성화하는 특수 페르소나
> /agent
# 파이썬 리뷰어를 선택
> 이 인증 코드의 보안 문제를 검토하십시오.

# Skills: 사용자의 요청에 따라 자동으로 활성화되는 모듈형 기능
# 스킬 설명과 일치해야 합니다(먼저 생성해야 합니다. 5장을 참조하세요).
> 이 코드에 대한 포괄적인 테스트를 생성하세요.
# 테스트 스킬이 구성되어 있으면 자동으로 활성화됩니다.

# 사용자 지정 지침(.github/copilot-instructions.md): 항상 켜짐
# 세션 전환이나 트리거 없이 모든 세션에 적용되는 지침입니다.
```

> 💡 **핵심 요점**: 에이전트와 스킬 모두 코드를 분석하고 생성할 수 있습니다. 진정한 차이점은 **활성화 방식**에 있습니다. 에이전트는 명시적 실행(`/agent`)을 통해 작동하고, 스킬은 자동 실행(프롬프트 일치)을 통해 작동하며, 사용자 지정 지침은 항상 활성화되어 있습니다.

### 3. Session의 초점을 유지하세요

세션에 이름을 지정하려면 `/rename` 명령어를 사용하고(이렇게 하면 기록에서 쉽게 찾을 수 있습니다), 깔끔하게 종료하려면 `/exit` 명령어를 사용하세요.

```bash
# Good: 세션당 하나의 기능만 사용 가능
> /rename list-unread-feature
# 읽지 않은 목록 작업
> /exit

copilot
> /rename export-csv-feature
# CSV 내보내기 작업
> /exit

# 효과가 떨어지는 방식: 모든 것을 한 번의 긴 시간에 끝내는 것
```

### 4. Copilot을 사용하여 워크플로우를 재사용 가능하게 만드세요

워크플로를 위키에 문서화하는 대신, Copilot이 사용할 수 있도록 저장소에 직접 인코딩하세요.

- **Custom instructions** (`.github/copilot-instructions.md`): 코딩 표준, 아키텍처 규칙, 빌드/테스트/배포 단계에 대한 항상 연결된 안내를 제공합니다. 모든 세션은 자동으로 그들을 따라갑니다.
- **Prompt files** (`.github/prompts/`): 코드 리뷰, 컴포넌트 생성, PR 설명 템플릿 등 재사용 가능한 매개변수화된 프롬프트를 팀이 공유할 수 있습니다.
- **Custom agents** (`.github/agents/`): 팀 내 누구나 '/agent'로 활성화할 수 있는 전문 페르소나(예: 보안 리뷰어나 문서 작성자)를 인코딩합니다.
- **Custom skills** (`.github/skills/`): 관련 사항이 있을 때 자동으로 활성화되는 단계별 워크플로우 지침을 패키지로 구성하세요.

> 💡 **결점**: 새로운 팀 구성원은 워크플로를 무료로 이용할 수 있습니다. 워크플로는 저장소에 구축되어 있으므로 누군가의 머릿속에만 갇혀 있지 않습니다.

---

## 보너스: 제작 패턴

이 패턴들은 선택 사항이지만, 전문적인 환경에서 유용합니다.

### 보도자료 설명 생성기

```bash
# Generate comprehensive PR descriptions
BRANCH=$(git branch --show-current)
COMMITS=$(git log main..$BRANCH --oneline)

copilot -p "Generate a PR description for:
Branch: $BRANCH
Commits:
$COMMITS

Include: Summary, Changes Made, Testing Done, Screenshots Needed"
```

### CI/CD 통합

For teams with existing CI/CD pipelines, you can automate Copilot reviews on every pull request using GitHub Actions. This includes posting review comments automatically and filtering for critical issues.

> 📖 **Learn more**: See [CI/CD Integration](../appendices/ci-cd-integration.md) for complete GitHub Actions workflows, configuration options, and troubleshooting tips.

---

# 관행

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

Put the complete workflow into practice.

---

## ▶️ 직접 시도해 보세요

데모를 완료한 후 다음 변형들을 시도해 보세요:

1. **종합 구현 과제**: 작은 기능 하나를 선택하세요(예: "읽지 않은 책 목록 보기" 또는 "CSV 파일로 내보내기"). 전체 워크플로를 활용해 보세요.
   - `/plan` 명령어를 사용하여 계획을 세우세요.
   - 에이전트를 활용한 설계 (python-reviewer, pytest-helper)
   - Implement
   - 테스트 생성
   - PR 만들기

2. **자동화 챌린지**: 코드 검토 자동화 워크플로에서 사전 커밋 훅을 설정합니다. 의도적으로 파일 경로 취약점을 포함한 커밋을 생성합니다. 이 커밋이 차단됩니까?

3. **귀사의 생산 워크플로우**: 자주 하는 작업에 대한 워크플로우를 직접 설계해 보세요. 체크리스트 형태로 작성해 보고, 스킬, 에이전트 또는 훅을 활용하여 자동화할 수 있는 부분은 무엇인지 생각해 보세요.

**셀프 체크**: 에이전트, 스킬, MCP가 어떻게 상호 작용하는지, 그리고 각각을 언제 사용해야 하는지 동료에게 설명할 수 있게 되면 과정을 완료한 것입니다.

---

## 📝 과제

### 주요 과제: 엔드 투 엔드 기능 구현

실습 예제를 통해 "읽지 않은 책 목록" 기능을 구축하는 과정을 단계별로 안내했습니다. 이제 다른 기능인 **연도 범위로 책 검색**에 전체 워크플로를 적용해 보겠습니다.

1. Copilot을 시작하고 컨텍스트를 수집합니다. `@samples/book-app-project/books.py`
2. `/plan` 명령으로 계획을 세웁니다. 사용자가 두 해 사이에 출판된 책을 찾을 수 있는 "연도별 검색" 명령을 추가합니다.
3. `BookCollection` 클래스에 `find_by_year_range(start_year, end_year)` 메서드를 구현합니다.
4. `book_app.py` 파일에 시작 연도와 종료 연도를 입력받는 `handle_search_year()` 함수를 추가합니다.
5. 테스트를 생성합니다. `@samples/book-app-project/books.py @samples/book-app-project/tests/test_books.py `잘못된 연도, 범위 반전, 검색 결과 없음과 같은 예외 상황을 포함하여 `find_by_year_range()` 함수에 대한 테스트를 생성합니다.`
6. `/review` 명령으로 검토합니다.
7. README 파일을 업데이트합니다. `@samples/book-app-project/README.md`에 다음 내용을 추가합니다. 새로운 "연도별 검색" 명령에 대한 문서를 작성합니다.
8. 커밋 메시지를 생성합니다.

진행 과정을 문서화하세요.

**성공 기준**: Copilot CLI를 사용하여 아이디어 구상부터 커밋까지(계획, 구현, 테스트, 문서화 및 검토 포함) 기능을 완료했습니다.

> 💡 **보너스**: 4장에서 에이전트를 설정했다면, 사용자 지정 에이전트를 만들어 사용해 보세요. 예를 들어, 구현 검토를 위한 오류 처리 에이전트와 README 업데이트를 위한 문서 작성 에이전트를 만들 수 있습니다.

<세부 정보>
<요약>💡 힌트 (클릭하여 펼치기)</요약>

**이 장 상단의 ["아이디어에서 병합된 PR까지"](#idea-to-merged-pr-in-one-session) 예제의 패턴을 따르세요.** 주요 단계는 다음과 같습니다.

1. `@samples/book-app-project/books.py`를 사용하여 컨텍스트를 수집합니다.
2. `/plan Add a “search by year” command`를 사용하여 계획을 수립합니다.
3. 메서드와 명령 핸들러를 구현합니다.
4. 예외 상황(잘못된 입력, 빈 결과, 역순 범위)을 고려한 테스트를 생성합니다.
5. `/review`를 사용하여 검토합니다.
6. `@samples/book-app-project/README.md`를 사용하여 README를 업데이트합니다.
7. `-p` 옵션을 사용하여 커밋 메시지를 생성합니다.

**고려해야 할 예외 상황:**
- 사용자가 "2000"과 "1990"을 입력하는 경우(역순 범위)는 어떻게 처리할까요?
- 범위에 맞는 책이 없는 경우는 어떻게 처리할까요?
- 사용자가 숫자가 아닌 값을 입력하는 경우는 어떻게 처리할까요?

**핵심은 아이디어 → 컨텍스트 → 계획 → 구현 → 테스트 → 문서화 → 커밋의 전체 워크플로를 연습하는 것입니다.**

</details>

---

<details>
<summary>🔧 <strong>흔히 저지르는 실수</strong> (클릭하여 펼치기)</summary>

| 실수 | 발생 원인 | 해결 방법 |
|---------|--------------|-----|
| 바로 구현으로 넘어감 | 나중에 수정하는 데 비용이 많이 드는 설계 문제를 놓침 | 먼저 `/plan`을 사용하여 접근 방식을 구상함 |
| 여러 도구를 활용할 수 있는데도 하나의 도구만 사용함 | 속도가 느리고 결과가 불완전함 | 조합: 분석용 에이전트 → 실행용 스킬 → 통합용 MCP |
| 커밋 전에 검토하지 않음 | 보안 문제나 버그가 발생함 | 항상 `/review`를 실행하거나 [pre-commit hook](#workflow-2-code-review-automation-optional)을 사용함 |
| 팀과 워크플로를 공유하는 것을 잊음 | 각자 바퀴를 다시 발명함 | 공유 에이전트, 스킬 및 지침에 패턴을 문서화함 |

</details>

---

# 요약

## 🔑 핵심 요점

1. **통합 > 분리**: 최대 효과를 위해 도구를 결합하세요.
2. **맥락 우선**: 분석 전에 항상 필요한 맥락을 수집하세요.
3. **에이전트는 분석, 스킬은 실행**: 작업에 맞는 도구를 사용하세요.
4. **반복 작업 자동화**: 훅과 스크립트를 활용하여 효율성을 높이세요.
5. **워크플로우 문서화**: 공유 가능한 패턴은 팀 전체에 도움이 됩니다.

> 📋 **빠른 참조**: 전체 명령 및 단축키 목록은 [GitHub Copilot CLI 명령 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참조하세요.
---

## 🎓 과정 완료!

축하합니다! 다음 내용을 학습하셨습니다.

| 챕터 | 학습 내용 |
---------|-------------------|
| 00 | Copilot CLI 설치 및 빠른 시작 |
| 01 | 세 가지 상호 작용 모드 |
| 02 | @ 구문을 사용한 컨텍스트 관리 |
| 03 | 개발 워크플로 |
| 04 | 특수 에이전트 |
| 05 | 확장 가능한 스킬 |
| 06 | MCP를 사용한 외부 연결 |
| 07 | 통합 프로덕션 워크플로 |

이제 GitHub Copilot CLI를 개발 워크플로에서 진정한 시너지 효과를 내는 도구로 활용할 준비가 되었습니다.

## ➡️ 다음 단계

학습은 여기서 끝나지 않습니다.

1. **매일 연습하세요**: Copilot CLI를 사용하여 실제 업무를 수행하세요.
2. **맞춤형 도구를 만드세요**: 특정 요구 사항에 맞는 에이전트와 스킬을 제작하세요.
3. **지식을 공유하세요**: 팀원들이 이러한 워크플로를 도입하도록 지원하세요.
4. **최신 정보를 확인하세요**: GitHub Copilot 업데이트를 팔로우하여 새로운 기능을 확인하세요.

### 자료
- [GitHub Copilot CLI Documentation](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [Community Skills](https://github.com/topics/copilot-skill)

---

정말 잘했어요! 이제 멋진 작품을 만들어 보세요.

**[← Back to Chapter 06](../06-mcp-servers/README.md)** | **[Return to Course Home →](../README.md)**
