![챕터 01: 첫걸음](images/chapter-header.png)

> **AI가 버그를 즉시 찾아내고, 헷갈리는 코드를 설명하며, 바로 실행 가능한 스크립트를 생성하는 모습을 확인해 보세요. 그리고 GitHub Copilot CLI를 사용하는 세 가지 방법을 배웁니다.**

이 장에서 마법이 시작됩니다! 개발자들이 GitHub Copilot CLI를 “스피드 다이얼에 등록해 둔 시니어 엔지니어”라고 말하는 이유를 직접 체험하게 될 거예요. AI가 보안 버그를 몇 초 만에 찾아내고, 복잡한 코드를 쉬운 말로 설명해 주며, 동작하는 스크립트를 즉석에서 만들어 주는 것을 보게 됩니다. 그런 다음 세 가지 상호작용 모드(Interactive, Plan, Programmatic)를 익혀서, 어떤 작업이든 상황에 맞는 모드를 정확히 선택할 수 있게 됩니다.

> ⚠️ **사전 준비**: 아래 데모를 실행하기 전에 먼저 **[챕터 00: 빠른 시작](../00-quick-start/README.md)** 을 완료했는지 확인하세요. GitHub Copilot CLI가 설치되어 있고 인증까지 완료되어 있어야 합니다.

## 🎯 학습 목표

이 장을 마치면 다음을 할 수 있습니다:

- 실습 데모를 통해 GitHub Copilot CLI가 제공하는 생산성 향상을 체감하기
- 어떤 작업이든 올바른 모드(Interactive, Plan, Programmatic)를 선택하기
- 슬래시 명령어로 세션을 제어하기

> ⏱️ **예상 소요 시간**: 약 45분 (읽기 15분 + 실습 30분)

---

# 첫 번째 Copilot CLI 경험

<img src="images/first-copilot-experience.png" alt="책상에 앉아 모니터에 코드가 표시된 개발자와, AI 도움을 상징하는 빛나는 입자" width="800"/>

바로 시작해서 Copilot CLI가 무엇을 할 수 있는지 확인해 보세요.

---

## 익숙해지기: 첫 프롬프트

인상적인 데모로 들어가기 전에, 지금 바로 시도해 볼 수 있는 간단한 프롬프트부터 시작해 봅시다. **코드 저장소도 필요 없습니다!** 터미널을 열고 Copilot CLI를 실행하기만 하면 됩니다:

```bash
copilot
```

초보자에게 친화적인 프롬프트 예시는 다음과 같습니다:

```
> Python에서 dataclass가 무엇인지 쉽게 설명해 줘

> 특정 키 기준으로 딕셔너리 리스트를 정렬하는 함수를 작성해 줘

> Python에서 list와 tuple의 차이점이 뭐야?

> 깔끔한 Python 코드를 작성하기 위한 모범 사례 5가지를 알려줘
```

Python을 쓰지 않나요? 괜찮습니다! 사용하는 언어에 대해 질문하면 됩니다.

어색함 없이 자연스럽게 느껴질 거예요. 동료에게 질문하듯 편하게 물어보면 됩니다. 탐색이 끝나면 `/exit`를 입력해 세션을 종료하세요.

**핵심 인사이트**: GitHub Copilot CLI는 대화형입니다. 시작을 위해 특별한 문법이 필요하지 않습니다. 평범한 영어(혹은 자연어)로 질문하면 됩니다.

## 실제로 보기

이제 개발자들이 이것을 “스피드 다이얼에 등록해 둔 시니어 엔지니어”라고 부르는 이유를 확인해 봅시다.

> 📖 **예시 읽는 방법**: `>`로 시작하는 줄은 Copilot CLI의 **대화형(Interactive)** 세션 안에서 입력하는 프롬프트입니다. `>`가 없는 줄은 터미널에서 실행하는 셸 명령어입니다.

> 💡 **예시 출력에 대하여**: 이 과정 전체에 걸쳐 표시되는 샘플 출력은 이해를 돕기 위한 예시입니다. Copilot CLI의 응답은 매번 달라지므로, 실제 결과는 문구/형식/세부 내용이 다를 수 있습니다. 정확한 텍스트가 아니라 반환되는 정보의 *유형*에 집중하세요.

