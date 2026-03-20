![챕터 03: 개발 워크플로](images/chapter-header.png)

> **AI가 당신이 질문조차 하지 못했던 버그까지 찾아줄 수 있다면 어떨까요?**

이 챕터에서는 GitHub Copilot CLI가 여러분의 ‘일상 주력 도구(daily driver)’가 됩니다. 여러분이 매일 의존하는 워크플로—테스트, 리팩터링, 디버깅, 그리고 Git—안에서 Copilot CLI를 활용하게 됩니다.

## 🎯 학습 목표

이 챕터를 마치면 다음을 할 수 있습니다:

- Copilot CLI로 포괄적인 코드 리뷰 수행하기
- 레거시 코드를 안전하게 리팩터링하기
- AI의 도움으로 이슈를 디버깅하기
- 테스트를 자동으로 생성하기
- Copilot CLI를 git 워크플로에 통합하기

> ⏱️ **예상 소요 시간**: 약 60분 (읽기 15분 + 실습 45분)

---

## 🧩 현실 비유: 목수의 워크플로

목수는 단지 도구를 다루는 법만 아는 것이 아니라, 작업 종류에 따라 서로 다른 *워크플로*를 가지고 있습니다.

<img src="images/carpenter-workflow-steps.png" alt="세 가지 워크플로 레인이 보이는 장인의 작업장: 가구 제작(측정, 절단, 조립, 마감), 손상 수리(평가, 제거, 수리, 매칭), 품질 점검(검사, 이음새 테스트, 정렬 확인)" width="800"/>

마찬가지로 개발자도 작업마다 다른 워크플로를 가지고 있습니다. GitHub Copilot CLI는 이러한 각 워크플로를 강화하여, 일상적인 코딩 작업에서 더 효율적이고 더 효과적으로 만들어 줍니다.

---

# 다섯 가지 워크플로

<img src="images/five-workflows.png" alt="코드 리뷰, 테스트, 디버깅, 리팩터링, git 통합을 나타내는 다섯 개의 네온 아이콘" width="800"/>

아래의 각 워크플로는 독립적으로 구성되어 있습니다. 현재 필요에 맞는 것을 선택하거나, 모두 차례대로 진행해도 좋습니다.

---

## 나만의 진행 방식(Choose Your Own Adventure)

이 챕터는 개발자가 보통 사용하는 다섯 가지 워크플로를 다룹니다. **하지만 한 번에 전부 읽을 필요는 없습니다!** 각 워크플로는 아래에서 접었다 펼 수 있는 섹션으로 독립 구성되어 있습니다. 지금 하고 있는 프로젝트에 가장 잘 맞는 것을 골라 진행해 보세요. 나머지는 언제든 다시 돌아와 살펴볼 수 있습니다.

<img src="images/five-workflows-swimlane.png" alt="다섯 가지 개발 워크플로(코드 리뷰, 리팩토링, 디버깅, 테스트 생성, Git 통합)를 가로 스윔레인으로 표현한 그림" width="800"/>

