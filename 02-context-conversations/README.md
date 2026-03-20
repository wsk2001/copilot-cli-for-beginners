![챕터 02: 컨텍스트와 대화](images/chapter-header.png)

> **만약 AI가 한 번에 파일 하나가 아니라, 코드베이스 전체를 볼 수 있다면 어떨까요?**

이 장에서는 GitHub Copilot CLI의 진짜 힘인 **컨텍스트(context)** 를 열어봅니다. `@` 문법을 사용해 파일과 디렉터리를 참조함으로써 Copilot CLI가 코드베이스를 더 깊이 이해하도록 만드는 방법을 배우게 됩니다. 또한 세션을 넘나들며 대화를 이어가고, 며칠 뒤에도 중단한 지점에서 정확히 다시 시작하는 방법을 익힙니다. 마지막으로 여러 파일을 함께 분석할 때, 단일 파일 리뷰로는 절대 잡히지 않는 버그를 어떻게 찾아내는지도 확인합니다.

## 🎯 학습 목표

이 장을 마치면 다음을 할 수 있습니다:

- `@` 문법으로 파일, 디렉터리, 이미지 참조하기
- `--resume`, `--continue`로 이전 세션 이어서 작업하기
- [컨텍스트 윈도우](../GLOSSARY.md#context-window)가 어떻게 동작하는지 이해하기
- 여러 턴(multi-turn) 대화를 효과적으로 작성하기
- 여러 프로젝트를 다루는 워크플로에서 디렉터리 권한 관리하기

> ⏱️ **예상 소요 시간**: 약 50분 (읽기 20분 + 실습 30분)

---

## 🧩 현실 비유: 동료와 함께 일하기

<img src="images/colleague-context-analogy.png" alt="컨텍스트가 만드는 차이 - 컨텍스트 없음 vs 컨텍스트 있음" width="800"/>

*동료와 마찬가지로 Copilot CLI도 마음을 읽지 못합니다. 더 많은 정보를 제공하면 사람도 Copilot도 더 정확하고 목적에 맞는 도움을 줄 수 있어요!* 

동료에게 버그를 설명한다고 상상해보세요:

> **컨텍스트 없이**: "책 앱이 안 돼요."

> **컨텍스트와 함께**: "`books.py`를 봐 주세요. 특히 `find_book_by_title` 함수가 대소문자 구분 없이(case-insensitive) 매칭을 하지 못하고 있어요."

Copilot CLI에 컨텍스트를 제공하려면 *`@` 문법*을 사용해 Copilot CLI가 특정 파일을 보도록 지정하면 됩니다.

---

# 필수: 기본 컨텍스트

<img src="images/essential-basic-context.png" alt="Copilot CLI 대화에서 컨텍스트가 흐르는 모습을 표현한, 빛나는 코드 블록과 빛의 궤적" width="800"/>

이 섹션은 컨텍스트를 효과적으로 다루기 위해 꼭 필요한 내용을 다룹니다. 먼저 이 기본기를 확실히 익혀보세요.

---

## @ 문법

프롬프트에서 `@` 기호는 파일과 디렉터리를 참조합니다. Copilot CLI에게 "이 파일을 봐"라고 지시하는 방법입니다.

> 💡 **참고**: 이 과정의 모든 예시는 이 저장소에 포함된 `samples/` 폴더를 사용합니다. 그래서 모든 명령을 그대로 따라 해볼 수 있어요.

### 지금 바로 해보기 (설정 불필요)

내 컴퓨터에 있는 어떤 파일로도 테스트할 수 있습니다:

```bash
copilot

# 가지고 있는 아무 파일이나 지정해보기
> @package.json이 하는 일을 설명해줘
> @README.md를 요약해줘
> @.gitignore에는 뭐가 있고 왜 필요해?
```

> 💡 **프로젝트가 없나요?** 빠르게 테스트 파일을 만들어보세요:
> ```bash
> echo "def greet(name): return 'Hello ' + name" > test.py
> copilot
> > @test.py가 하는 일이 뭐야?
> ```

### 기본 @ 패턴

| 패턴 | 의미 | 사용 예 |
|---------|--------------|-------------|
| `@file.py` | 단일 파일 참조 | `Review @samples/book-app-project/books.py` |
| `@folder/` | 디렉터리 내 모든 파일 참조 | `Review @samples/book-app-project/` |
| `@file1.py @file2.py` | 여러 파일을 함께 참조 | `Compare @samples/book-app-project/book_app.py @samples/book-app-project/books.py` |

### 단일 파일 참조하기

```bash
copilot

> @samples/book-app-project/utils.py가 하는 일을 설명해줘
```

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![파일 컨텍스트 데모](images/file-context-demo.gif)

*데모 출력은 환경에 따라 달라질 수 있습니다. 모델, 도구, 응답은 화면에 보이는 것과 다를 수 있어요.*

</details>

---

### 여러 파일 참조하기

```bash
copilot

> @samples/book-app-project/book_app.py와 @samples/book-app-project/books.py를 일관성 관점에서 비교해줘
```

### 디렉터리 전체 참조하기

```bash
copilot

> @samples/book-app-project/ 안의 모든 파일을 검토해서 에러 처리가 어떤지 봐줘
```

---

## 파일 간 인텔리전스(Cross-File Intelligence)

여기서부터 컨텍스트가 ‘초능력’이 됩니다. 단일 파일 분석은 유용하지만, 여러 파일을 함께 보는 분석은 완전히 다른 차원의 도움을 줍니다.

<img src="images/cross-file-intelligence.png" alt="파일 간 인텔리전스 - 단일 파일 분석 vs 여러 파일 분석을 비교하며, 함께 분석하면 버그/데이터 흐름/패턴을 더 잘 드러낸다는 내용" width="800"/>

### 데모: 여러 파일에 걸친 버그 찾기

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/books.py
>
> 이 파일들은 어떻게 함께 동작해? 데이터 흐름은 어떻게 돼?
```

> 💡 **고급 옵션**: 보안 관점의 파일 간 분석을 하고 싶다면, Python 보안 예시를 시도해보세요:
> ```bash
> > @samples/buggy-code/python/user_service.py @samples/buggy-code/python/payment_processor.py
> > 두 파일 모두에 걸친 보안 취약점을 찾아줘
> ```

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![멀티 파일 데모](images/multi-file-demo.gif)

*데모 출력은 환경에 따라 달라질 수 있습니다. 모델, 도구, 응답은 화면에 보이는 것과 다를 수 있어요.*

</details>

---

**Copilot CLI가 찾아내는 것들**:

```
모듈 간 분석
============= 

1. 데이터 흐름 패턴
   book_app.py는 BookCollection 인스턴스를 만들고 메서드를 호출
   books.py는 BookCollection 클래스를 정의하고 데이터 저장을 관리

   흐름: book_app.py (UI) → books.py (비즈니스 로직) → data.json (저장소)

2. 중복된 출력 함수
   book_app.py:9-21    show_books() 함수
   utils.py:28-36      print_books() 함수

   영향: 거의 동일한 두 함수가 같은 일을 함. 한쪽을 업데이트하면
   다른 쪽도 잊지 않고 업데이트해야 함.

3. 일관되지 않은 에러 처리
   book_app.py는 연도 변환에서 ValueError를 처리
   books.py는 에러 시 조용히 None/False를 반환

   패턴: 모듈 간 에러 처리 방식이 통일되어 있지 않음
```

**왜 중요할까요?** 단일 파일 리뷰로는 큰 그림을 놓치기 쉽습니다. 파일 간 분석을 해야만 다음을 발견할 수 있어요:
- **중복 코드**(통합이 필요)
- 컴포넌트가 어떻게 상호작용하는지 보여주는 **데이터 흐름 패턴**
- 유지보수성에 영향을 주는 **아키텍처 이슈**

---

### 데모: 60초 만에 코드베이스 이해하기

<img src="images/codebase-understanding.png" alt="수동 코드 리뷰(1시간) vs AI 지원 분석(10초)을 비교하는 분할 화면" width="800" />

새 프로젝트에 합류했나요? Copilot CLI로 빠르게 파악할 수 있습니다.

```bash
copilot

> @samples/book-app-project/
>
> 한 문단으로 이 앱이 뭘 하는지, 가장 큰 품질 이슈가 뭔지 말해줘
```

**얻을 수 있는 결과**:
```
이것은 JSON 파일에 저장된 책을 추가/목록/삭제/검색할 수 있는 CLI 책 컬렉션 관리자입니다.
가장 큰 품질 이슈는 다음과 같습니다:

1. 출력 로직 중복 - show_books()와 print_books()가 같은 일을 함
2. 일관성 없는 에러 처리 - 어떤 에러는 예외를 던지고, 어떤 것은 False를 반환
3. 입력 검증 부족 - year가 0일 수 있고, title/author에 빈 문자열도 허용
4. 테스트 부재 - find_book_by_title 같은 핵심 함수에 대한 테스트 커버리지 없음

우선순위: 중복 출력 함수를 통합하고 입력 검증을 추가하기
```
**결과**: 1시간 걸릴 코드 읽기가 10초로 압축됩니다. 어디에 집중해야 할지 바로 알 수 있어요.

---

## 실전 예시

### 예시 1: 컨텍스트 기반 코드 리뷰

```bash
copilot

> @samples/book-app-project/books.py 이 파일에서 잠재적인 버그를 리뷰해줘

# 이제 Copilot CLI는 전체 파일 내용을 알고, 구체적인 피드백을 줄 수 있습니다:
# "49번째 줄: 대소문자 구분 비교 때문에 책을 놓칠 수 있음..."
# "29번째 줄: JSON 디코드 에러를 잡지만 데이터 손상은 로그에 남지 않음..."

> @samples/book-app-project/book_app.py는 어때?

# 이제 book_app.py도 리뷰하지만, 여전히 books.py의 컨텍스트를 알고 있음
```

### 예시 2: 코드베이스 이해하기

```bash
copilot

> @samples/book-app-project/books.py 이 모듈은 무슨 역할이야?

# Copilot CLI가 books.py를 읽고 BookCollection 클래스를 이해함

> @samples/book-app-project/ 코드 구조를 한 번에 개요로 설명해줘

# Copilot CLI가 디렉터리를 스캔하고 요약함

> 앱은 책을 어떻게 저장하고 로드해?

# Copilot CLI는 이미 본 코드를 바탕으로 흐름을 추적할 수 있음
```

<details>
<summary>🎬 여러 턴 대화 예시 보기!</summary>

![멀티 턴 데모](images/multi-turn-demo.gif)

*데모 출력은 환경에 따라 달라질 수 있습니다. 모델, 도구, 응답은 화면에 보이는 것과 다를 수 있어요.*

</details>

### 예시 3: 여러 파일 리팩터링

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/utils.py
> show_books()와 print_books()가 중복으로 보이는데, 통합하도록 도와줘.

# Copilot CLI는 두 파일을 모두 보고, 중복 코드를 합치는 방법을 제안할 수 있음
```

---

## 세션 관리

작업하는 동안 세션은 자동으로 저장됩니다. 이전 세션을 다시 열어서 중단한 지점부터 계속 작업할 수 있어요.

### 세션 자동 저장

모든 대화는 자동으로 저장됩니다. 정상적으로 종료하기만 하면 됩니다:

```bash
copilot

> @samples/book-app-project/ 모든 모듈에서 에러 처리를 개선해보자

[... 작업 ...]

> /exit
```

### 가장 최근 세션 이어서 하기

```bash
# 중단한 지점에서 계속
copilot --continue
```

### 특정 세션 이어서 하기

```bash
# 세션 목록에서 대화형으로 선택
copilot --resume

# 또는 ID로 특정 세션 재개
copilot --resume abc123
```

> 💡 **세션 ID는 어떻게 찾나요?** 외울 필요 없습니다. `copilot --resume`를 ID 없이 실행하면 이전 세션 목록(이름, ID, 마지막 활동 시각)이 대화형으로 표시됩니다. 거기서 원하는 것을 고르면 됩니다.
>
> **터미널이 여러 개라면?** 터미널 창마다 세션이 별도로 존재하며 각각의 컨텍스트를 가집니다. Copilot CLI를 세 개 터미널에서 켜두면 세 개의 세션이 생기는 셈입니다. 어떤 터미널에서든 `--resume`로 전체 목록을 탐색할 수 있고, `--continue`는 어떤 터미널에서든 *가장 마지막에 닫힌* 세션을 이어줍니다.
>
> **재시작 없이 세션을 바꿀 수 있나요?** 네. 활성 세션 안에서 `/resume` 슬래시 명령을 사용하세요:
> ```
> > /resume
> # 전환할 세션 목록 표시
> ```

### 세션 정리하기

나중에 찾기 쉽게 세션에 의미 있는 이름을 붙여보세요:

```bash
copilot

> /rename book-app-review
# 세션 이름이 바뀌어 찾기 쉬워짐
```

### 컨텍스트 확인 및 관리

파일과 대화가 쌓이면 Copilot CLI의 [컨텍스트 윈도우](../GLOSSARY.md#context-window)가 점점 채워집니다. 두 가지 명령이 제어에 도움이 됩니다:

```bash
copilot

> /context
컨텍스트 사용량: 45,000 / 128,000 토큰 (35%)

> /clear
# 컨텍스트를 지우고 새로 시작. 주제가 바뀔 때 유용
```

> 💡 **`/clear`를 언제 쓰나요?** 예를 들어 `books.py`를 리뷰하다가 `utils.py` 이야기로 넘어가고 싶다면 `/clear`를 먼저 실행하세요. 이전 주제의 컨텍스트가 남아 있으면 응답이 헷갈릴 수 있습니다.

---

### 중단한 지점에서 다시 시작하기

<img src="images/session-persistence-timeline.png" alt="GitHub Copilot CLI 세션이 여러 날에 걸쳐 유지되는 타임라인 - 월요일 시작, 수요일에 컨텍스트 그대로 재개" width="800" />

*세션은 종료 시 자동 저장됩니다. 며칠 뒤에도 파일/이슈/진행 상황까지 포함해 완전한 컨텍스트로 재개할 수 있어요.*

여러 날에 걸친 워크플로를 상상해보세요:

```bash
# 월요일: 책 앱 리뷰 시작
copilot

> /rename book-app-review
> @samples/book-app-project/books.py
> 코드 품질 이슈를 전부 찾아 번호를 매겨줘

발견된 품질 이슈:
1. 출력 함수 중복 (book_app.py & utils.py) - 중간
2. 빈 문자열 입력에 대한 검증 없음 - 중간
3. year가 0 또는 음수일 수 있음 - 낮음
4. 모든 함수에 타입 힌트가 없음 - 낮음
5. 에러 로깅 누락 - 낮음

> 이슈 #1(중복 함수)을 고쳐줘
# 수정 작업...

> /exit
```
```bash
# 수요일: 중단한 지점에서 그대로 재개
copilot --continue

> 책 앱 리뷰에서 아직 해결되지 않은 이슈가 뭐였지?

book-app-review 세션에서 남은 이슈:
2. 빈 문자열 입력에 대한 검증 없음 - 중간
3. year가 0 또는 음수일 수 있음 - 낮음
4. 모든 함수에 타입 힌트가 없음 - 낮음
5. 에러 로깅 누락 - 낮음

이슈 #1(중복 함수)은 월요일에 해결됨.

> 그럼 이슈 #2를 하자
```

**이게 강력한 이유**: 며칠 뒤에도 Copilot CLI는 다음을 기억합니다:
- 작업 중이던 정확한 파일
- 번호 매긴 이슈 목록
- 이미 해결한 항목
- 대화의 흐름과 배경

다시 설명할 필요도, 다시 읽을 필요도 없이 바로 이어서 작업할 수 있습니다.

---

**🎉 이제 필수는 끝났습니다!** `@` 문법, 세션 관리(`--continue`/`--resume`/`/rename`), 컨텍스트 명령(`/context`/`/clear`)만으로도 생산성을 크게 높일 수 있어요. 아래 내용은 선택 사항입니다. 필요할 때 다시 돌아와도 됩니다.

---

# 선택: 더 깊게 알아보기

<img src="images/optional-going-deeper.png" alt="컨텍스트 개념을 더 깊게 탐색하는 것을 상징하는 파란/보라 톤의 추상적인 크리스탈 동굴" width="800" />

아래 주제들은 위의 필수 내용을 바탕으로 확장합니다. **관심 있는 것만 골라 보거나, [Practice](#practice)로 바로 넘어가도 됩니다.**

| 이런 걸 배우고 싶어요... | 이동 |
|---|---|
| 와일드카드 패턴과 고급 세션 명령 | [추가 @ 패턴 & 세션 명령](#additional-patterns) |
| 여러 프롬프트에 걸쳐 컨텍스트를 쌓아가는 대화 | [컨텍스트 인지 대화](#context-aware-conversations) |
| 토큰 제한과 `/compact` | [컨텍스트 윈도우 이해하기](#understanding-context-windows) |
| 어떤 파일을 참조할지 고르는 방법 | [무엇을 참조할지 선택하기](#choosing-what-to-reference) |
| 스크린샷/목업 분석 | [이미지 다루기](#working-with-images) |

<details>
<summary><strong>추가 @ 패턴 & 세션 명령</strong></summary>
<a id="additional-patterns"></a>

### 추가 @ 패턴

파워 유저를 위해 Copilot CLI는 와일드카드 패턴과 이미지 참조도 지원합니다:

| 패턴 | 의미 |
|---------|--------------|
| `@folder/*.py` | 폴더 내 모든 .py 파일 |
| `@**/test_*.py` | 재귀 와일드카드: 어디에 있든 test 파일 찾기 |
| `@image.png` | UI 리뷰용 이미지 파일 |

```bash
copilot

> @samples/book-app-project/**/*.py 에서 TODO 주석을 전부 찾아줘
```

### 세션 정보 보기

```bash
copilot

> /session
# 현재 세션 상세와 워크스페이스 요약 표시

> /usage
# 세션 지표와 통계 표시
```

### 세션 공유하기

```bash
copilot

> /share file ./my-session.md
# 세션을 마크다운 파일로 내보내기

> /share gist
# 세션을 GitHub Gist로 생성
```

</details>

<details>
<summary><strong>컨텍스트 인지 대화</strong></summary>
<a id="context-aware-conversations"></a>

### 컨텍스트 인지 대화

진짜 마법은 여러 턴에 걸쳐 대화를 이어가며, 이전 내용을 바탕으로 점점 더 발전시키는 데서 일어납니다.

#### 예시: 점진적 개선(Progressive Enhancement)

```bash
copilot

> @samples/book-app-project/books.py BookCollection 클래스를 리뷰해줘

Copilot CLI: "클래스는 동작하는 것 같지만 다음이 보여요:
1. 일부 메서드에 타입 힌트 누락
2. 빈 title/author에 대한 검증 없음
3. 더 나은 에러 처리가 있으면 좋겠음"

> 모든 메서드에 타입 힌트를 추가해줘

Copilot CLI: "완전한 타입 힌트를 적용한 버전은 다음과 같아요..."
[타입 추가 버전 표시]

> 이제 에러 처리를 개선해줘

Copilot CLI: "타입이 적용된 버전을 바탕으로, 개선된 에러 처리를 이렇게 적용할 수 있어요..."
[검증 및 적절한 예외 추가]

> 최종 버전에 대한 테스트를 생성해줘

Copilot CLI: "타입과 에러 처리가 적용된 클래스를 기준으로..."
[포괄적인 테스트 생성]
```

각 프롬프트가 이전 작업 위에 쌓이는 것을 확인할 수 있습니다. 이것이 컨텍스트의 힘입니다.

</details>

<details>
<summary><strong>컨텍스트 윈도우 이해하기</strong></summary>
<a id="understanding-context-windows"></a>

### 컨텍스트 윈도우 이해하기

필수 파트에서 `/context`, `/clear`를 이미 보았습니다. 여기서는 컨텍스트 윈도우가 어떻게 동작하는지 더 깊게 살펴봅니다.

모든 AI에는 한 번에 고려할 수 있는 텍스트의 양인 "컨텍스트 윈도우"가 있습니다.

<img src="images/context-window-visualization.png" alt="컨텍스트 윈도우 시각화" width="800" />

*컨텍스트 윈도우는 책상과 비슷합니다. 한 번에 올려둘 수 있는 공간은 제한적이며, 파일/대화 히스토리/시스템 프롬프트가 모두 공간을 차지합니다.*

#### 한계에 도달하면 무슨 일이 일어나나요?

```bash
copilot

> /context

컨텍스트 사용량: 45,000 / 128,000 토큰 (35%)

# 파일과 대화가 늘어나면 이 수치는 계속 증가

> @large-codebase/

컨텍스트 사용량: 120,000 / 128,000 토큰 (94%)

# 경고: 컨텍스트 한계에 근접

> @another-large-file.py

컨텍스트 한계 도달. 오래된 컨텍스트는 요약됩니다.
```

#### `/compact` 명령

컨텍스트가 가득 차 가는데 대화 흐름은 유지하고 싶다면, `/compact`가 히스토리를 요약해 토큰을 확보해줍니다:

```bash
copilot

> /compact
# 대화 히스토리를 요약해 컨텍스트 공간을 확보
# 핵심 발견과 결정은 유지됨
```

#### 컨텍스트 효율 팁

| 상황 | 행동 | 이유 |
|-----------|--------|-----|
| 새 주제 시작 | `/clear` | 관련 없는 컨텍스트 제거 |
| 대화가 너무 길어짐 | `/compact` | 히스토리를 요약해 토큰 확보 |
| 특정 파일만 필요 | `@folder/`보다 `@file.py` | 필요한 것만 불러와 컨텍스트 절약 |
| 한계에 자주 도달 | 새 세션 시작 | 새로운 128K 컨텍스트 |
| 주제가 여러 개 | 주제별로 `/rename` | 올바른 세션을 쉽게 재개 |

#### 대규모 코드베이스 베스트 프랙티스

1. **구체적으로 지정**: `@samples/book-app-project/` 대신 `@samples/book-app-project/books.py`
2. **주제 전환 시 초기화**: 포커스가 바뀌면 `/clear`
3. **`/compact` 활용**: 대화를 요약해 토큰 확보
4. **세션 분리**: 기능/주제별로 세션을 나눠 사용

</details>

<details>
<summary><strong>무엇을 참조할지 선택하기</strong></summary>
<a id="choosing-what-to-reference"></a>

### 무엇을 참조할지 선택하기

컨텍스트 관점에서 모든 파일이 똑같은 가치를 갖는 건 아닙니다. 효율적으로 선택하는 법을 알아봅시다.

#### 파일 크기 고려

| 파일 크기 | 대략적인 [토큰](../GLOSSARY.md#token) | 전략 |
|-----------|-------------------|----------|
| 작음 (<100줄) | 약 500~1,500 토큰 | 자유롭게 참조 |
| 중간 (100~500줄) | 약 1,500~7,500 토큰 | 특정 파일 위주로 참조 |
| 큼 (500+줄) | 7,500+ 토큰 | 선택적으로, 구체적으로 |
| 매우 큼 (1000+줄) | 15,000+ 토큰 | 분리하거나 필요한 부분만 지정 고려 |

**구체적 예시:**
- 책 앱의 Python 파일 4개 합계 ≈ 2,000~3,000 토큰
- 일반적인 Python 모듈(200줄) ≈ 3,000 토큰
- Flask API 파일(400줄) ≈ 6,000 토큰
- package.json ≈ 200~500 토큰
- 짧은 프롬프트 + 응답 ≈ 500~1,500 토큰

> 💡 **코드 토큰 빠른 추정:** 코드 줄 수 × 약 15 ≈ 토큰. (대략적인 추정치입니다.)

#### 포함할 것 vs 제외할 것

**가치가 큰 것(포함 추천):**
- 엔트리 포인트 (`book_app.py`, `main.py`, `app.py`)
- 지금 질문하는 대상 파일
- 대상 파일이 직접 import하는 파일
- 설정 파일 (`requirements.txt`, `pyproject.toml`)
- 데이터 모델/데이터클래스

**가치가 낮은 것(제외 고려):**
- 생성된 파일(컴파일 산출물, 번들된 에셋)
- node_modules나 vendor 디렉터리
- 큰 데이터 파일/픽스처
- 질문과 무관한 파일

#### 구체성 스펙트럼

```
덜 구체적 ────────────────────────► 더 구체적
@samples/book-app-project/                      @samples/book-app-project/books.py:47-52
     │                                       │
     └─ 전부 스캔(컨텍스트 더 많이 사용)           └─ 필요한 부분만(컨텍스트 절약)
```

**넓게 가야 할 때** (`@samples/book-app-project/`):
- 코드베이스 초반 탐색
- 여러 파일에 걸친 패턴 찾기
- 아키텍처 리뷰

**좁게 가야 할 때** (`@samples/book-app-project/books.py`):
- 특정 이슈 디버깅
- 특정 파일 코드 리뷰
- 특정 함수 질문

#### 실전 예시: 단계적으로 컨텍스트 로딩하기

```bash
copilot

# 1단계: 구조부터 파악
> @package.json 이 프로젝트는 어떤 프레임워크를 써?

# 2단계: 답변을 바탕으로 범위를 좁힘
> @samples/book-app-project/ 프로젝트 구조를 보여줘

# 3단계: 핵심에 집중
> @samples/book-app-project/books.py BookCollection 클래스를 리뷰해줘

# 4단계: 필요할 때만 관련 파일 추가
> @samples/book-app-project/book_app.py @samples/book-app-project/books.py CLI가 BookCollection을 어떻게 쓰는지 설명해줘
```

이렇게 단계적으로 접근하면 컨텍스트를 더 집중적이고 효율적으로 사용할 수 있습니다.

</details>

<details>
<summary>이미지 다루기</summary>
<a id="working-with-images"></a>

### 이미지 다루기

`@` 문법으로 이미지를 대화에 포함할 수 있고, 클립보드에서 그대로 붙여넣기(Cmd+V / Ctrl+V)도 가능합니다. Copilot CLI는 스크린샷/목업/다이어그램을 분석해 UI 디버깅, 디자인 구현, 에러 분석에 도움을 줄 수 있습니다.

```bash
copilot

> @images/screenshot.png 이 이미지에서 무슨 일이 벌어지고 있어?

> @images/mockup.png 이 디자인과 동일하게 보이도록 HTML/CSS를 작성해줘. index.html에 HTML을 두고, styles.css에 CSS를 넣어줘.
```

> 📖 **더 알아보기**: 지원 형식, 실전 활용, 이미지와 코드를 함께 쓰는 팁은 [추가 컨텍스트 기능](../appendices/additional-context.md#working-with-images)에서 확인하세요.

</details>

---

# Practice

<img src="../images/practice.png" alt="실습 준비가 된 따뜻한 책상 세팅(모니터/스탠드/커피/헤드폰)" width="800" />

이제 컨텍스트와 세션 관리 스킬을 직접 적용해 봅시다.

---

## ▶️ 직접 해보기

### 전체 프로젝트 리뷰

이 과정에는 바로 리뷰할 수 있는 샘플 파일이 포함되어 있습니다. copilot을 시작한 뒤, 다음 프롬프트를 실행해보세요:

```bash
copilot

> @samples/book-app-project/ 이 프로젝트의 코드 품질 리뷰를 해줘

# Copilot CLI는 다음 같은 이슈를 찾아낼 수 있습니다:
# - 출력 함수 중복
# - 입력 검증 부족
# - 일관성 없는 에러 처리
```

> 💡 **내 파일로 해보고 싶나요?** 작은 Python 프로젝트를 만들어보세요(`mkdir -p my-project/src`). .py 파일을 몇 개 추가한 뒤 `@my-project/src/`를 리뷰해보면 됩니다. 원한다면 copilot에게 샘플 코드를 생성해달라고 요청할 수도 있어요!

### 세션 워크플로

```bash
copilot

> /rename book-app-review
> @samples/book-app-project/books.py 빈 제목 입력에 대한 검증을 추가해보자

[Copilot CLI가 검증 접근 방식을 제안]

> 그 수정사항을 구현해줘
> 이제 @samples/book-app-project/에서 중복 출력 함수를 통합하자
> /exit

# 나중에 - 중단한 지점에서 재개
copilot --continue

> 우리가 만든 변경사항에 대한 테스트를 생성해줘
```

---

데모를 끝냈다면, 아래 변형도 시도해보세요:

1. **파일 간 챌린지**: book_app.py와 books.py가 함께 어떻게 동작하는지 분석:
   ```bash
   copilot
   > @samples/book-app-project/book_app.py @samples/book-app-project/books.py
   > 이 파일들의 관계는 뭐야? 코드 스멜이 있을까?
   ```

2. **세션 챌린지**: 세션을 시작하고 `/rename my-first-session`으로 이름을 붙인 뒤, 뭔가 작업하고 `/exit`로 나가세요. 그 다음 `copilot --continue`를 실행해보세요. 정말로 하던 일을 기억하고 있나요?

3. **컨텍스트 챌린지**: 세션 도중 `/context`를 실행해 토큰 사용량을 확인하세요. 그리고 `/compact`를 실행한 뒤 다시 확인해보세요. (자세한 내용은 Going Deeper의 [컨텍스트 윈도우 이해하기](#understanding-context-windows)를 참고하세요.)

**셀프 체크**: `@folder/`가 왜 파일을 하나씩 여는 것보다 더 강력한지 설명할 수 있다면, 컨텍스트를 이해한 것입니다.

---

## 📝 과제

### 메인 챌린지: 데이터 흐름 추적하기

실습 예시는 코드 품질 리뷰와 입력 검증에 집중했습니다. 이번에는 같은 컨텍스트 스킬을 다른 작업에 적용해, 앱에서 데이터가 어떻게 이동하는지 추적해보세요:

1. 대화형 세션 시작: `copilot`
2. `books.py`와 `book_app.py`를 함께 참조:
   `@samples/book-app-project/books.py @samples/book-app-project/book_app.py 사용자 입력으로 들어온 책 정보가 data.json에 저장되기까지의 흐름을 추적해줘. 각 단계에서 어떤 함수가 관여해?`
3. 데이터 파일도 컨텍스트로 추가:
   `@samples/book-app-project/data.json 이 JSON 파일이 없거나 손상되면 어떻게 돼? 어떤 함수가 실패할까?`
4. 파일 간 개선 제안 요청:
   `@samples/book-app-project/books.py @samples/book-app-project/utils.py 두 파일에서 모두 통용되는 일관된 에러 처리 전략을 제안해줘.`
5. 세션 이름 지정: `/rename data-flow-analysis`
6. `/exit`로 종료한 뒤, `copilot --continue`로 재개하고 데이터 흐름에 대한 후속 질문을 해보세요

**성공 기준**: 여러 파일에 걸쳐 데이터를 추적하고, 이름 붙인 세션을 재개하며, 파일 간 제안을 받을 수 있어야 합니다.

<details>
<summary>💡 힌트 (클릭해서 펼치기)</summary>

**시작하기:**
```bash
cd /path/to/copilot-cli-for-beginners
copilot
> @samples/book-app-project/books.py @samples/book-app-project/book_app.py 사용자 입력으로 들어온 책 정보가 data.json에 저장되기까지의 흐름을 추적해줘.
> @samples/book-app-project/data.json 이 파일이 없거나 손상되면 어떻게 돼?
> /rename data-flow-analysis
> /exit
```

그 다음 재개: `copilot --continue`

**유용한 명령:**
- `@file.py` - 단일 파일 참조
- `@folder/` - 폴더 내 모든 파일 참조(끝에 `/` 포함)
- `/context` - 컨텍스트 사용량 확인
- `/rename <name>` - 세션 이름 지정

</details>

### 보너스 챌린지: 컨텍스트 한계

1. `@samples/book-app-project/`로 책 앱 파일을 한 번에 모두 참조
2. 여러 파일( `books.py`, `utils.py`, `book_app.py`, `data.json`)에 대해 자세한 질문을 여러 개 하기
3. `/context`로 사용량을 확인. 얼마나 빨리 차는지 보기
4. `/compact`로 공간을 확보하고 계속 대화하기
5. 더 구체적인 참조(예: 전체 폴더 대신 `@samples/book-app-project/books.py`)로 바꿔 사용량 차이를 확인하기

---

<details>
<summary>🔧 <strong>자주 하는 실수 & 트러블슈팅</strong> (클릭해서 펼치기)</summary>

### 자주 하는 실수

| 실수 | 결과 | 해결 |
|---------|--------------|-----|
| 파일명 앞에 `@`를 뺴먹음 | Copilot CLI가 "books.py"를 일반 텍스트로 취급 | `@samples/book-app-project/books.py`처럼 `@`를 붙이기 |
| 세션이 자동으로 이어질 거라 기대함 | 새로 `copilot`을 시작하면 이전 컨텍스트가 사라짐 | `--continue`(최근) 또는 `--resume`(선택) 사용 |
| 현재 디렉터리 밖의 파일 참조 | "Permission denied" 또는 "File not found" | `/add-dir /path/to/directory`로 접근 허용 |
| 주제 전환 시 `/clear`를 안 씀 | 이전 컨텍스트가 새 주제의 답변을 혼란스럽게 함 | 다른 작업으로 넘어가기 전에 `/clear` 실행 |

### 트러블슈팅

**"File not found" 에러** - 올바른 디렉터리에 있는지 확인하세요:

```bash
pwd  # 현재 디렉터리 확인
ls   # 파일 목록 확인

# 그 다음 copilot을 시작하고 상대 경로 사용
copilot

> @samples/book-app-project/books.py를 리뷰해줘
```

**"Permission denied"** - 디렉터리를 허용 목록에 추가하세요:

```bash
copilot --add-dir /path/to/directory

# 또는 세션 안에서:
> /add-dir /path/to/directory
```

**컨텍스트가 너무 빨리 차요**:
- 파일 참조를 더 구체적으로 하기
- 주제 전환 시 `/clear` 사용
- 작업을 여러 세션으로 분리

</details>

---

# 요약

## 🔑 핵심 정리

1. **`@` 문법**은 Copilot CLI에게 파일/디렉터리/이미지 컨텍스트를 제공합니다.
2. **여러 턴 대화**는 컨텍스트가 누적되며 점점 더 발전합니다.
3. **세션 자동 저장**: `--continue` 또는 `--resume`으로 이어서 작업할 수 있습니다.
4. **컨텍스트 윈도우에는 한계가 있음**: `/context`, `/clear`, `/compact`로 관리하세요.
5. **권한 플래그**(`--add-dir`, `--allow-all`)로 여러 디렉터리 접근을 제어합니다. 신중하게 사용하세요.
6. **이미지 참조**(`@screenshot.png`)로 UI 이슈를 시각적으로 디버깅할 수 있습니다.

> 📚 **공식 문서**: 컨텍스트/세션/파일 작업에 대한 전체 레퍼런스는 [Copilot CLI 사용하기](https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli)를 참고하세요.

> 📋 **퀵 레퍼런스**: 명령 및 단축키 목록은 [GitHub Copilot CLI 명령 레퍼런스](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참고하세요.

---

## ➡️ 다음은?

이제 Copilot CLI에 컨텍스트를 제공하는 방법을 알게 되었으니, 실제 개발 작업에 적용해봅시다. 방금 배운 컨텍스트 테크닉(파일 참조, 파일 간 분석, 세션 관리)은 다음 장에서 다루는 강력한 워크플로의 기반이 됩니다.

**[챕터 03: 개발 워크플로](../03-development-workflows/README.md)** 에서는 다음을 배웁니다:

- 코드 리뷰 워크플로
- 리팩터링 패턴
- 디버깅 지원
- 테스트 생성
- Git 통합

---

**[← 챕터 01로 돌아가기](../01-setup-and-first-steps/README.md)** | **[챕터 03으로 계속 →](../03-development-workflows/README.md)**