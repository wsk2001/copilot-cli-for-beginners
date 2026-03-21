![Chapter 06: MCP Servers](images/chapter-header.png)

> **만약 Copilot이 터미널에서 GitHub 이슈를 읽고, 데이터베이스를 확인하고, PR을 생성할 수 있다면 어떨까요?**

현재 Copilot은 사용자가 직접 제공하는 데이터, 즉 `@`로 참조하는 파일, 대화 기록, 그리고 자체 학습 데이터만을 활용할 수 있습니다. 하지만 Copilot이 스스로 GitHub 저장소를 확인하고, 프로젝트 파일을 탐색하거나, 라이브러리의 최신 문서를 찾아볼 수 있다면 어떨까요?

MCP(Model Context Protocol)는 바로 그런 역할을 합니다. MCP를 통해 Copilot은 외부 서비스에 연결하여 실시간 실제 데이터에 접근할 수 있습니다. Copilot이 연결하는 각 서비스를 "MCP 서버"라고 합니다. 이 장에서는 이러한 연결을 몇 가지 설정하고 이를 통해 Copilot의 활용도가 얼마나 크게 향상되는지 살펴보겠습니다.

> 💡 **MCP를 이미 사용해 보셨나요?** [빠른 시작 가이드](#-github-mcp-서버-내장-기능-시작하기)로 이동하여 작동 여부를 확인하고 서버 구성을 시작하세요.

## 🎯 학습 목표

이 장을 마치면 다음을 수행할 수 있게 됩니다.

- MCP가 무엇이며 왜 중요한지 이해합니다.
- `/mcp` 명령어를 사용하여 MCP 서버를 관리합니다.
- GitHub, 파일 시스템 및 문서에 대한 MCP 서버를 구성합니다.
- 책 앱 프로젝트에서 MCP 기반 워크플로를 사용합니다.
- 사용자 지정 MCP 서버를 구축해야 하는 시점과 방법을 압니다(선택 사항).

> ⏱️ **예상 소요 시간**: 약 50분 (읽기 15분 + 실습 35분)

---

## 🧩 실제 사례: 브라우저 확장 프로그램

<img src="images/browser-extensions-analogy.png" alt="MCP Servers are like Browser Extensions" width="800"/>

MCP 서버를 브라우저 확장 프로그램과 비슷하게 생각해 보세요. 브라우저 자체는 웹 페이지를 표시할 수 있지만, 확장 프로그램은 브라우저를 추가 서비스에 연결해 줍니다.

| 브라우저 확장 프로그램 | 연결 대상 | MCP와 유사한 기능 |
|----------------------|-----------|-----------------|
| 비밀번호 관리자 | 비밀번호 보관소 | **GitHub MCP** → 저장소, 이슈, PR |
| Grammarly | 글쓰기 분석 서비스 | **Context7 MCP** → 라이브러리 문서 관리 |
| 파일 관리자 | 클라우드 스토리지 | **파일 시스템 MCP** → 로컬 프로젝트 파일 |

확장 프로그램이 없어도 브라우저는 여전히 유용하지만, 확장 프로그램을 사용하면 강력한 도구로 거듭납니다. MCP 서버는 Copilot에도 똑같은 역할을 합니다. MCP 서버는 Copilot을 실제 데이터 소스에 연결하여 GitHub 이슈를 읽고, 파일 시스템을 탐색하고, 최신 문서를 가져오는 등 다양한 작업을 수행할 수 있도록 지원합니다.

***MCP 서버는 Copilot을 외부 세계(GitHub, 저장소, 문서 등)에 연결합니다.***

> 💡 **핵심 정보**: MCP가 없으면 Copilot은 `@`로 명시적으로 공유한 파일만 볼 수 있습니다. MCP를 사용하면 Copilot은 프로젝트를 능동적으로 탐색하고, GitHub 저장소를 확인하고, 문서를 검색하는 등의 모든 작업을 자동으로 수행할 수 있습니다.

---

<img src="images/quick-start-mcp.png" alt="Power cable connecting with bright electrical spark surrounded by floating tech icons representing MCP server connections" width="800"/>

# 빠른 시작: 30초 만에 MCP 시작하기

## GitHub MCP 서버 내장 기능 시작하기

설정을 하기 전에 MCP가 어떻게 작동하는지 바로 살펴보겠습니다.

GitHub MCP 서버는 기본적으로 포함되어 있습니다. 다음을 시도해 보세요.

```bash
copilot
> 이 저장소의 최근 커밋 목록을 표시합니다.
```

Copilot이 실제 커밋 데이터를 반환한다면, MCP가 제대로 작동하는 것을 보신 겁니다. GitHub MCP 서버가 여러분을 대신하여 GitHub에 접속한 것이죠. 하지만 GitHub는 단 하나의 서버일 뿐입니다. 이 장에서는 파일 시스템 접근, 최신 문서 업데이트 등 Copilot이 더 많은 기능을 수행할 수 있도록 서버를 추가하는 방법을 보여줍니다.

---

## `/mcp show` 명령어

구성된 MCP 서버와 활성화 여부를 확인하려면 `/mcp show` 명령어를 사용하십시오.

```bash
copilot

> /mcp show

MCP Servers:
✓ github (enabled) - GitHub integration
✓ filesystem (enabled) - File system access
```

> 💡 **GitHub 서버만 보이시나요?** 정상입니다! 아직 MCP 서버를 추가하지 않았다면 GitHub만 표시됩니다. 다음 섹션에서 더 많은 서버를 추가할 수 있습니다.

> 📚 **모든 `/mcp` 명령어를 보고 싶으신가요?** 서버를 추가, 편집, 활성화 및 삭제하는 추가 명령어가 있습니다. 이 장의 끝에 있는 [전체 명령어 참조](#-추가-mcp-명령어)를 확인하세요.

<details>
<summary>🎬 실제로 작동하는 모습을 확인해 보세요!</summary>

![MCP Status Demo](images/mcp-status-demo.gif)

*데모 결과는 다를 수 있습니다. 사용하시는 모델, 도구 및 응답은 여기에 표시된 내용과 다를 수 있습니다.*

</details>

---

## MCP 도입 시 어떤 변화가 있을까요?

MCP가 실제로 가져오는 차이점은 다음과 같습니다.

**MCP 미사용 시:**
```bash
> GitHub 이슈 #42에는 어떤 내용이 있나요?

"저는 GitHub에 접속할 수 없습니다. 문제 내용을 복사해서 붙여넣어 주셔야 합니다."
```

**MCP 사용 시:**
```bash
> 이 저장소의 GitHub 이슈 #42에는 어떤 내용이 있나요?

문제 #42: 특수 문자가 포함된 비밀번호로 로그인 실패
상태: 열림
레이블: 버그, 우선순위 높음
설명: 사용자들이 특수 문자가 포함된 비밀번호로 로그인할 경우 오류가 발생한다고 보고했습니다...
```

MCP는 사용자의 실제 개발 환경을 Copilot에 알려줍니다.

> 📚 **공식 문서**: GitHub Copilot과 MCP의 연동 방식에 대한 자세한 내용은 [MCP 소개](https://docs.github.com/copilot/concepts/context/mcp)를 참조하세요.

---

# MCP 서버 구성하기

<img src="images/configuring-mcp-servers.png" alt="Hands adjusting knobs and sliders on a professional audio mixing board representing MCP server configuration" width="800"/>

MCP가 실제로 동작하는 모습을 보셨으니, 이제 추가 서버를 설정해 봅시다. 이 섹션에서는 구성 파일 형식과 새 서버를 추가하는 방법을 다룹니다.

---

## MCP 구성 파일

MCP 서버는 `~/.copilot/mcp-config.json`(사용자 수준, 모든 프로젝트에 적용) 또는 `.vscode/mcp.json`(프로젝트 수준, 현재 작업 공간에만 적용)에서 구성됩니다.

```json
{
  "mcpServers": {
    "server-name": {
      "type": "local",
      "command": "npx",
      "args": ["@package/server-name"],
      "tools": ["*"]
    }
  }
}
```

*대부분의 MCP 서버는 npm 패키지로 배포되며 `npx` 명령어로 실행됩니다.*

<details>
<summary>💡 <strong>JSON이 처음이신가요?</strong> 각 필드의 의미를 알아보려면 클릭하세요</summary>

| 필드 | 의미 |
|------|------|
| `"mcpServers"` | 모든 MCP 서버 구성을 담는 컨테이너 |
| `"server-name"` | 사용자가 정하는 이름 (예: "github", "filesystem") |
| `"type": "local"` | 서버가 사용자의 로컬 머신에서 실행됨 |
| `"command": "npx"` | 실행할 프로그램 (npx는 npm 패키지를 실행함) |
| `"args": [...]` | 명령어에 전달할 인수 |
| `"tools": ["*"]` | 이 서버의 모든 도구 허용 |

**JSON 규칙 중요 사항:**
- 문자열에는 큰따옴표 `"` 사용 (작은따옴표 사용 금지)
- 마지막 항목 뒤에 쉼표 없음
- 파일은 유효한 JSON이어야 합니다 (확실하지 않은 경우 [JSON 유효성 검사기](https://jsonlint.com/) 사용)

</details>

---

## MCP 서버 추가하기

GitHub MCP 서버는 기본 내장되어 있어 별도 설정이 필요 없습니다. 아래는 추가로 설치할 수 있는 서버들입니다. **관심 있는 것을 선택하거나, 순서대로 진행하세요.**

| 원하는 기능 | 이동 |
|-------------|------|
| Copilot이 프로젝트 파일을 탐색하도록 허용 | [파일 시스템 서버](#파일-시스템-서버) |
| 최신 라이브러리 문서 가져오기 | [Context7 서버](#context7-서버-문서) |
| 선택 사항 추가 기능 탐색 (사용자 지정 서버, web_fetch) | [기본 이상의 기능](#기본-이상의-기능) |

<details>
<summary><strong>파일 시스템 서버</strong> - Copilot이 프로젝트 파일을 탐색하도록 허용</summary>
<a id="파일-시스템-서버"></a>

### 파일 시스템 서버

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    }
  }
}
```

> 💡 **`.` 경로**: `.`는 "현재 디렉터리"를 의미합니다. Copilot은 실행된 위치를 기준으로 파일에 접근할 수 있습니다. Codespace에서는 워크스페이스 루트가 됩니다. 원하시면 `/workspaces/copilot-cli-for-beginners`와 같은 절대 경로를 사용할 수도 있습니다.

이 내용을 `~/.copilot/mcp-config.json`에 추가하고 Copilot을 재시작하세요.

</details>

<details>
<summary><strong>Context7 서버</strong> - 최신 라이브러리 문서 가져오기</summary>
<a id="context7-서버-문서"></a>

### Context7 서버 (문서)

Context7은 널리 사용되는 프레임워크 및 라이브러리의 최신 문서에 Copilot이 접근할 수 있게 해줍니다. 오래된 학습 데이터에 의존하는 대신, 실제 최신 문서를 직접 가져옵니다.

```json
{
  "mcpServers": {
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

- ✅ **API 키 불필요**
- ✅ **계정 불필요**
- ✅ **코드는 로컬에 유지됨**

이 내용을 `~/.copilot/mcp-config.json`에 추가하고 Copilot을 재시작하세요.

</details>

<details>
<summary><strong>기본 이상의 기능</strong> - 사용자 지정 서버 및 웹 접근 (선택 사항)</summary>
<a id="기본-이상의-기능"></a>

위의 핵심 서버에 익숙해지면 사용할 수 있는 선택적 추가 기능입니다.

### Microsoft Learn MCP 서버

지금까지 살펴본 MCP 서버(파일 시스템, Context7)는 모두 로컬 머신에서 실행됩니다. 하지만 MCP 서버는 원격으로도 실행될 수 있습니다. 즉, Copilot CLI에 URL만 알려주면 나머지는 알아서 처리됩니다. `npx`나 `python`도 필요 없고, 로컬 프로세스나 설치해야 할 의존성도 없습니다.

[Microsoft Learn MCP 서버](https://github.com/microsoftdocs/mcp)가 좋은 예입니다. Copilot CLI가 공식 Microsoft 문서(Azure, Microsoft Foundry 및 기타 AI 주제, .NET, Microsoft 365 등)에 직접 접근하여 문서를 검색하고, 전체 페이지를 가져오고, 모델의 학습 데이터에 의존하는 대신 공식 코드 샘플을 찾을 수 있습니다.

- ✅ **API 키 불필요**
- ✅ **계정 불필요**
- ✅ **로컬 설치 불필요**

**`/plugin install`을 사용한 빠른 설치:**

JSON 구성 파일을 직접 편집하는 대신, 단 한 줄의 명령어로 설치할 수 있습니다.

```bash
copilot

> /plugin install microsoftdocs/mcp
```

이 명령어는 서버와 관련 에이전트 스킬을 자동으로 추가합니다. 설치되는 스킬은 다음과 같습니다.

- **microsoft-docs**: 개념, 튜토리얼, 사실 조회
- **microsoft-code-reference**: API 조회, 코드 샘플 및 문제 해결
- **microsoft-skill-creator**: Microsoft 기술에 관한 사용자 지정 스킬을 생성하는 메타 스킬

**사용 예:**
```bash
copilot

> Python 앱을 Azure App Service에 배포하는 권장 방법은 무엇인가요? Microsoft Learn을 검색해 주세요.
```

📚 자세히 알아보기: [Microsoft Learn MCP 서버 개요](https://learn.microsoft.com/training/support/mcp-get-started)

### `web_fetch`를 사용한 웹 접근

Copilot CLI에는 모든 URL의 콘텐츠를 가져올 수 있는 내장 `web_fetch` 도구가 있습니다. 터미널을 벗어나지 않고도 README, API 문서, 릴리스 노트 등을 불러올 때 유용합니다. MCP 서버가 따로 필요하지 않습니다.

`~/.copilot/config.json`(Copilot 일반 설정)을 통해 접근 가능한 URL을 제어할 수 있습니다. 이 파일은 `~/.copilot/mcp-config.json`(MCP 서버 정의)과는 별개입니다.

```json
{
  "permissions": {
    "allowedUrls": [
      "https://api.github.com/**",
      "https://docs.github.com/**",
      "https://*.npmjs.org/**"
    ],
    "blockedUrls": [
      "http://**"
    ]
  }
}
```

**사용 예:**
```bash
copilot

> https://github.com/facebook/react 의 README를 가져와서 요약해 주세요.
```

### 사용자 지정 MCP 서버 구축하기

자신만의 API, 데이터베이스, 또는 내부 도구에 Copilot을 연결하고 싶으신가요? Python으로 사용자 지정 MCP 서버를 만들 수 있습니다. 미리 만들어진 서버(GitHub, 파일 시스템, Context7)가 대부분의 사용 사례를 커버하므로 이 작업은 완전히 선택 사항입니다.

📖 책 앱을 예제로 사용하는 완전한 안내는 [사용자 지정 MCP 서버 가이드](mcp-custom-server.md)를 참조하세요.

📚 더 많은 배경 정보는 [MCP for Beginners 과정](https://github.com/microsoft/mcp-for-beginners)을 참조하세요.

</details>

<a id="완전한-구성-파일"></a>

### 완전한 구성 파일

파일 시스템 및 Context7 서버가 포함된 전체 `mcp-config.json`입니다.

> 💡 **참고:** GitHub MCP는 내장되어 있습니다. 구성 파일에 추가할 필요가 없습니다.

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    },
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

전역 접근을 위해 `~/.copilot/mcp-config.json`으로 저장하거나, 프로젝트별 구성을 위해 `.vscode/mcp.json`으로 저장하세요.

---

# MCP 서버 사용하기

이제 MCP 서버를 구성했으니, 실제로 무엇을 할 수 있는지 살펴봅시다.

<img src="images/using-mcp-servers.png" alt="Using MCP Servers - Hub-and-spoke diagram showing a Developer CLI connected to GitHub, Filesystem, Context7, and Custom/Web Fetch servers" width="800" />

---

## 서버 사용 예시

**관심 있는 서버를 선택하거나, 순서대로 살펴보세요.**

| 시도하고 싶은 것 | 이동 |
|----------------|------|
| GitHub 저장소, 이슈, PR | [GitHub 서버](#github-서버-내장) |
| 프로젝트 파일 탐색 | [파일 시스템 서버 사용법](#파일-시스템-서버-사용법) |
| 라이브러리 문서 조회 | [Context7 서버 사용법](#context7-서버-사용법) |
| 사용자 지정 서버, Microsoft Learn MCP 및 web_fetch 사용 | [기본 이상의 기능 사용법](#기본-이상의-기능-사용법) |

<details>
<summary><strong>GitHub 서버 (내장)</strong> - 저장소, 이슈, PR 등에 접근</summary>
<a id="github-서버-내장"></a>

### GitHub 서버 (내장)

GitHub MCP 서버는 **내장**되어 있습니다. Copilot에 로그인했다면(초기 설정 시 완료), 이미 작동하고 있습니다. 별도 구성이 필요 없습니다!

> 💡 **작동하지 않나요?** `/login`을 실행하여 GitHub에 다시 인증하세요.

<details>
<summary><strong>개발 컨테이너에서의 인증</strong></summary>

- **GitHub Codespaces** (권장): 인증이 자동으로 이루어집니다. `gh` CLI가 Codespace 토큰을 상속받습니다. 별도 조치가 필요 없습니다.
- **로컬 개발 컨테이너 (Docker)**: 컨테이너 시작 후 `gh auth login`을 실행한 다음 Copilot을 재시작하세요.

**인증 문제 해결:**
```bash
# 인증 상태 확인
gh auth status

# 인증되지 않은 경우 로그인
gh auth login

# GitHub MCP 연결 확인
copilot
> /mcp show
```

</details>

| 기능 | 예시 |
|------|------|
| **저장소 정보** | 커밋, 브랜치, 기여자 보기 |
| **이슈** | 이슈 목록 조회, 생성, 검색 및 댓글 달기 |
| **풀 리퀘스트** | PR 보기, 차이 확인, PR 생성, 상태 확인 |
| **코드 검색** | 저장소 전체에서 코드 검색 |
| **Actions** | 워크플로 실행 및 상태 쿼리 |

```bash
copilot

# 이 저장소의 최근 활동 보기
> 이 저장소의 마지막 커밋 5개를 나열해 주세요.

최근 커밋:
1. abc1234 - 5장 스킬 예제 업데이트 (2일 전)
2. def5678 - 책 앱 테스트 픽스처 추가 (3일 전)
3. ghi9012 - 3장 README 오타 수정 (4일 전)
...

# 저장소 구조 탐색
> 이 저장소에 어떤 브랜치가 있나요?

브랜치:
- main (기본)
- chapter6 (현재)

# 저장소 전체에서 코드 패턴 검색
> pytest를 임포트하는 파일을 이 저장소에서 검색해 주세요.

1개 파일 발견:
- samples/book-app-project/tests/test_books.py
```

> 💡 **자신의 포크에서 작업하시나요?** 이 강의 저장소를 포크했다면 이슈 생성, 풀 리퀘스트 생성 등 쓰기 작업도 시도해 볼 수 있습니다. 아래 연습 문제에서 실습해 보겠습니다.

> ⚠️ **결과가 표시되지 않나요?** GitHub MCP는 로컬 파일이 아닌 저장소의 원격(github.com)에서 작동합니다. 저장소에 원격이 설정되어 있는지 확인하세요: `git remote -v`를 실행하여 확인하세요.

</details>

<details>
<summary><strong>파일 시스템 서버</strong> - 프로젝트 파일 탐색 및 분석</summary>
<a id="파일-시스템-서버-사용법"></a>

### 파일 시스템 서버

구성이 완료되면 파일 시스템 MCP는 Copilot이 자동으로 사용할 수 있는 도구를 제공합니다.

```bash
copilot

> book-app-project 디렉터리에 Python 파일이 몇 개 있나요?

samples/book-app-project/에서 Python 파일 3개 발견:
- book_app.py
- books.py
- utils.py

> data.json 파일의 전체 크기는 얼마인가요?

samples/book-app-project/data.json: 2.4 KB

> 책 앱에서 타입 힌트가 없는 모든 함수를 찾아주세요.

타입 힌트가 없는 함수 2개 발견:
- samples/book-app-project/utils.py:10 - get_user_choice()
- samples/book-app-project/utils.py:14 - get_book_details()
```

</details>

<details>
<summary><strong>Context7 서버</strong> - 라이브러리 문서 조회</summary>
<a id="context7-서버-사용법"></a>

### Context7 서버

```bash
copilot

> pytest 픽스처 사용의 모범 사례는 무엇인가요?

pytest 문서에서:

픽스처 - 테스트의 고정 기준을 제공하기 위해 픽스처를 사용하세요:

    import pytest

    @pytest.fixture
    def sample_books():
        return [
            {"title": "1984", "author": "George Orwell", "year": 1949},
            {"title": "Dune", "author": "Frank Herbert", "year": 1965},
        ]

    def test_find_by_author(sample_books):
        # 픽스처는 인수로 자동 전달됩니다
        results = [b for b in sample_books if "Orwell" in b["author"]]
        assert len(results) == 1

모범 사례:
- setup/teardown 메서드 대신 픽스처 사용
- 임시 파일에는 tmp_path 픽스처 사용
- 환경 변경에는 monkeypatch 사용
- 픽스처 범위를 적절히 설정 (function, class, module, session)

> 이것을 책 앱의 테스트 파일에 어떻게 적용할 수 있나요?

# Copilot은 이제 공식 pytest 패턴을 알고 있으며
# samples/book-app-project/tests/test_books.py에 적용할 수 있습니다.
```

</details>

<details>
<summary><strong>기본 이상의 기능</strong> - 사용자 지정 서버 및 web_fetch 사용법</summary>
<a id="기본-이상의-기능-사용법"></a>

### 기본 이상의 기능

**사용자 지정 MCP 서버**: [사용자 지정 MCP 서버 가이드](mcp-custom-server.md)에서 book-lookup 서버를 구축했다면, 책 컬렉션을 직접 쿼리할 수 있습니다.

```bash
copilot

> book lookup 서버를 사용하여 "1984"에 대한 정보를 조회해 주세요. George Orwell의 책을 검색해 주세요.
```

**Microsoft Learn MCP**: [Microsoft Learn MCP 서버](#microsoft-learn-mcp-서버)를 설치했다면, 공식 Microsoft 문서를 직접 조회할 수 있습니다.

```bash
copilot

> Azure Function에 대해 관리형 ID를 구성하는 방법은 무엇인가요? Microsoft Learn을 검색해 주세요.
```

**Web Fetch**: 내장된 `web_fetch` 도구를 사용하여 모든 URL의 콘텐츠를 가져올 수 있습니다.

```bash
copilot

> https://github.com/facebook/react 의 README를 가져와서 요약해 주세요.
```

</details>

---

## 다중 서버 워크플로

이 워크플로들은 개발자들이 "이것 없이는 다시는 일하고 싶지 않다"고 말하는 이유를 보여줍니다. 각 예시는 단일 세션에서 여러 MCP 서버를 결합합니다.

<img src="images/issue-to-pr-workflow.png" alt="Issue to PR Workflow using MCP - Shows the complete flow from getting a GitHub issue through creating a pull request" width="800"/>

*완전한 MCP 워크플로: GitHub MCP가 저장소 데이터를 가져오고, 파일 시스템 MCP가 코드를 찾고, Context7 MCP가 모범 사례를 제공하며, Copilot이 분석을 처리합니다.*

아래의 각 예시는 독립적입니다. **관심 있는 것을 선택하거나, 모두 읽어보세요.**

| 보고 싶은 것 | 이동 |
|-------------|------|
| 여러 서버가 함께 작동하는 모습 | [다중 서버 탐색](#다중-서버-탐색) |
| 이슈에서 PR까지 한 세션에서 처리 | [이슈-to-PR 워크플로](#이슈-to-pr-워크플로) |
| 빠른 프로젝트 상태 확인 | [상태 대시보드](#상태-대시보드) |

<details>
<summary><strong>다중 서버 탐색</strong> - 파일 시스템, GitHub, Context7을 한 세션에서 결합</summary>
<a id="다중-서버-탐색"></a>

#### 여러 MCP 서버로 책 앱 탐색하기

```bash
copilot

# 1단계: 파일 시스템 MCP로 책 앱 탐색
> samples/book-app-project/의 모든 Python 파일을 나열하고 각 파일이
> 무엇을 하는지 요약해 주세요.

Python 파일 3개 발견:
- book_app.py: 명령어 라우팅이 있는 CLI 진입점 (list, add, remove, find)
- books.py: JSON을 통한 데이터 영속성을 갖춘 BookCollection 클래스
- utils.py: 사용자 입력 및 표시를 위한 헬퍼 함수

# 2단계: GitHub MCP로 최근 변경 사항 확인
> samples/book-app-project/의 파일에 영향을 준 마지막 커밋 3개는 무엇인가요?

책 앱에 영향을 준 최근 커밋:
1. abc1234 - BookCollection에 대한 테스트 픽스처 추가 (2일 전)
2. def5678 - find_by_author 메서드 추가 (5일 전)
3. ghi9012 - 초기 책 앱 설정 (1주일 전)

# 3단계: Context7 MCP로 모범 사례 조회
> JSON 데이터 영속성에 대한 Python 모범 사례는 무엇인가요?

Python 문서에서:
- 파일 I/O에는 컨텍스트 관리자(with 문) 사용
- 손상된 파일을 위한 JSONDecodeError 처리
- 구조화된 데이터에는 데이터클래스 사용
- 데이터 손상 방지를 위한 원자적 쓰기 고려

# 4단계: 권장 사항 종합
> 책 앱 코드와 이 모범 사례를 바탕으로 어떤 개선을 제안하시겠습니까?

제안:
1. 빈 문자열 및 잘못된 연도에 대한 add_book()의 입력 유효성 검사 추가
2. 데이터 손상 방지를 위해 save_books()에 원자적 쓰기 고려
3. utils.py 함수에 타입 힌트 추가 (get_user_choice, get_book_details)
```

<details>
<summary>🎬 MCP 워크플로 실제 작동 모습 확인!</summary>

![MCP Workflow Demo](images/mcp-workflow-demo.gif)

*데모 결과는 다를 수 있습니다. 사용하시는 모델, 도구 및 응답은 여기에 표시된 내용과 다를 수 있습니다.*

</details>

**결과**: 코드 탐색 → 히스토리 검토 → 모범 사례 조회 → 개선 계획. **하나의 터미널 세션에서 세 개의 MCP 서버를 함께 사용하여 모두 처리했습니다.**

</details>

<details>
<summary><strong>이슈-to-PR 워크플로</strong> - 터미널을 떠나지 않고 GitHub 이슈에서 풀 리퀘스트까지</summary>
<a id="이슈-to-pr-워크플로"></a>

#### 이슈-to-PR 워크플로 (자신의 저장소에서)

쓰기 권한이 있는 자신의 포크 또는 저장소에서 가장 잘 작동합니다.

> 💡 **지금 당장 시도하기 어려우셔도 괜찮습니다.** 읽기 전용 클론에 있다면, 과제에서 이것을 연습할 것입니다. 지금은 흐름을 이해하기 위해 읽어보세요.

```bash
copilot

> GitHub 이슈 #1의 세부 정보를 가져와 주세요.

이슈 #1: 책 연도 입력 유효성 검사 추가
상태: 열림
설명: add_book 함수가 모든 연도 값을 허용합니다...

> @samples/book-app-project/books.py 이슈 #1에 설명된 문제를 수정해 주세요.

[Copilot이 add_book()에 연도 유효성 검사를 구현합니다]

> 수정이 제대로 작동하는지 테스트를 실행해 주세요.

8개 테스트 모두 통과 ✓

> "책 앱에 연도 유효성 검사 추가"라는 제목으로 풀 리퀘스트를 생성해 주세요.

✓ PR #2 생성됨: 책 앱에 연도 유효성 검사 추가
```

**복사-붙여넣기 없음. 컨텍스트 전환 없음. 하나의 터미널 세션으로 완료.**

</details>

<details>
<summary><strong>상태 대시보드</strong> - 여러 서버를 사용하여 빠른 프로젝트 상태 확인</summary>
<a id="상태-대시보드"></a>

#### 책 앱 상태 대시보드

```bash
copilot

> 책 앱 프로젝트에 대한 상태 보고서를 제공해 주세요:
> 1. samples/book-app-project/의 Python 파일 전체에서 모든 함수를 나열해 주세요.
> 2. 타입 힌트가 있는 함수와 없는 함수를 확인해 주세요.
> 3. samples/book-app-project/tests/에 존재하는 테스트를 표시해 주세요.
> 4. 이 디렉터리의 최근 커밋 히스토리를 확인해 주세요.

책 앱 상태 보고서
=================

📊 발견된 함수:
- books.py: BookCollection의 8개 메서드 (모두 타입 힌트 있음 ✓)
- book_app.py: 6개 함수 (4개 타입 힌트 있음, 2개 없음)
- utils.py: 3개 함수 (1개 타입 힌트 있음, 2개 없음)

🧪 테스트 커버리지:
- test_books.py: BookCollection을 다루는 8개 테스트 함수
- 누락: book_app.py CLI 함수에 대한 테스트 없음
- 누락: utils.py 헬퍼 함수에 대한 테스트 없음

📝 최근 활동:
- 지난 주 3개 커밋
- 가장 최근: 테스트 픽스처 추가됨

권장 사항:
- utils.py 함수에 타입 힌트 추가
- book_app.py CLI 핸들러에 대한 테스트 추가
- 모든 파일이 적절한 크기(<100줄) - 좋은 구조!
```

**결과**: 수 초 만에 여러 데이터 소스를 집계합니다. 수동으로는 grep 실행, 줄 수 계산, git log 확인, 테스트 파일 탐색 등 최소 15분 이상 걸리는 작업입니다.

</details>

---

# 연습

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

**🎉 이제 기본 사항을 모두 이해하셨습니다!** MCP가 무엇인지, 서버를 구성하는 방법, 그리고 실제 워크플로를 살펴보셨습니다. 이제 직접 해볼 시간입니다.

---

## ▶️ 직접 해보기

이제 여러분의 차례입니다! 책 앱 프로젝트에서 MCP 서버를 사용하는 연습을 위해 이 연습 문제들을 완료하세요.

### 연습 1: MCP 상태 확인

사용 가능한 MCP 서버를 확인하는 것부터 시작해 보세요.

```bash
copilot

> /mcp show
```

GitHub 서버가 활성화되어 있는 것으로 표시되어야 합니다. 그렇지 않은 경우 `/login`을 실행하여 인증하세요.

---

### 연습 2: 파일 시스템 MCP로 책 앱 탐색

파일 시스템 서버를 구성했다면, 책 앱을 탐색해 보세요.

```bash
copilot

> samples/book-app-project/에 Python 파일이 몇 개 있나요?
> 각 파일에 정의된 함수는 무엇인가요?
```

**예상 결과**: Copilot이 `book_app.py`, `books.py`, `utils.py`와 해당 함수들을 나열합니다.

> 💡 **파일 시스템 MCP를 아직 구성하지 않으셨나요?** 위의 [완전한 구성](#완전한-구성-파일) 섹션에서 구성 파일을 생성하세요. 그런 다음 Copilot을 재시작하세요.

---

### 연습 3: GitHub MCP로 저장소 히스토리 쿼리

내장된 GitHub MCP를 사용하여 이 강의 저장소를 탐색하세요.

```bash
copilot

> 이 저장소의 마지막 커밋 5개를 나열해 주세요.

> 이 저장소에 어떤 브랜치가 있나요?
```

**예상 결과**: Copilot이 GitHub 원격에서 최근 커밋 메시지와 브랜치 이름을 표시합니다.

> ⚠️ **Codespace에서 작업하시나요?** 이것은 자동으로 작동합니다. 인증이 상속됩니다. 로컬 클론에 있다면 `gh auth status`에서 로그인되어 있는지 확인하세요.

---

### 연습 4: 여러 MCP 서버 결합

이제 파일 시스템과 GitHub MCP를 단일 세션에서 결합해 보세요.

```bash
copilot

> samples/book-app-project/data.json을 읽고 컬렉션에 어떤 책이 있는지
> 알려주세요. 그런 다음 이 파일이 마지막으로 수정된 시점을 알아보기 위해
> 최근 커밋을 확인해 주세요.
```

**예상 결과**: Copilot이 JSON 파일을 읽고(파일 시스템 MCP), "The Hobbit", "1984", "Dune", "To Kill a Mockingbird", "Mysterious Book"을 포함한 5권의 책을 나열한 후 GitHub에서 커밋 히스토리를 쿼리합니다.

**자가 점검**: "내 저장소의 커밋 히스토리를 확인해 줘"가 수동으로 `git log`를 실행하고 출력을 프롬프트에 붙여넣는 것보다 나은 이유를 설명할 수 있을 때 MCP를 이해한 것입니다.

---

## 📝 과제

### 주요 과제: 책 앱 MCP 탐색

책 앱 프로젝트에서 MCP 서버를 함께 사용하는 연습을 하세요. 단일 Copilot 세션에서 이 단계들을 완료하세요.

1. **MCP 작동 확인**: `/mcp show`를 실행하고 최소한 GitHub 서버가 활성화되어 있는지 확인하세요.
2. **파일 시스템 MCP 설정** (아직 완료하지 않은 경우): 파일 시스템 서버 구성으로 `~/.copilot/mcp-config.json`을 생성하세요.
3. **코드 탐색**: Copilot에게 파일 시스템 서버를 사용하여 다음을 요청하세요.
   - `samples/book-app-project/books.py`의 모든 함수 나열
   - `samples/book-app-project/utils.py`에서 타입 힌트가 없는 함수 확인
   - `samples/book-app-project/data.json`을 읽고 데이터 품질 문제 식별 (힌트: 마지막 항목을 살펴보세요)
4. **저장소 활동 확인**: Copilot에게 GitHub MCP를 사용하여 다음을 요청하세요.
   - `samples/book-app-project/`의 파일에 영향을 준 최근 커밋 나열
   - 열린 이슈 또는 풀 리퀘스트가 있는지 확인
5. **서버 결합**: 단일 프롬프트에서 Copilot에게 다음을 요청하세요.
   - `samples/book-app-project/tests/test_books.py`의 테스트 파일 읽기
   - 테스트된 함수를 `books.py`의 모든 함수와 비교
   - 누락된 테스트 커버리지 요약

**성공 기준**: 단일 Copilot 세션에서 파일 시스템과 GitHub MCP 데이터를 자연스럽게 결합할 수 있으며, 각 MCP 서버가 응답에 기여한 내용을 설명할 수 있습니다.

<details>
<summary>💡 힌트 (클릭하여 펼치기)</summary>

**1단계: MCP 확인**
```bash
copilot
> /mcp show
# "github"가 활성화된 것으로 표시되어야 합니다.
# 그렇지 않은 경우 실행: /login
```

**2단계: 구성 파일 생성**

위의 [완전한 구성](#완전한-구성-파일) 섹션의 JSON을 사용하여 `~/.copilot/mcp-config.json`으로 저장하세요.

**3단계: 찾아야 할 데이터 품질 문제**

`data.json`의 마지막 책은 다음과 같습니다.
```json
{
  "title": "Mysterious Book",
  "author": "",
  "year": 0,
  "read": false
}
```
작성자가 비어 있고 연도가 0입니다. 이것이 데이터 품질 문제입니다!

**5단계: 테스트 커버리지 비교**

`test_books.py`의 테스트는 `add_book`, `mark_as_read`, `remove_book`, `get_unread_books`, `find_book_by_title`을 다룹니다. `load_books`, `save_books`, `list_books` 함수에는 직접적인 테스트가 없습니다. `book_app.py`의 CLI 함수와 `utils.py`의 헬퍼에는 테스트가 전혀 없습니다.

**MCP가 작동하지 않는 경우:** 구성 파일 편집 후 Copilot을 재시작하세요.

</details>

### 보너스 과제: 사용자 지정 MCP 서버 구축

더 깊이 파고들 준비가 되셨나요? [사용자 지정 MCP 서버 가이드](mcp-custom-server.md)를 따라 모든 API에 연결할 수 있는 자신만의 MCP 서버를 Python으로 구축해 보세요.

---

<details>
<summary>🔧 <strong>일반적인 실수 및 문제 해결</strong> (클릭하여 펼치기)</summary>

### 일반적인 실수

| 실수 | 발생하는 문제 | 해결 방법 |
|------|-------------|-----------|
| GitHub MCP가 내장되어 있다는 것을 모름 | 수동으로 설치/구성 시도 | GitHub MCP는 기본으로 포함됩니다. 다음을 시도해 보세요: "이 저장소의 최근 커밋을 나열해 줘" |
| 잘못된 위치에서 구성 파일 찾기 | MCP 설정을 찾거나 편집할 수 없음 | 사용자 수준 구성은 `~/.copilot/mcp-config.json`에, 프로젝트 수준은 `.vscode/mcp.json`에 있습니다 |
| 구성 파일에 잘못된 JSON | MCP 서버가 로드되지 않음 | `/mcp show`를 사용하여 구성을 확인하고 JSON 구문을 유효성 검사하세요 |
| MCP 서버 인증을 잊음 | "인증 실패" 오류 | 일부 MCP는 별도의 인증이 필요합니다. 각 서버의 요구 사항을 확인하세요 |

### 문제 해결

**"MCP 서버를 찾을 수 없음"** - 다음을 확인하세요.
1. npm 패키지가 존재하는지: `npm view @modelcontextprotocol/server-github`
2. 구성이 유효한 JSON인지
3. 서버 이름이 구성과 일치하는지

현재 구성을 확인하려면 `/mcp show`를 사용하세요.

**"GitHub 인증 실패"** - 내장 GitHub MCP는 `/login` 자격 증명을 사용합니다. 다음을 시도해 보세요.

```bash
copilot
> /login
```

이 명령어로 GitHub에 다시 인증됩니다. 문제가 지속되면 GitHub 계정에 접근하려는 저장소에 대한 필요한 권한이 있는지 확인하세요.

**"MCP 서버 시작 실패"** - 서버 로그를 확인하세요.
```bash
# 서버 명령어를 수동으로 실행하여 오류 확인
npx -y @modelcontextprotocol/server-github
```

**MCP 도구를 사용할 수 없음** - 서버가 활성화되어 있는지 확인하세요.
```bash
copilot

> /mcp show
# 서버가 목록에 있고 활성화되어 있는지 확인하세요.
```

서버가 비활성화된 경우 재활성화 방법은 아래의 [추가 `/mcp` 명령어](#-추가-mcp-명령어)를 참조하세요.

</details>

---

<details>
<summary>📚 <strong>추가 <code>/mcp</code> 명령어</strong> (클릭하여 펼치기)</summary>
<a id="-추가-mcp-명령어"></a>

`/mcp show` 외에도 MCP 서버를 관리하는 여러 다른 명령어가 있습니다.

| 명령어 | 기능 |
|--------|------|
| `/mcp show` | 구성된 모든 MCP 서버와 상태 표시 |
| `/mcp add` | 새 서버 추가를 위한 대화형 설정 |
| `/mcp edit <server-name>` | 기존 서버 구성 편집 |
| `/mcp enable <server-name>` | 비활성화된 서버 활성화 |
| `/mcp disable <server-name>` | 서버 일시적으로 비활성화 |
| `/mcp delete <server-name>` | 서버 영구적으로 제거 |

이 강의의 대부분에서는 `/mcp show`만 있으면 됩니다. 다른 명령어는 시간이 지나면서 더 많은 서버를 관리할 때 유용해집니다.

</details>

---

# 요약

## 🔑 핵심 요점

1. **MCP**는 Copilot을 외부 서비스(GitHub, 파일 시스템, 문서)에 연결합니다.
2. **GitHub MCP는 내장되어 있습니다** - 구성이 필요 없으며 `/login`만 실행하면 됩니다.
3. **파일 시스템 및 Context7**은 `~/.copilot/mcp-config.json`을 통해 구성됩니다.
4. **다중 서버 워크플로**는 단일 세션에서 여러 소스의 데이터를 결합합니다.
5. **서버 상태 확인**은 `/mcp show`로 합니다 (서버 관리를 위한 추가 명령어도 사용 가능합니다).
6. **사용자 지정 서버**를 사용하면 모든 API에 연결할 수 있습니다 (선택 사항, 부록 가이드에서 다룸).

> 📋 **빠른 참조**: 전체 명령어 및 단축키 목록은 [GitHub Copilot CLI 명령어 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참조하세요.

---

## ➡️ 다음 단계

이제 모든 구성 요소를 갖추셨습니다: 모드, 컨텍스트, 워크플로, 에이전트, 스킬, MCP. 이제 이 모든 것을 함께 사용할 시간입니다.

**[7장: 모두 합치기](../07-putting-it-together/README.md)**에서는 다음을 배웁니다.

- 에이전트, 스킬, MCP를 통합 워크플로에서 결합
- 아이디어에서 병합된 PR까지 완전한 기능 개발
- 훅을 사용한 자동화
- 팀 환경을 위한 모범 사례

---

**[← 5장으로 돌아가기](../05-skills/README.md)** | **[7장으로 계속 →](../07-putting-it-together/README.md)**