### 데모 1: 몇 초 만에 코드 리뷰

이 과정에는 의도적으로 코드 품질 문제가 들어 있는 샘플 파일이 포함되어 있습니다. 그중 하나를 리뷰해 봅시다:

```bash
# 로컬에서 작업 중이고 아직 클론하지 않았다면 과정 저장소를 클론
git clone https://github.com/github/copilot-cli-for-beginners
cd copilot-cli-for-beginners

# Copilot 시작
copilot
```

대화형 세션에 들어가서 다음을 입력합니다:

```
> @samples/book-app-project/book_app.py 를 코드 품질 관점에서 리뷰하고 개선점을 제안해 줘
```

> 💡 **`@`는 무엇인가요?** `@` 기호는 Copilot CLI에게 “파일을 읽어라”라고 지시합니다. 챕터 02에서 자세히 다룹니다. 지금은 예시대로 그대로 복사해서 실행해 보세요.

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![코드 리뷰 데모](images/code-review-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 여기와 다를 수 있습니다.*

</details>

---

**핵심**: 몇 초 만에 전문적인 코드 리뷰를 받을 수 있습니다. 수동으로 리뷰한다면… 음… 그보다 훨씬 오래 걸리겠죠!

---

### 데모 2: 헷갈리는 코드 설명

코드를 보면서 “이게 대체 뭐 하는 거지?” 하고 멈춰 본 적 있나요? Copilot CLI 세션에서 이렇게 해 보세요:

```
> @samples/book-app-project/books.py 가 무엇을 하는지 쉽게 설명해 줘
```

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![코드 설명 데모](images/explain-code-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 달라질 수 있습니다.*

</details>

---

**무슨 일이 일어나나요?** (출력은 달라질 수 있음) Copilot CLI가 파일을 읽고, 코드를 이해한 다음, 평이한 말로 설명해 줍니다.

```
이것은 Python dataclass를 사용한 책 컬렉션 관리 모듈입니다.

디지털 책장이라고 생각해 보세요:
- Book은 dataclass로, 책 정보(제목, 저자, 연도, 읽음 상태)를 저장하는 간단한 방법입니다.
- BookCollection은 전체 컬렉션을 관리하고 저장/불러오기를 처리합니다.

핵심 구성요소:

1. @dataclass 데코레이터(8번째 줄)
   - __init__, __repr__ 등을 자동으로 생성
   - Python에서 데이터 구조를 깔끔하게 정의하는 방식

2. BookCollection 클래스(16번째 줄)
   - Book 객체 리스트를 유지
   - JSON 파일 I/O로 영속성 처리
   - load_books()는 data.json을 읽음
   - save_books()는 asdict()를 사용해 data.json에 씀

3. 책 관련 동작:
   - add_book() - 새 책을 만들고 저장
   - find_book_by_title() - 제목으로 검색
   - mark_as_read() - 읽음 상태 업데이트
   - find_by_author() - 저자명으로 필터링

일반적인 패턴: JSON에서 읽기 → Python 객체로 작업 → JSON으로 다시 쓰기
```

**핵심**: 복잡한 코드도 인내심 있는 멘토가 설명하듯 풀어줍니다.

---

### 데모 3: 동작하는 코드 생성

구글링에 15분쯤 쓸 뻔한 함수가 필요하신가요? 같은 세션에서 이렇게 해 보세요:

```
> 책 리스트를 받아서 통계를 반환하는 Python 함수를 작성해 줘:
  전체 개수, 읽은 책 수, 안 읽은 책 수, 가장 오래된 책과 가장 최신 책
```

---

<details>
<summary>🎬 실제 동작 보기!</summary>

![코드 생성 데모](images/generate-code-demo.gif)

*데모 출력은 달라질 수 있습니다. 사용 중인 모델, 도구, 응답은 달라질 수 있습니다.*

</details>

---

**무슨 일이 일어나나요?** 몇 초 만에 완전하게 동작하는 함수를 만들어 주며, 복사-붙여넣기-실행이 가능합니다.

탐색이 끝나면 세션을 종료하세요:

```
> /exit
```

**핵심**: 즉각적인 결과를 얻고, 하나의 연속된 세션 안에서 작업을 이어갈 수 있습니다.

---

# 모드와 명령어

<img src="images/modes-and-commands.png" alt="GitHub Copilot CLI의 모드와 명령을 나타내는 미래적인 제어 패널(빛나는 화면과 다이얼, 이퀄라이저)" width="800"/>

방금 Copilot CLI가 할 수 있는 일을 봤습니다. 이제 이 능력들을 *어떻게* 효과적으로 활용할지 이해해 봅시다. 핵심은 상황에 따라 세 가지 상호작용 모드 중 무엇을 써야 하는지 아는 것입니다.

> 💡 **참고**: Copilot CLI에는 입력을 기다리지 않고 작업을 진행하는 **Autopilot** 모드도 있습니다. 강력하지만 전체 권한을 부여해야 하고, 프리미엄 요청을 자율적으로 사용합니다. 이 과정은 아래의 세 가지 모드에 초점을 맞춥니다. 기본기에 익숙해진 뒤 Autopilot을 안내해 드립니다.

---

## 🧩 현실 비유: 외식하기

GitHub Copilot CLI를 사용하는 것은 외식하러 가는 것과 비슷합니다. 이동 경로를 계획하는 것부터 주문을 넣는 것까지, 상황에 따라 다른 접근이 필요하죠:

| 모드 | 외식 비유 | 언제 사용하나 |
|------|----------|---------------|
| **Plan** | 식당까지 가는 GPS 경로 | 복잡한 작업 - 경로를 잡고, 중간 경유를 검토하고, 계획에 합의한 뒤 실행 |
| **Interactive** | 점원(서빙 직원)과 대화 | 탐색과 반복 - 질문하고, 커스터마이즈하고, 실시간 피드백 받기 |
| **Programmatic** | 드라이브스루 주문 | 빠르고 구체적인 작업 - 환경을 유지한 채로 신속하게 결과 얻기 |

외식과 마찬가지로, 각 접근이 언제 자연스러운지 금방 익숙해질 것입니다.

<img src="images/ordering-food-analogy.png" alt="GitHub Copilot CLI를 사용하는 세 가지 방법 - Plan 모드(식당으로 가는 GPS), Interactive 모드(점원과 대화), Programmatic 모드(드라이브스루)" width="800"/>

*작업에 따라 모드를 선택하세요: 먼저 설계가 필요하면 Plan, 대화형 협업은 Interactive, 한 번에 끝내는 질문은 Programmatic*

### 어떤 모드부터 시작해야 하나요?

**Interactive 모드부터 시작하세요.**
- 실험해 보고 후속 질문을 할 수 있습니다
- 대화를 통해 맥락이 자연스럽게 쌓입니다
- `/clear`로 실수를 쉽게 되돌릴 수 있습니다

익숙해지면 다음도 시도해 보세요:
- **Programmatic 모드** (`copilot -p "<프롬프트>"`)로 빠른 1회성 질문
- 더 자세한 설계가 필요할 때 **Plan 모드** (`/plan`)

---

## 세 가지 모드

### 모드 1: Interactive 모드(여기서 시작)

<img src="images/interactive-mode.png" alt="Interactive 모드 - 질문에 답하고 주문을 조정해 주는 점원과 대화하는 것과 비슷" width="250"/>

**적합한 상황**: 탐색, 반복, 여러 턴 대화. 질문에 답해 주고 피드백을 받아 주문을 조정하는 점원과 대화하는 것과 비슷합니다.

대화형 세션 시작:

```bash
copilot
```

사용 가능한 명령어 도움말을 보려면 다음을 입력하세요:

```
> /help
```

**핵심 인사이트**: Interactive 모드는 맥락을 유지합니다. 이전 메시지들이 쌓이며, 실제 대화처럼 이어집니다.

#### Interactive 모드 예시

```bash
copilot

> @samples/book-app-project/utils.py 를 리뷰하고 개선점을 제안해 줘

> 모든 함수에 타입 힌트를 추가해 줘

> 에러 처리를 더 견고하게 만들어 줘

> /exit
```

각 프롬프트가 이전 답변을 기반으로 이어지는 것을 확인할 수 있습니다. 매번 처음부터 시작하는 게 아니라 “대화”를 하는 것입니다.

---

### 모드 2: Plan 모드

<img src="images/plan-mode.png" alt="Plan 모드 - 여행 전에 GPS로 경로를 계획하는 것과 비슷" width="250"/>

**적합한 상황**: 실행 전에 접근 방식을 검토하고 싶은 복잡한 작업. 여행 전에 GPS로 경로를 계획하는 것과 비슷합니다.

Plan 모드는 코드를 쓰기 전에 단계별 계획을 만드는 데 도움을 줍니다. `/plan` 명령을 사용하거나, 대화형 세션 중 **Shift+Tab**으로 Plan 모드로 전환할 수 있습니다:

> 💡 **팁**: **Shift+Tab**은 모드를 순환합니다: Interactive → Plan → Autopilot. 세션 중 언제든 눌러서 모드를 바꿀 수 있습니다.

```bash
copilot

> /plan 책 앱에 "읽음 표시" 명령을 추가해 줘
```

**Plan 모드 출력 예시:** (실제 출력은 달라질 수 있음)

```
📋 구현 계획

Step 1: book_app.py의 명령 처리기 업데이트
  - "mark" 명령을 위한 새로운 elif 분기 추가
  - handle_mark_as_read() 함수 생성

Step 2: 처리기 함수 구현
  - 사용자에게 책 제목을 입력받기
  - collection.mark_as_read(title) 호출
  - 성공/실패 메시지 출력

Step 3: 도움말 텍스트 업데이트
  - 가능한 명령 목록에 "mark" 추가
  - 명령 사용법 문서화

Step 4: 흐름 테스트
  - 책 추가
  - 읽음 표시
  - 목록 출력에서 상태 변경 확인

구현을 진행할까요? [Y/n]
```

**핵심 인사이트**: Plan 모드는 코드가 작성되기 전에 접근 방식을 검토하고 수정할 수 있게 해줍니다. 계획을 완성한 뒤에는 파일로 저장하도록 지시할 수도 있습니다. 예를 들어, “이 계획을 `mark_as_read_plan.md`에 저장해 줘”라고 말하면 계획 내용을 담은 마크다운 파일이 생성됩니다.

> 💡 **더 복잡한 것을 원하나요?** `/plan 책 앱에 검색 및 필터 기능을 추가해 줘`를 시도해 보세요. Plan 모드는 간단한 기능부터 전체 애플리케이션 수준까지 확장됩니다.

> 📚 **Autopilot 모드**: Shift+Tab으로 **Autopilot**이라는 세 번째 모드도 순환되는 것을 보셨을 수 있습니다. Autopilot 모드에서는 Copilot이 각 단계마다 입력을 기다리지 않고 전체 계획을 끝까지 수행합니다. 마치 동료에게 작업을 맡기고 “끝나면 알려줘”라고 말하는 것과 비슷하죠. 일반적인 흐름은 plan → 수락 → autopilot이며, 좋은 계획을 쓰는 능력이 중요합니다. Interactive와 Plan에 익숙해진 뒤 [공식 문서](https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot)를 참고해 보세요.

---

### 모드 3: Programmatic 모드

<img src="images/programmatic-mode.png" alt="Programmatic 모드 - 빠른 주문을 위해 드라이브스루를 이용하는 것과 비슷" width="250"/>

**적합한 상황**: 자동화, 스크립트, CI/CD, 한 번에 끝나는 명령. 점원과 대화하지 않고 빠르게 주문하는 드라이브스루와 비슷합니다.

대화 없이 한 번만 실행할 명령에는 `-p` 플래그를 사용하세요:

```bash
# 코드 생성
copilot -p "어떤 숫자가 짝수인지 홀수인지 확인하는 함수를 작성해 줘"

# 빠른 도움말
copilot -p "Python에서 JSON 파일을 어떻게 읽어?"
```

**핵심 인사이트**: Programmatic 모드는 빠르게 답하고 종료합니다. 대화가 아니라 입력 → 출력입니다.

<details>
<summary>📚 <strong>더 나아가기: 스크립트에서 Programmatic 모드 사용</strong> (클릭하여 펼치기)</summary>

익숙해지면 셸 스크립트에서 `-p`를 사용할 수 있습니다:

```bash
#!/bin/bash

# 커밋 메시지 자동 생성
COMMIT_MSG=$(copilot -p "다음 변경사항에 대한 커밋 메시지를 만들어 줘: $(git diff --staged)")
git commit -m "$COMMIT_MSG"

# 파일 리뷰
copilot --allow-all -p "@myfile.py 를 리뷰해서 문제를 찾아 줘"
```

> ⚠️ **`--allow-all`에 대하여**: 이 플래그는 권한 요청 프롬프트를 모두 건너 뛰고, Copilot CLI가 파일을 읽고/명령을 실행하고/URL에 접근하도록 허용합니다. Programmatic 모드(`-p`)는 대화형 세션이 아니므로 승인할 기회가 없어서 필요한 경우가 있습니다. 반드시 본인이 작성한 프롬프트에만 사용하고, 신뢰할 수 있는 디렉터리에서만 사용하세요. 신뢰할 수 없는 입력이나 민감한 디렉터리에는 절대 사용하지 마세요.

</details>

---

## 필수 슬래시 명령어

이 명령들은 Interactive 모드에서 동작합니다. **이 6개만 먼저 익히세요** — 일상 사용의 90%를 커버합니다:

| 명령어 | 하는 일 | 언제 사용하나 |
|--------|--------|--------------|
| `/help` | 사용 가능한 명령어 표시 | 명령어가 기억나지 않을 때 |
| `/clear` | 대화를 지우고 새로 시작 | 주제를 바꿀 때 |
| `/plan` | 구현 전에 작업 계획 세우기 | 더 복잡한 기능을 다룰 때 |
| `/research` | GitHub 및 웹 소스를 활용한 심층 조사 | 코딩 전 조사/탐색이 필요할 때 |
| `/model` | AI 모델 표시/전환 | 다른 모델로 바꾸고 싶을 때 |
| `/exit` | 세션 종료 | 작업이 끝났을 때 |

여기까지가 시작을 위한 전부입니다! 익숙해지면 추가 명령어들도 살펴볼 수 있습니다.

> 📚 **공식 문서**: 전체 명령/플래그 목록은 [CLI 명령 참조](https://docs.github.com/copilot/reference/cli-command-reference)를 확인하세요.

<details>
<summary>📚 <strong>추가 명령어</strong> (클릭하여 펼치기)</summary>

> 💡 위의 필수 명령어만으로도 일상 작업의 많은 부분을 처리할 수 있습니다. 이 섹션은 더 많은 기능을 탐색할 준비가 되었을 때 참고하세요.

### 에이전트 환경

| 명령어 | 하는 일 |
|--------|--------|
| `/init` | 저장소에 Copilot 지침 초기화 |
| `/agent` | 사용 가능한 에이전트 탐색 및 선택 |
| `/skills` | 향상된 기능을 위한 스킬 관리 |
| `/mcp` | MCP 서버 설정 관리 |

> 💡 스킬은 [챕터 05](../05-skills/README.md)에서 자세히 다룹니다. MCP 서버는 [챕터 06](../06-mcp-servers/README.md)에서 다룹니다.

### 모델과 서브에이전트

| 명령어 | 하는 일 |
|--------|--------|
| `/model` | AI 모델 표시/전환 |
| `/delegate` | GitHub의 Copilot coding agent(클라우드 에이전트)에게 작업 위임 |
| `/fleet` | 복잡한 작업을 병렬 하위 작업으로 분할 |
| `/tasks` | 백그라운드 서브에이전트 및 분리된 셸 세션 보기 |

### 코드

| 명령어 | 하는 일 |
|--------|--------|
| `/diff` | 현재 디렉터리에서 변경사항 확인 |
| `/pr` | 현재 브랜치의 PR 조작 |
| `/review` | 코드 리뷰 에이전트 실행 |
| `/research` | GitHub/웹 기반 심층 조사 실행 |
| `/terminal-setup` | 멀티라인 입력 지원 활성화(shift+enter, ctrl+enter) |

### 권한

| 명령어 | 하는 일 |
|--------|--------|
| `/allow-all` | 이 세션의 모든 권한 요청 자동 승인 |
| `/add-dir <디렉터리>` | 허용 목록에 디렉터리 추가 |
| `/list-dirs` | 허용된 디렉터리 목록 표시 |
| `/cwd`, `/cd [디렉터리]` | 현재 작업 디렉터리 보기/변경 |

> ⚠️ **주의**: `/allow-all`은 확인 프롬프트를 건너뜁니다. 신뢰할 수 있는 프로젝트에는 유용하지만, 신뢰할 수 없는 코드에는 주의하세요.

### 세션

| 명령어 | 하는 일 |
|--------|--------|
| `/resume` | 다른 세션으로 전환(선택적으로 세션 ID 지정) |
| `/rename` | 현재 세션 이름 변경 |
| `/context` | 컨텍스트 윈도우 토큰 사용량 및 시각화 표시 |
| `/usage` | 세션 사용량 메트릭/통계 표시 |
| `/session` | 세션 정보 및 워크스페이스 요약 표시 |
| `/compact` | 대화를 요약하여 컨텍스트 사용량 줄이기 |
| `/share` | 세션을 마크다운 파일 또는 GitHub gist로 내보내기 |

### 도움말 및 피드백

| 명령어 | 하는 일 |
|--------|--------|
| `/help` | 사용 가능한 명령어 표시 |
| `/changelog` | CLI 버전 변경 로그 표시 |
| `/feedback` | GitHub에 피드백 제출 |
| `/theme` | 터미널 테마 보기/설정 |

### 빠른 셸 명령어

AI 없이 셸 명령을 직접 실행하려면 앞에 `!`를 붙이세요:

```bash
copilot

> !git status
# git status를 직접 실행(= AI 우회)

> !python -m pytest tests/
# pytest 실행
```

### 모델 전환

Copilot CLI는 OpenAI, Anthropic, Google 등 다양한 제공자의 여러 AI 모델을 지원합니다. 사용 가능한 모델은 구독 수준과 지역에 따라 달라집니다. `/model`로 옵션을 확인하고 전환하세요:

```bash
copilot
> /model

# 사용 가능한 모델을 표시하고 선택하게 합니다. Sonnet 4.5를 선택하세요.
```

> 💡 **팁**: 어떤 모델은 다른 모델보다 “프리미엄 요청”을 더 많이 소모합니다. **1x**로 표시된 모델(예: Claude Sonnet 4.5)은 유능하면서 효율적이라 기본값으로 좋습니다. 더 높은 배율의 모델은 프리미엄 요청 할당량을 더 빨리 소모하니, 정말 필요할 때만 사용하세요.

</details>

---

# 실습

<img src="../images/practice.png" alt="코드가 표시된 모니터, 스탠드 조명, 커피, 헤드폰이 있는 아늑한 책상 세팅(실습 준비)" width="800"/>

배운 내용을 직접 적용해 볼 시간입니다.

---

## ▶️ 직접 해보기

### 대화형 탐색

Copilot을 시작하고 후속 프롬프트를 사용해 책 앱을 반복적으로 개선해 보세요:

```bash
copilot

> @samples/book-app-project/book_app.py 를 리뷰해 줘 - 뭐가 개선될까?

> if/elif 체인을 더 유지보수하기 좋은 구조로 리팩터링해 줘

> 모든 핸들러 함수에 타입 힌트를 추가해 줘

> /exit
```

### 기능 계획 세우기

`/plan`을 사용해 코드를 작성하기 전에 구현을 단계별로 설계하게 할 수 있습니다:

```bash
copilot

> /plan 제목 또는 저자 기준으로 책을 찾을 수 있는 검색 기능을 책 앱에 추가해 줘

# 계획을 검토
# 승인 또는 수정
# 단계별로 구현되는 과정을 확인
```

### Programmatic 모드로 자동화하기

`-p` 플래그를 쓰면 대화형 모드에 들어가지 않고 터미널에서 Copilot CLI를 바로 실행할 수 있습니다. 아래 스크립트를 저장소 루트에서 터미널(= Copilot 내부가 아님)에 복사해 붙여넣으면, 책 앱의 모든 Python 파일을 리뷰할 수 있습니다.

```bash
# 책 앱의 모든 Python 파일 리뷰
for file in samples/book-app-project/*.py; do
  echo "Reviewing $file..."
  copilot --allow-all -p "@$file 에 대한 빠른 코드 품질 리뷰 - 치명적인 이슈만"
done
```

**PowerShell (Windows):**

```powershell
# 책 앱의 모든 Python 파일 리뷰
Get-ChildItem samples/book-app-project/*.py | ForEach-Object {
  $relativePath = "samples/book-app-project/$($_.Name)";
  Write-Host "Reviewing $relativePath...";
  copilot --allow-all -p "@$relativePath 에 대한 빠른 코드 품질 리뷰 - 치명적인 이슈만" 
}
```

---

데모를 완료한 뒤에는 다음 변형도 시도해 보세요:

1. **Interactive 챌린지**: `copilot`을 시작하고 책 앱을 탐색해 보세요. `@samples/book-app-project/books.py`에 대해 질문하고, 개선 요청을 연속으로 3번 해 보세요.

2. **Plan 모드 챌린지**: `/plan 책 앱에 평점 및 리뷰 기능을 추가해 줘`를 실행하세요. 계획을 꼼꼼히 읽어보세요. 말이 되나요?

3. **Programmatic 챌린지**: `copilot --allow-all -p "@samples/book-app-project/book_app.py 의 모든 함수를 나열하고 각각 무엇을 하는지 설명해 줘"`를 실행하세요. 한 번에 잘 되었나요?

---

## 📝 과제

### 메인 과제: 책 앱 유틸리티 개선

실습 예시는 주로 `book_app.py`를 리뷰하고 리팩터링하는 데 초점을 맞췄습니다. 이제 같은 스킬을 다른 파일 `utils.py`에 적용해 봅시다:

1. 대화형 세션 시작: `copilot`
2. Copilot CLI에게 파일 요약을 요청: `@samples/book-app-project/utils.py 이 파일의 각 함수가 무엇을 하는지 알려줘`
3. 입력 검증 추가 요청: "`get_user_choice()`가 빈 입력과 숫자가 아닌 입력도 처리하도록 검증을 추가해 줘"
4. 에러 처리 개선 요청: "`get_book_details()`가 제목으로 빈 문자열을 받으면 어떻게 될까? 가드(검사)를 추가해 줘"
5. docstring 요청: "`get_book_details()`에 매개변수 설명과 반환값이 포함된 포괄적인 docstring을 추가해 줘"
6. 맥락이 어떻게 이어지는지 관찰하세요. 각 개선 요청은 이전 요청을 바탕으로 합니다.
7. `/exit`로 종료

**성공 기준**: 입력 검증, 에러 처리, docstring이 추가된 개선된 `utils.py`를 얻는 것입니다. 이 모든 과정이 여러 턴의 대화로 이루어져야 합니다.

<details>
<summary>💡 힌트(클릭하여 펼치기)</summary>

**시도해 볼 샘플 프롬프트:**
```bash
> @samples/book-app-project/utils.py 이 파일의 각 함수가 무엇을 하는지 알려줘
> get_user_choice()가 빈 입력과 숫자가 아닌 입력도 처리하도록 검증을 추가해 줘
> get_book_details()가 제목으로 빈 문자열을 받으면 어떻게 될까? 가드(검사)를 추가해 줘
> get_book_details()에 매개변수 설명과 반환값이 포함된 포괄적인 docstring을 추가해 줘
```

**자주 발생하는 문제:**
- Copilot CLI가 추가 질문을 하면 자연스럽게 답하면 됩니다
- 맥락이 계속 이어지므로, 각 프롬프트는 이전 응답을 기반으로 합니다
- 새로 시작하고 싶으면 `/clear`를 사용하세요

</details>

### 보너스 과제: 모드 비교

예시에서는 검색 기능에 `/plan`을 사용했고, 배치 리뷰에는 `-p`를 사용했습니다. 이제 하나의 새로운 작업에 대해 세 가지 모드를 모두 써 보세요: `BookCollection` 클래스에 `list_by_year()` 메서드를 추가하기

1. **Interactive**: `copilot` → 단계별로 메서드를 설계하고 구현해 달라고 요청
2. **Plan**: `/plan BookCollection에 list_by_year(start, end) 메서드를 추가해서 출판 연도 범위로 책을 필터링해 줘`
3. **Programmatic**: `copilot --allow-all -p "@samples/book-app-project/books.py 에 list_by_year(start, end) 메서드를 추가해서 start~end(포함) 사이에 출판된 책을 반환하게 해 줘"`

**회고**: 어떤 모드가 가장 자연스러웠나요? 각각 언제 사용하겠나요?

---

<details>
<summary>🔧 <strong>자주 하는 실수 & 문제 해결</strong> (클릭하여 펼치기)</summary>

### 자주 하는 실수

| 실수 | 어떤 일이 발생하나 | 해결 |
|------|------------------|------|
| `exit`를 `/exit` 대신 입력 | Copilot CLI는 `exit`를 명령이 아닌 프롬프트로 처리 | 슬래시 명령은 항상 `/`로 시작합니다 |
| 여러 턴 대화를 `-p`로 시도 | `-p` 호출은 매번 독립적이라 이전 맥락이 없음 | 맥락이 쌓이는 대화는 Interactive 모드(`copilot`) 사용 |
| `$`나 `!`가 포함된 프롬프트에 따옴표를 빼먹음 | 셸이 특수문자를 Copilot CLI보다 먼저 해석 | 프롬프트를 따옴표로 감싸기: `copilot -p "$HOME 이 뭐야?"` |

### 문제 해결

**"Model not available"** - 구독에 따라 일부 모델은 제공되지 않을 수 있습니다. `/model`로 사용 가능 모델을 확인하세요.

**"Context too long"** - 대화가 컨텍스트 윈도우를 초과했습니다. `/clear`로 초기화하거나 새 세션을 시작하세요.

**"Rate limit exceeded"** - 몇 분 기다린 뒤 다시 시도하세요. 배치 작업은 프로그램형 모드를 사용하되, 지연을 넣는 것을 고려하세요.

</details>

---

# 요약

## 🔑 핵심 정리

1. **Interactive 모드**는 탐색과 반복에 적합하며 맥락이 이어집니다. 지금까지 말한 내용을 기억하는 상대와 대화하는 것과 같습니다.
2. **Plan 모드**는 보통 더 복잡한 작업에 사용합니다. 구현 전에 계획을 검토하세요.
3. **Programmatic 모드**는 자동화에 적합합니다. 대화가 필요 없습니다.
4. **네 가지 필수 명령** (`/help`, `/clear`, `/plan`, `/exit`)만으로도 대부분의 일상 사용을 커버합니다.

> 📋 **빠른 참조**: 전체 명령어와 단축키는 [GitHub Copilot CLI 명령 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 확인하세요.

---

## ➡️ 다음은?

이제 세 가지 모드를 이해했으니, Copilot CLI에 코드 맥락을 제공하는 방법을 배워봅시다.

**[챕터 02: 컨텍스트와 대화](../02-context-conversations/README.md)** 에서는 다음을 배웁니다:

- 파일/디렉터리를 참조하는 `@` 문법
- `--resume`, `--continue`로 세션 관리
- 컨텍스트 관리가 Copilot CLI를 얼마나 강력하게 만드는지

---

**[← 코스 홈으로](../README.md)** | **[챕터 02로 계속 →](../02-context-conversations/README.md)"