| 나는 …하고 싶다 | 바로가기 |
|---|---|
| 머지 전에 코드를 리뷰하고 싶다 | [워크플로 1: 코드 리뷰](#workflow-1-code-review) |
| 지저분하거나 레거시 코드를 정리하고 싶다 | [워크플로 2: 리팩터링](#workflow-2-refactoring) |
| 버그를 추적해서 고치고 싶다 | [워크플로 3: 디버깅](#workflow-3-debugging) |
| 내 코드의 테스트를 생성하고 싶다 | [워크플로 4: 테스트 생성](#workflow-4-test-generation) |
| 더 나은 커밋/PR을 작성하고 싶다 | [워크플로 5: Git 통합](#workflow-5-git-integration) |
| 코딩 전에 조사(리서치)를 하고 싶다 | [빠른 팁: 계획/코딩 전에 리서치하기](#quick-tip-research-before-you-plan-or-code) |
| 버그 수정 워크플로를 처음부터 끝까지 보고 싶다 | [한 번에 엮어 보기](#putting-it-all-together-bug-fix-workflow) |

아래에서 **원하는 워크플로를 선택해 펼친 뒤**, GitHub Copilot CLI가 해당 영역의 개발 프로세스를 어떻게 개선하는지 확인해 보세요.

---

<a id="workflow-1-code-review"></a>
<details>
<summary><strong>워크플로 1: 코드 리뷰</strong> - 파일 리뷰, /review 에이전트 사용, 심각도 체크리스트 만들기</summary>

<img src="images/code-review-swimlane-single.png" alt="코드 리뷰 워크플로: 리뷰, 이슈 식별, 우선순위 지정, 체크리스트 생성" width="800"/>

### 기본 리뷰

이 예시는 `@` 기호로 파일을 참조하여, Copilot CLI가 리뷰를 위해 해당 파일의 내용을 직접 읽을 수 있게 합니다.

```bash
copilot

> @samples/book-app-project/book_app.py 를 코드 품질 관점에서 리뷰해줘
```

---

<details>
<summary>🎬 실제 동작 보기</summary>

![코드 리뷰 데모](images/code-review-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기 표시된 것과 다를 수 있습니다.*

</details>

---

### 입력값 검증(Input Validation) 리뷰

프롬프트에 관심 있는 카테고리를 나열하면, Copilot CLI가 특정 우려사항(여기서는 입력값 검증)에 집중해서 리뷰하도록 유도할 수 있습니다.

```text
copilot

> @samples/book-app-project/utils.py 를 입력값 검증 관점에서 리뷰해줘. 다음을 확인해줘: 누락된 검증, 에러 처리 공백, 엣지 케이스
```

### 여러 파일을 아우르는 프로젝트 리뷰

디렉터리 전체를 `@`로 참조하면 Copilot CLI가 프로젝트 내 모든 파일을 한 번에 스캔할 수 있습니다.

```bash
copilot

> @samples/book-app-project/ 이 프로젝트 전체를 리뷰해줘. 발견한 이슈를 심각도별로 분류해서 마크다운 체크리스트로 만들어줘
```

### 대화형(Interactive) 코드 리뷰

멀티턴 대화를 통해 더 깊게 파고들 수 있습니다. 넓게 리뷰한 뒤, 세션을 다시 시작하지 않고 후속 질문을 이어가세요.

```bash
copilot

> @samples/book-app-project/book_app.py 이 파일을 다음 관점에서 리뷰해줘:
> - 입력값 검증
> - 에러 처리
> - 코드 스타일 및 베스트 프랙티스

# Copilot CLI가 상세 리뷰를 제공

> 사용자 입력 처리에서 놓치고 있는 엣지 케이스가 있을까?

# Copilot CLI가 빈 문자열, 특수문자 등의 잠재 이슈를 보여줌

> 발견한 모든 이슈를 심각도 우선순위로 정리한 체크리스트로 만들어줘

# Copilot CLI가 우선순위 기반 액션 아이템을 생성
```

### 리뷰 체크리스트 템플릿

출력을 특정 형식으로 구조화해 달라고 요청하면(여기서는 이슈에 붙여 넣기 좋은 ‘심각도별 마크다운 체크리스트’), Copilot CLI가 그에 맞춰 정리해 줍니다.

```bash
copilot

> @samples/book-app-project/ 를 리뷰하고, 발견한 이슈를 아래 기준으로 분류한 마크다운 체크리스트를 만들어줘:
> - Critical (데이터 손실 위험, 크래시)
> - High (버그, 잘못된 동작)
> - Medium (성능, 유지보수성)
> - Low (스타일, 소소한 개선)
```

### Git 변경 사항 이해하기 (/review 사용 시 중요)

`/review` 명령을 사용하기 전에, git에서 두 가지 변경 상태를 이해해야 합니다.

| 변경 유형 | 의미 | 확인 방법 |
|-------------|---------------|------------|
| **스테이징된 변경 사항(Staged changes)** | `git add`로 다음 커밋에 포함시키기로 표시한 파일 | `git diff --staged` |
| **스테이징되지 않은 변경 사항(Unstaged changes)** | 수정했지만 아직 add하지 않은 파일 | `git diff` |

```bash
# 빠른 참고
git status           # 스테이징/미스테이징 모두 표시
git add file.py      # 파일을 스테이징
git diff             # 스테이징되지 않은 변경 사항
git diff --staged    # 스테이징된 변경 사항
```

### /review 명령 사용하기

`/review` 명령은 내장된 **코드 리뷰 에이전트**를 호출합니다. 이 에이전트는 스테이징/미스테이징 변경 사항을 높은 신호 대비 낮은 잡음(signal-to-noise)으로 분석하도록 최적화되어 있습니다. 자유 형식 프롬프트를 길게 쓰는 대신, 슬래시 명령으로 특화된 내장 에이전트를 트리거해 보세요.

```bash
copilot

> /review
# 스테이징/미스테이징 변경 사항에 대해 코드 리뷰 에이전트를 실행
# 집중적이고 실행 가능한 피드백 제공

> /review 인증(authentication)에서 보안 이슈가 있는지 확인해줘
# 특정 초점 영역을 지정해 리뷰 실행
```

> 💡 **팁**: 코드 리뷰 에이전트는 ‘대기 중인 변경 사항’이 있을 때 가장 잘 동작합니다. `git add`로 파일을 스테이징하면 더 집중된 리뷰를 받을 수 있습니다.

</details>

---

<a id="workflow-2-refactoring"></a>
<details>
<summary><strong>워크플로 2: 리팩터링</strong> - 코드 재구성, 관심사 분리, 에러 처리 개선</summary>

<img src="images/refactoring-swimlane-single.png" alt="리팩터링 워크플로: 코드 평가, 변경 계획, 구현, 동작 검증" width="800"/>

### 간단한 리팩터링

> **먼저 이렇게 해보세요:** `@samples/book-app-project/book_app.py 명령 처리 로직이 if/elif 체인으로 되어 있어. 딕셔너리 디스패치 패턴을 사용하도록 리팩터링해줘.`

가볍고 직관적인 개선부터 시작하세요. 다음 프롬프트들은 book app에 적용해 볼 수 있습니다. 각 프롬프트는 `@` 파일 참조와 구체적인 리팩터링 지시를 함께 제공해, Copilot CLI가 무엇을 바꿔야 하는지 정확히 이해하게 합니다.

```bash
copilot

> @samples/book-app-project/book_app.py 명령 처리 로직이 if/elif 체인으로 되어 있어. 딕셔너리 디스패치 패턴을 사용하도록 리팩터링해줘.

> @samples/book-app-project/utils.py 모든 함수에 타입 힌트를 추가해줘

> @samples/book-app-project/book_app.py 책 표시(display) 로직을 utils.py로 추출해서 관심사를 더 잘 분리해줘
```

> 💡 **리팩터링이 처음이라면?** 타입 힌트 추가나 변수명 개선처럼 단순한 요청부터 시작한 뒤, 더 복잡한 변환으로 확장해 보세요.

---

<details>
<summary>🎬 실제 동작 보기</summary>

![리팩터 데모](images/refactor-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기 표시된 것과 다를 수 있습니다.*

</details>

---

### 관심사 분리(Separate Concerns)

한 프롬프트에서 `@`로 여러 파일을 함께 참조하면, Copilot CLI가 리팩터 과정에서 파일 간 코드 이동까지 제안/수행할 수 있습니다.

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/book_app.py
> utils.py 파일에 print문과 로직이 섞여 있어. 출력(display) 함수와 데이터 처리 로직을 분리하도록 리팩터링해줘.
```

### 에러 처리 개선

서로 관련된 파일 2개를 제공하고, 가로지르는 관심사(여기서는 에러 처리)를 설명하면 Copilot CLI가 두 파일에 걸쳐 일관된 수정 방향을 제안할 수 있습니다.

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/books.py
> 이 파일들의 에러 처리가 일관되지 않아. 커스텀 예외(custom exceptions)를 사용해서 통일된 접근을 제안해줘.
```

### 문서(Documentation) 추가

각 docstring에 무엇을 포함해야 하는지 구체적인 불릿 리스트로 지정하면 Copilot CLI가 그에 맞춰 문서를 작성해 줍니다.

```bash
copilot

> @samples/book-app-project/books.py 모든 메서드에 대해 포괄적인 docstring을 추가해줘:
> - 파라미터 타입과 설명 포함
> - 반환값 문서화
> - 발생 가능한 예외 명시
> - 사용 예시 추가
```

### 테스트와 함께 안전하게 리팩터링하기

멀티턴 대화에서 두 요청을 연결해 보세요. 먼저 현재 동작을 테스트로 고정한 뒤, 그 테스트를 안전망으로 삼아 리팩터링하면 됩니다.

```bash
copilot

> @samples/book-app-project/books.py 리팩터링 전에, 현재 동작을 기준으로 테스트를 먼저 생성해줘

# 먼저 테스트를 확보

> 이제 BookCollection 클래스를 리팩터링해서 파일 작업(file operations)에 컨텍스트 매니저(context manager)를 사용하도록 바꿔줘

# 테스트가 동작 보존을 검증하므로 더 안심하고 리팩터링 가능
```

</details>

---

<a id="workflow-3-debugging"></a>
<details>
<summary><strong>워크플로 3: 디버깅</strong> - 버그 추적, 보안 점검, 여러 파일에 걸친 이슈 추적</summary>

<img src="images/debugging-swimlane-single.png" alt="디버깅 워크플로: 에러 이해, 근본 원인 파악, 수정, 테스트" width="800"/>

### 간단한 디버깅

> **먼저 이렇게 해보세요:** `@samples/book-app-buggy/books_buggy.py 사용자들이 \"The Hobbit\"을 검색하면 데이터에 있는데도 결과가 없다고 해. 왜 그런지 디버깅해줘.`

무엇이 잘못되었는지 증상을 설명하는 것부터 시작하세요. 아래는 buggy book app에서 시도해 볼 수 있는 대표적인 디버깅 패턴들입니다. 각 프롬프트는 `@` 파일 참조와 명확한 증상을 함께 제공해, Copilot CLI가 버그의 위치를 찾고 진단할 수 있게 합니다.

```bash
copilot

# 패턴: "X가 나와야 하는데 Y가 나와요"
> @samples/book-app-buggy/books_buggy.py 사용자들이 "The Hobbit"을 검색하면 데이터에 있는데도 결과가 없다고 해. 왜 그런지 디버깅해줘.

# 패턴: "예상치 못한 동작"
> @samples/book-app-buggy/book_app_buggy.py 존재하지 않는 책을 삭제하려고 하면, 앱이 삭제됐다고 말해. 왜 그런지 찾아줘.

# 패턴: "결과가 틀려요"
> @samples/book-app-buggy/books_buggy.py 책 한 권만 읽음 처리했는데, 모든 책이 읽음 처리돼. 버그가 뭐야?
```

> 💡 **디버깅 팁**: *증상*(무엇이 보이는지)과 *기대*(원래 어떻게 되어야 하는지)를 함께 설명하세요. Copilot CLI가 나머지를 파악합니다.

---

<details>
<summary>🎬 실제 동작 보기</summary>

![버그 수정 데모](images/fix-bug-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기 표시된 것과 다를 수 있습니다.*

</details>

---

### "버그 탐정" - AI가 ‘관련된’ 버그까지 찾는다

여기서 맥락 기반 디버깅의 강점이 드러납니다. buggy book app에서 이 시나리오를 시도해 보세요. `@`로 파일 전체를 제공하고, 사용자 제보 증상만 설명하면 Copilot CLI가 근본 원인을 추적하며 주변의 다른 버그까지 발견할 수 있습니다.

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
>
> 사용자 제보: "저자 이름으로 책을 찾을 때, 부분 문자열로는 동작하지 않는다"
> 왜 이런지 디버깅해줘
```

**Copilot CLI가 하는 일**:
```
근본 원인: 80번째 줄에서 부분 매칭(in) 대신 정확한 일치(==)를 사용하고 있습니다.

80번째 줄: return [b for b in self.books if b.author == author]

find_by_author 함수는 정확히 일치해야만 검색됩니다. "Tolkien"으로 검색하면
"J.R.R. Tolkien"의 책은 찾지 못합니다.

수정: 대소문자 무시 + 부분 매칭으로 변경:
return [b for b in self.books if author.lower() in b.author.lower()]
```

**이게 중요한 이유**: Copilot CLI는 파일 전체를 읽고, 버그 리포트의 맥락을 이해하여, 구체적인 수정안과 명확한 설명을 제공합니다.

> 💡 **보너스**: Copilot CLI가 파일 전체를 분석하기 때문에, 여러분이 묻지 않은 *다른* 문제도 종종 발견합니다. 예를 들어 저자 검색을 고치면서 `find_book_by_title`의 대소문자 민감도 버그를 같이 지적할 수도 있습니다!

### 현실 세계 보안 사이드바

내 코드를 디버깅하는 것도 중요하지만, 운영 환경의 애플리케이션에서 보안 취약점을 이해하는 일은 더 중요합니다. 다음 예시처럼, 익숙하지 않은 파일을 Copilot CLI에 주고 보안 이슈를 점검해 달라고 요청해 보세요.

```bash
copilot

> @samples/buggy-code/python/user_service.py 이 Python 사용자 서비스에서 모든 보안 취약점을 찾아줘
```

이 파일은 실제 프로덕션 앱에서 마주치게 될 현실적인 보안 패턴을 보여줍니다.

> 💡 **자주 접하게 될 보안 용어:**
> - **SQL 인젝션(SQL Injection)**: 사용자 입력을 그대로 쿼리에 넣어 공격자가 악성 명령을 실행할 수 있게 되는 문제
> - **파라미터화 쿼리(Parameterized queries)**: 안전한 대안. 플레이스홀더(`?`)로 데이터와 SQL 명령을 분리
> - **레이스 컨디션(Race condition)**: 두 작업이 동시에 일어나 서로 간섭하는 문제
> - **XSS(Cross-Site Scripting)**: 공격자가 웹 페이지에 악성 스크립트를 주입하는 문제

---

### 에러 이해하기

스택 트레이스를 프롬프트에 그대로 붙여 넣고, `@` 파일 참조까지 함께 제공하면 Copilot CLI가 에러를 소스 코드에 매핑해 설명해 줍니다.

```bash
copilot

> 이런 에러가 나요:
> AttributeError: 'NoneType' object has no attribute 'title'
>     at show_books (book_app.py:19)
>
> @samples/book-app-project/book_app.py 왜 발생하는지와 어떻게 고칠지 설명해줘
```

### 테스트 케이스와 함께 디버깅하기

정확한 입력과 관찰된 출력을 설명하면, Copilot CLI가 재현 가능한 구체적 사례를 바탕으로 추론할 수 있습니다.

```bash
copilot

> @samples/book-app-buggy/books_buggy.py remove_book 함수에 버그가 있어. "Dune"을 삭제하려고 하면,
> "Dune Messiah"까지 같이 삭제돼. 원인을 설명하고 수정안을 제시해줘.
```

### 코드 전체에서 이슈 추적하기

여러 파일을 함께 참조하고, Copilot CLI에게 데이터 흐름을 따라가며 어디서 문제가 시작되는지 추적하도록 요청할 수 있습니다.

```bash
copilot

> 사용자들이 책 목록 번호가 1부터가 아니라 0부터 시작한다고 해.
> @samples/book-app-buggy/book_app_buggy.py @samples/book-app-buggy/books_buggy.py
> 목록 표시 흐름을 따라가며 어디서 문제가 생기는지 찾아줘
```

### 데이터 이슈 이해하기

코드와 함께 데이터를 제공하면, Copilot CLI가 전체 맥락을 이해한 상태에서 에러 처리 개선을 제안할 수 있습니다.

```bash
copilot

> @samples/book-app-project/data.json @samples/book-app-project/books.py
> 가끔 JSON 파일이 깨져서 앱이 크래시가 나. 이런 경우 우아하게 처리하려면 어떻게 해야 해?
```

</details>

---

<a id="workflow-4-test-generation"></a>
<details>
<summary><strong>워크플로 4: 테스트 생성</strong> - 엣지 케이스까지 포함한 포괄적인 테스트 자동 생성</summary>

<img src="images/test-gen-swimlane-single.png" alt="테스트 생성 워크플로: 함수 분석, 테스트 생성, 엣지 케이스 포함, 실행" width="800"/>

> **먼저 이렇게 해보세요:** `@samples/book-app-project/books.py 모든 함수를 대상으로 엣지 케이스를 포함한 pytest 테스트를 생성해줘`

### "테스트 폭발" - 2개 테스트 vs 15개+ 테스트

수동으로 테스트를 작성할 때, 개발자는 보통 2~3개의 기본 테스트를 만듭니다:
- 유효한 입력 테스트
- 유효하지 않은 입력 테스트
- 엣지 케이스 테스트

Copilot CLI에 포괄적인 테스트 생성을 요청하면 어떤 일이 벌어지는지 확인해 보세요! 아래 프롬프트는 `@` 파일 참조와 구조화된 불릿 리스트를 함께 사용하여, Copilot CLI가 더 철저한 테스트 커버리지로 안내되도록 합니다.

```bash
copilot

> @samples/book-app-project/books.py 포괄적인 pytest 테스트를 생성해줘. 다음을 포함해줘:
> - 책 추가
> - 책 삭제
> - 제목으로 찾기
> - 저자로 찾기
> - 읽음 표시
> - 데이터가 비어 있을 때의 엣지 케이스
```

---

<details>
<summary>🎬 실제 동작 보기</summary>

![테스트 생성 데모](images/test-gen-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기 표시된 것과 다를 수 있습니다.*

</details>

---

**얻게 되는 것**: 다음을 포함한 15개+ 포괄적 테스트

```python
class TestBookCollection:
    # 해피 패스
    def test_add_book_creates_new_book(self):
        ...
    def test_list_books_returns_all_books(self):
        ...

    # 찾기(Find) 관련
    def test_find_book_by_title_case_insensitive(self):
        ...
    def test_find_book_by_title_returns_none_when_not_found(self):
        ...
    def test_find_by_author_partial_match(self):
        ...
    def test_find_by_author_case_insensitive(self):
        ...

    # 엣지 케이스
    def test_add_book_with_empty_title(self):
        ...
    def test_remove_nonexistent_book(self):
        ...
    def test_mark_as_read_nonexistent_book(self):
        ...

    # 데이터 영속성
    def test_save_books_persists_to_json(self):
        ...
    def test_load_books_handles_missing_file(self):
        ...
    def test_load_books_handles_corrupted_json(self):
        ...

    # 특수 문자
    def test_add_book_with_unicode_characters(self):
        ...
    def test_find_by_author_with_special_characters(self):
        ...
```

**결과**: 30초 만에, 떠올리기만 해도 1시간은 걸릴 엣지 케이스 테스트를 얻을 수 있습니다.

---

### 유닛 테스트(Unit Tests)

단일 함수를 대상으로, 어떤 입력 카테고리를 테스트하고 싶은지 나열하면 Copilot CLI가 더 집중적이고 철저한 유닛 테스트를 생성합니다.

```bash
copilot

> @samples/book-app-project/utils.py get_book_details에 대한 포괄적인 pytest 테스트를 생성해줘. 다음을 커버해줘:
> - 유효한 입력
> - 빈 문자열
> - 잘못된 연도 형식
> - 매우 긴 제목
> - 저자 이름에 포함된 특수 문자
```

### 테스트 실행하기

툴체인에 대한 자연어 질문을 던지면, Copilot CLI가 적절한 셸 명령을 생성해 줍니다.

```bash
copilot

> 테스트를 어떻게 실행해? pytest 명령을 알려줘.

# Copilot CLI 응답 예시:
# cd samples/book-app-project && python -m pytest tests/
# 또는 자세히 보기: python -m pytest tests/ -v
# print 출력 보기: python -m pytest tests/ -s
```

### 특정 시나리오용 테스트

고급/까다로운 시나리오를 나열하면 Copilot CLI가 해피 패스를 넘어서는 테스트를 생성합니다.

```bash
copilot

> @samples/book-app-project/books.py 다음 시나리오에 대한 테스트를 생성해줘:
> - 중복 책 추가(같은 제목+같은 저자)
> - 제목 부분 일치로 책 삭제
> - 컬렉션이 비어 있을 때 책 찾기
> - 저장 중 파일 권한 에러
> - 책 컬렉션에 대한 동시 접근
```

### 기존 파일에 테스트 추가하기

하나의 함수에 대해 *추가* 테스트를 요청하면, Copilot CLI가 기존에 없던 케이스를 보완하는 테스트를 생성합니다.

```bash
copilot

> @samples/book-app-project/books.py
> find_by_author 함수에 대해 다음 엣지 케이스를 포함한 추가 테스트를 만들어줘:
> - 하이픈이 있는 저자명(예: "Jean-Paul Sartre")
> - 이름이 여러 개인 저자
> - 빈 문자열 저자
> - 악센트가 있는 저자명
```

</details>

---

<a id="workflow-5-git-integration"></a>
<details>
<summary><strong>워크플로 5: Git 통합</strong> - 커밋 메시지, PR 설명, /pr, /delegate, /diff</summary>

<img src="images/git-integration-swimlane-single.png" alt="Git 통합 워크플로: 변경 사항 스테이징, 메시지 생성, 커밋, PR 생성" width="800"/>

> 💡 **이 워크플로는 기본적인 git 이해를 전제로 합니다**(스테이징, 커밋, 브랜치). git이 낯설다면, 다른 네 가지 워크플로부터 먼저 시도해 보세요.

### 커밋 메시지 생성

> **먼저 이렇게 해보세요:** `copilot -p "Generate a conventional commit message for: $(git diff --staged)"` — 변경 사항을 스테이징한 다음 실행하면 Copilot CLI가 커밋 메시지를 작성해 줍니다.

이 예시는 `-p` 인라인 프롬프트 플래그와 셸 명령 치환을 이용해 `git diff` 출력을 Copilot CLI로 파이프하여, 한 번에 커밋 메시지를 생성하는 방법을 보여줍니다. `$(...)` 문법은 괄호 안의 명령을 실행하고 그 출력을 바깥 명령에 삽입합니다.

```bash

# 무엇이 바뀌었는지 확인
git diff --staged

# [Conventional Commit](../GLOSSARY.md#conventional-commit) 형식으로 커밋 메시지 생성
# ("feat(books): add search" 또는 "fix(data): handle empty input" 같은 구조화된 메시지)
copilot -p "Generate a conventional commit message for: $(git diff --staged)"

# 출력 예시: "feat(books): add partial author name search
# 
# - Update find_by_author to support partial matches
# - Add case-insensitive comparison
# - Improve user experience when searching authors"
```

---

<details>
<summary>🎬 실제 동작 보기</summary>

![Git 통합 데모](images/git-integration-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기 표시된 것과 다를 수 있습니다.*

</details>

---

### 변경 사항 설명하기

`git show` 출력을 `-p` 프롬프트로 전달하면, 마지막 커밋이 무엇을 바꿨는지 자연어로 요약할 수 있습니다.

```bash
# 이 커밋이 무엇을 바꿨지?
copilot -p "Explain what this commit does: $(git show HEAD --stat)"
```

### PR 설명(PR Description)

`git log` 출력과 구조화된 프롬프트 템플릿을 결합해, 완성도 높은 PR 설명을 자동 생성할 수 있습니다.

```bash
# 브랜치 변경 사항으로부터 PR 설명 생성
copilot -p "이 변경 사항들에 대한 풀 리퀘스트 설명을 작성해줘:
$(git log main..HEAD --oneline)

포함할 내용:
- 변경 요약
- 왜 변경했는지
- 수행한 테스트
- Breaking changes? (yes/no)"
```

### 인터랙티브 모드에서 현재 브랜치의 /pr 사용하기

Copilot CLI의 인터랙티브 모드에서 브랜치 작업 중이라면 `/pr` 명령으로 PR을 다룰 수 있습니다. `/pr`로 PR 보기, 새 PR 생성, 기존 PR 수정, 또는 브랜치 상태에 따라 자동 결정(auto)까지 사용할 수 있습니다.

```bash
copilot

> /pr [view|create|fix|auto]
```

### 푸시 전 리뷰

`git diff main..HEAD`를 `-p` 프롬프트에 넣어, 푸시 전에 브랜치 전체 변경 사항을 빠르게 점검할 수 있습니다.

```bash
# 푸시 전에 마지막 점검
copilot -p "푸시하기 전에 이 변경 사항들을 리뷰해서 문제 없는지 확인해줘:
$(git diff main..HEAD)"
```

### /delegate로 백그라운드 작업 맡기기

`/delegate` 명령은 GitHub의 Copilot 코딩 에이전트에게 작업을 위임합니다. `/delegate` 슬래시 명령(또는 `&` 단축키)을 사용해, 명확히 정의된 작업을 백그라운드 에이전트에게 오프로딩할 수 있습니다.

```bash
copilot

> /delegate 로그인 폼에 입력값 검증을 추가해줘

# 또는 & 접두사 단축키 사용:
> & README 헤더의 오타를 고쳐줘

# Copilot CLI:
# 1. 새 브랜치에 변경 사항을 커밋
# 2. 드래프트 PR을 오픈
# 3. GitHub에서 백그라운드로 작업
# 4. 완료되면 리뷰 요청
```

이 방식은 여러분이 다른 일에 집중하는 동안, 잘 정의된 작업을 맡겨 두기에 좋습니다.

### /diff로 세션 변경 사항 리뷰하기

`/diff` 명령은 현재 세션 동안 변경된 모든 내용을 보여줍니다. 커밋 전에 Copilot CLI가 수정한 모든 파일의 변경 사항을 시각적으로 확인할 때 유용합니다.

```bash
copilot

# 변경을 몇 가지 한 뒤...
> /diff

# 이 세션에서 수정된 모든 파일의 변경 사항을 시각적으로 표시
# 커밋 전에 리뷰하기 좋음
```

</details>

---

## 빠른 팁: 계획하거나 코딩하기 전에 리서치하기

라이브러리를 조사하거나, 베스트 프랙티스를 이해하거나, 익숙하지 않은 주제를 탐색해야 할 때는 `/research`로 코드를 작성하기 전에 ‘딥 리서치’ 조사를 실행할 수 있습니다.

```bash
copilot

> /research CLI 앱에서 사용자 입력 검증을 위한 최고의 Python 라이브러리는 뭐야?
```

Copilot은 GitHub 저장소와 웹 소스를 검색한 뒤, 참고 링크와 함께 요약을 제공합니다. 새로운 기능을 시작하기 전에 더 나은 결정을 내리기 위해 유용하며, `/share`로 결과를 공유할 수도 있습니다.

> 💡 **팁**: `/research`는 `/plan` *이전*에 특히 잘 맞습니다. 먼저 접근 방식을 조사하고, 그 다음 구현 계획을 세우세요.

---

## 한 번에 엮어 보기: 버그 수정 워크플로

다음은 제보된 버그를 수정하는 전체 워크플로의 예시입니다:

```bash

# 1. 버그 리포트 이해
copilot

> 사용자 제보: '저자 이름으로 책을 찾을 때, 부분 문자열로는 동작하지 않는다'
> @samples/book-app-project/books.py 분석해서 유력한 원인을 찾아줘

# 2. 이슈 디버깅(같은 세션에서 계속)
> 분석을 바탕으로 find_by_author 함수를 보여주고, 무엇이 문제인지 설명해줘

> find_by_author 함수가 부분 이름 매칭을 처리하도록 수정해줘

# 3. 수정에 대한 테스트 생성
> @samples/book-app-project/books.py 다음을 대상으로 pytest 테스트를 생성해줘:
> - 저자 전체 이름 매칭
> - 저자 부분 이름 매칭
> - 대소문자 무시 매칭
> - 저자 이름을 찾지 못했을 때

# 4. 커밋 메시지 생성
copilot -p "Generate commit message for: $(git diff --staged)"

# 출력 예시: "fix(books): support partial author name search"
```

### 버그 수정 워크플로 요약

| 단계 | 작업 | Copilot 명령 |
|------|--------|-----------------|
| 1 | 버그 이해 | `> [버그 설명] @relevant-file.py 유력한 원인을 분석해줘` |
| 2 | 상세 분석 | `> 함수를 보여주고 문제를 설명해줘` |
| 3 | 수정 구현 | `> [구체적인 문제]를 수정해줘` |
| 4 | 테스트 생성 | `> [특정 시나리오]에 대한 테스트를 생성해줘` |
| 5 | 커밋 | `copilot -p "Generate commit message for: $(git diff --staged)"` |

---

# 실습(Practice)

<img src="../images/practice.png" alt="코드가 표시된 모니터, 스탠드 조명, 커피 컵, 헤드폰이 놓인 따뜻한 책상 세팅—실습 준비 완료" width="800"/>

이제 여러분 차례입니다. 이 워크플로를 직접 적용해 보세요.

---

## ▶️ 직접 해보기

데모를 끝낸 뒤, 아래 변형을 시도해 보세요:

1. **버그 탐정 챌린지**: `samples/book-app-buggy/books_buggy.py`의 `mark_as_read` 함수를 Copilot CLI에게 디버깅하게 해보세요. 왜 한 권만이 아니라 모든 책이 읽음 처리되는지 설명했나요?

2. **테스트 챌린지**: book app의 `add_book` 함수에 대한 테스트를 생성해 보세요. Copilot CLI가 포함한 엣지 케이스 중, 여러분이 미처 생각하지 못했던 것이 몇 개나 있나요?

3. **커밋 메시지 챌린지**: book app 파일을 아무거나 조금 수정한 뒤 스테이징(`git add .`)하고, 다음을 실행해 보세요:
   ```bash
   copilot -p "Generate a conventional commit message for: $(git diff --staged)"
   ```
   빠르게 직접 쓴 것보다 메시지가 더 낫나요?

**셀프 체크**: 개발 워크플로를 이해했다면, "이 버그 디버깅해줘"가 "버그 찾아줘"보다 강력한 이유(맥락이 중요함)를 설명할 수 있어야 합니다.

---

## 📝 과제

### 메인 챌린지: 리팩터링하고, 테스트하고, 배포(Ship)하기

실습 예시는 `find_book_by_title`과 코드 리뷰에 초점을 맞췄습니다. 이제 같은 워크플로 스킬을 `book-app-project`의 다른 함수에 적용해 보세요:

1. **리뷰**: Copilot CLI에게 `books.py`의 `remove_book()`을 엣지 케이스/잠재 이슈 관점에서 리뷰하게 하세요:
   `@samples/book-app-project/books.py remove_book() 함수를 리뷰해줘. 제목이 다른 책과 부분적으로 겹칠 때(예: "Dune" vs "Dune Messiah") 어떻게 될까? 처리되지 않은 엣지 케이스가 있나?`
2. **리팩터링**: Copilot CLI에게 `remove_book()`을 개선하여 대소문자 무시 매칭, 책을 찾지 못했을 때 유용한 피드백을 반환하도록 해보세요.
3. **테스트**: 개선된 `remove_book()`에 대해 다음을 커버하는 pytest 테스트를 생성하세요:
   - 존재하는 책 삭제
   - 대소문자 무시 제목 매칭
   - 존재하지 않는 책을 삭제할 때 적절한 피드백 반환
   - 빈 컬렉션에서 삭제
4. **리뷰**: 변경 사항을 스테이징하고 `/review`로 남은 이슈가 없는지 확인하세요.
5. **커밋**: conventional commit 메시지를 생성하세요:
   `copilot -p "Generate a conventional commit message for: $(git diff --staged)"`

<details>
<summary>💡 힌트(클릭하여 펼치기)</summary>

**각 단계의 샘플 프롬프트:**

```bash
copilot

# 1단계: 리뷰
> @samples/book-app-project/books.py remove_book() 함수 리뷰해줘. 어떤 엣지 케이스가 처리되지 않았어?

# 2단계: 리팩터링
> remove_book()을 대소문자 무시 매칭을 사용하도록 개선하고, 책을 찾지 못했을 때 명확한 메시지를 반환하게 해줘. 변경 전/후 코드를 보여줘.

# 3단계: 테스트
> 개선된 remove_book()에 대한 pytest 테스트를 생성해줘. 다음을 포함해줘:
> - 존재하는 책 삭제
> - 대소문자 무시 매칭("dune"이 "Dune"을 삭제)
> - 책을 못 찾았을 때 적절한 응답
> - 빈 컬렉션에서 삭제

# 4단계: 리뷰
> /review

# 5단계: 커밋
> 이 리팩터링에 대한 conventional commit 메시지를 만들어줘
```

**팁:** `remove_book()`을 개선한 뒤, Copilot CLI에게 "이 파일에서 같은 개선이 도움이 될 만한 다른 함수가 있을까?"라고 물어보세요. `find_book_by_title()`이나 `find_by_author()`에 비슷한 개선을 제안할 수도 있습니다.

</details>

### 보너스 챌린지: Copilot CLI로 애플리케이션 만들기

> 💡 **참고**: 이 GitHub Skills 실습은 Python이 아니라 **Node.js**를 사용합니다. 하지만 여기서 연습하는 Copilot CLI 기법(이슈 만들기, 코드 생성, 터미널에서 협업)은 어떤 언어에도 적용됩니다.

이 실습은 Node.js 계산기 앱을 만들면서, 터미널에서 Copilot CLI로 이슈를 만들고, 코드를 생성하고, 템플릿/에이전트를 활용하며, 반복적으로 개발하는 방법을 보여줍니다.

##### <img src="../images/github-skills-logo.png" width="28" align="center" /> ["Copilot CLI로 애플리케이션 만들기" Skills 실습 시작하기](https://github.com/skills/create-applications-with-the-copilot-cli)

---

<details>
<summary>🔧 <strong>자주 하는 실수 & 문제 해결</strong> (클릭하여 펼치기)</summary>

### 자주 하는 실수

| 실수 | 어떤 일이 벌어지나 | 해결 |
|---------|--------------|-----|
| "이 코드 리뷰해줘"처럼 모호한 프롬프트 | 중요한 문제를 놓치는 일반적인 피드백 | 구체적으로: "SQL 인젝션, XSS, 인증 보안 이슈를 확인해줘" |
| 코드 리뷰에 `/review`를 쓰지 않음 | 최적화된 코드 리뷰 에이전트를 놓침 | 높은 신호 대비 낮은 잡음 출력에 튜닝된 `/review` 사용 |
| 맥락 없이 "버그 찾아줘"라고 요청 | 어떤 버그인지 몰라 진단이 어려움 | 증상을 설명: "사용자들이 X를 할 때 Y가 발생" |
| 테스트 프레임워크를 지정하지 않음 | 잘못된 문법/어설션 라이브러리로 테스트가 생성될 수 있음 | "Jest로" 또는 "pytest로"처럼 프레임워크를 명시 |

### 문제 해결

**리뷰가 덜 자세해 보일 때** — 무엇을 확인할지 더 구체적으로 요청하세요:

```bash
copilot

# 대신:
> @samples/book-app-project/book_app.py 리뷰해줘

# 이렇게:
> @samples/book-app-project/book_app.py 를 입력값 검증, 에러 처리, 엣지 케이스 관점에서 리뷰해줘
```

**테스트가 내 프레임워크와 맞지 않을 때** — 프레임워크를 지정하세요:

```bash
copilot

> @samples/book-app-project/books.py pytest로 테스트를 생성해줘 (unittest 말고)
```

**리팩터링이 동작을 바꿀 때** — 동작 보존을 강조하세요:

```bash
copilot

> @samples/book-app-project/book_app.py 명령 처리 로직을 딕셔너리 디스패치로 리팩터링해줘. 중요: 외부 동작은 완전히 동일해야 해 - breaking change 금지
```

</details>

---

# 요약

## 🔑 핵심 정리

<img src="images/specialized-workflows.png" alt="모든 작업을 위한 특화 워크플로: 코드 리뷰, 리팩터링, 디버깅, 테스트, Git 통합" width="800"/>

1. **코드 리뷰**는 구체적인 프롬프트로 더 포괄적이 된다
2. **리팩터링**은 테스트를 먼저 생성하면 더 안전해진다
3. **디버깅**은 에러와 코드를 함께 보여주면 효과가 커진다
4. **테스트 생성**은 엣지 케이스와 에러 시나리오를 포함해야 한다
5. **Git 통합**은 커밋 메시지와 PR 설명을 자동화한다

> 📋 **빠른 참고**: 전체 명령과 단축키 목록은 [GitHub Copilot CLI 명령 레퍼런스](https://docs.github.com/en/copilot/reference/cli-command-reference)를 확인하세요.

---

## ✅ 체크포인트: 핵심을 마스터했습니다

**축하합니다!** 이제 GitHub Copilot CLI로 생산적으로 일하기 위한 핵심 역량을 갖췄습니다:

| 스킬 | 챕터 | 이제 할 수 있는 것 |
|-------|---------|----------------|
| 기본 명령 | Ch 01 | 인터랙티브 모드, 플랜 모드, 프로그래매틱 모드(-p), 슬래시 명령 사용 |
| 컨텍스트 | Ch 02 | `@`로 파일 참조, 세션 관리, 컨텍스트 윈도우 이해 |
| 워크플로 | Ch 03 | 코드 리뷰, 리팩터링, 디버깅, 테스트 생성, git 통합 |

챕터 04~06에는 더 많은 기능이 있으며, 시간을 내서 학습할 가치가 있습니다.

---

## 🛠️ 나만의 워크플로 만들기

GitHub Copilot CLI를 사용하는 ‘정답’은 하나가 아닙니다. 나만의 패턴을 만들 때 도움이 되는 몇 가지 팁입니다:

> 📚 **공식 문서**: 권장 워크플로와 팁은 [Copilot CLI best practices](https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices)를 참고하세요.

- **비교적 복잡한 작업은 `/plan`부터 시작**하세요. 실행 전에 계획을 다듬으면 결과가 좋아집니다.
- **잘 통하는 프롬프트는 저장**해 두세요. Copilot CLI가 실수했을 때 무엇이 문제였는지 기록해 두면, 시간이 지날수록 개인 플레이북이 됩니다.
- **자유롭게 실험**하세요. 어떤 개발자는 길고 자세한 프롬프트를 선호하고, 어떤 개발자는 짧은 프롬프트+후속 질문을 선호합니다. 자연스러운 방식을 찾아보세요.

> 💡 **다음 챕터 예고**: 챕터 04와 05에서는, Copilot CLI가 자동으로 로드하는 커스텀 인스트럭션과 스킬로 베스트 프랙티스를 ‘코드화’하는 방법을 배웁니다.

---

## ➡️ 다음은 무엇인가요?

남은 챕터들은 Copilot CLI의 역량을 확장하는 기능을 다룹니다:

| 챕터 | 다루는 내용 | 필요해지는 순간 |
|---------|----------------|---------------------|
| Ch 04: 에이전트 | 특화된 AI 페르소나 만들기 | 도메인 전문가(프론트엔드, 보안)가 필요할 때 |
| Ch 05: 스킬 | 반복 작업용 인스트럭션 자동 로드 | 같은 프롬프트를 자주 반복할 때 |
| Ch 06: MCP | 외부 서비스 연결 | GitHub/DB 등에서 라이브 데이터가 필요할 때 |

**추천**: 핵심 워크플로를 일주일 정도 사용해 보고, 구체적 니즈가 생기면 챕터 04~06으로 돌아오세요.

---

## 추가 주제로 계속

**[챕터 04: 에이전트와 커스텀 인스트럭션](../04-agents-custom-instructions/README.md)**에서는 다음을 배웁니다:

- 내장 에이전트(`/plan`, `/review`) 사용
- `.agent.md` 파일로 특화 에이전트(프론트엔드 전문가, 보안 감사자) 만들기
- 멀티 에이전트 협업 패턴
- 프로젝트 표준을 위한 커스텀 인스트럭션 파일

---

**[← 챕터 02로 돌아가기](../02-context-conversations/README.md)** | **[챕터 04로 계속 →](../04-agents-custom-instructions/README.md)"