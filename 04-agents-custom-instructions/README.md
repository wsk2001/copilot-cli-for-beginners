![Chapter 04: Agents and Custom Instructions](images/chapter-header.png)

**파이썬 코드 리뷰어, 테스트 전문가, 보안 리뷰어를 모두 하나의 도구로 고용할 수 있다면 어떨까요?**

3장에서는 코드 리뷰, 리팩토링, 디버깅, 테스트 생성, Git 통합 등 필수적인 워크플로우를 익혔습니다. 이러한 워크플로우를 통해 GitHub Copilot CLI를 매우 효율적으로 활용할 수 있습니다. 이제 한 단계 더 나아가 보겠습니다.

지금까지 Copilot CLI는 범용 도우미로 사용해 왔습니다. 에이전트를 사용하면 타입 힌트 및 PEP 8을 준수하는 코드 리뷰어 또는 pytest 케이스를 작성하는 테스트 도우미처럼 특정 역할과 표준을 Copilot에 부여할 수 있습니다. 에이전트에 특정 지침을 입력하면 동일한 작업도 훨씬 더 나은 결과를 얻을 수 있습니다.

## 🎯 학습 목표

이 장을 마치면 다음을 수행할 수 있게 됩니다.

- 내장 에이전트 사용: 계획(`/plan`), 코드 검토(`/review`) 및 자동 에이전트(탐색, 작업) 이해
- 에이전트 파일(`.agent.md`)을 사용하여 특수 에이전트 생성
- 도메인별 작업에 에이전트 사용
- `/agent` 및 `--agent`를 사용하여 에이전트 간 전환
- 프로젝트별 표준에 맞는 사용자 지정 지침 파일 작성

> ⏱️ **예상 소요 시간**: 약 55분 (읽기 20분 + 실습 35분)

---

## 🧩 실제 사례: 전문가 고용

집에 도움이 필요할 때, "만능 해결사" 한 명을 부르는 대신 전문가를 부릅니다.

| 문제 | 전문가 | 이유 |
|---------|------------|-----|
| 누수 | 배관공 | 배관 규정을 숙지하고 특수 공구를 보유 |
| 배선 교체 | 전기 기술자 | 안전 요건을 이해하고 규정을 준수 |
| 새 지붕 | 지붕 시공 전문가 | 자재 및 지역 기후 조건을 고려 |

에이전트도 마찬가지입니다. 일반적인 AI 대신 특정 작업에 집중하고 올바른 프로세스를 따르는 에이전트를 사용하세요. 지침을 한 번만 설정하면 해당 전문 분야가 필요할 때마다 재사용할 수 있습니다. 예를 들어 코드 검토, 테스트, 보안, 문서화 등이 있습니다.

<img src="images/hiring-specialists-analogy.png" alt="Hiring Specialists Analogy - Just as you call specialized tradespeople for house repairs, AI agents are specialized for specific tasks like code review, testing, security, and documentation" width="800" />

---

# 에이전트 사용하기

내장 에이전트와 사용자 지정 에이전트를 바로 시작해 보세요.

---

## *에이전트가 처음이신가요?* 여기에서 시작하세요!
에이전트를 사용해 본 적도, 만들어 본 적도 없으신가요? 이 강좌를 시작하는 데 필요한 모든 정보를 여기에서 확인하세요.

1. **내장 에이전트를 지금 바로 사용해 보세요:**
```bash
copilot

> /plan 책 앱에 책 연도 입력 유효성 검사 추가

```
이 명령은 Plan 에이전트를 실행하여 단계별 구현 계획을 생성합니다.

2. **사용자 지정 에이전트 예제를 살펴보세요:** 에이전트의 지침을 정의하는 것은 간단합니다. 제공된 [python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md) 파일을 참조하여 패턴을 확인하세요.

3. **핵심 개념을 이해하세요:** 에이전트는 일반적인 사람이 아닌 특정 분야의 전문가에게 자문을 구하는 것과 같습니다. "프런트엔드 에이전트"는 접근성과 컴포넌트 패턴에 자동으로 초점을 맞추므로, 에이전트의 지침에 이미 명시되어 있어 별도로 상기시켜줄 필요가 없습니다.

## 내장 에이전트

**3장 개발 워크플로에서 이미 몇 가지 내장 에이전트를 사용해 보셨습니다!**
<br>`/plan`과 `/review`는 실제로 내장 에이전트입니다. 이제 내부적으로 어떤 일이 일어나는지 알게 되셨습니다. 전체 목록은 다음과 같습니다.

| 에이전트 | 호출 방법 | 기능 |
|-------|---------------|--------------|
| **계획** | `/plan` 또는 `Shift+Tab` (순환 모드) | 코딩 전에 단계별 구현 계획을 생성합니다. |
| **코드 검토** | `/review` | 스테이징/스테이징되지 않은 변경 사항을 검토하고 실행 가능한 피드백을 제공합니다. |
| **초기화** | `/init` | 프로젝트 구성 파일(지침, 에이전트)을 생성합니다. |
| **탐색** | *자동* | Copilot에 코드베이스 탐색 또는 분석을 요청할 때 내부적으로 사용됩니다. |
| **작업** | *자동* | 테스트, 빌드, 린트, 종속성 설치 등의 명령을 실행합니다.

<br>

**내장 에이전트 작동 예시** - 계획, 코드 검토, 탐색, 작업 실행 예시

```bash
copilot

# Invoke the Plan agent to create an implementation plan
> /plan Add input validation for book year in the book app

# Invoke the Code-review agent on your changes
> /review

# Explore and Task agents are invoked automatically when relevant:
> Run the test suite        # Uses Task agent

> Explore how book data is loaded    # Uses Explore agent
```

태스크 에이전트는 어떨까요? 태스크 에이전트는 백그라운드에서 작동하여 진행 상황을 관리하고 추적하며, 다음과 같이 깔끔하고 명확한 형식으로 보고합니다.

| 결과 | 표시되는 내용 |
|---------|--------------|
| ✅ **성공** | 간략한 요약 (예: "247개 테스트 모두 통과", "빌드 성공") |
| ❌ **실패** | 스택 트레이스, 컴파일러 오류 및 자세한 로그를 포함한 전체 출력 |

> 📚 **공식 문서**: [GitHub Copilot CLI 에이전트](https://docs.github.com/copilot/how-tos/use-copilot-agents/use-copilot-cli#use-custom-agents)

---

# Copilot CLI에 에이전트 추가

워크플로우에 포함할 에이전트를 직접 정의할 수 있습니다! 한 번 정의하면 바로 사용할 수 있습니다!

<img src="images/using-agents.png" alt="Four colorful AI robots standing together, each with different tools representing specialized agent capabilities" width="800"/>

## 🗂️ 에이전트 추가하기

에이전트 파일은 `.agent.md` 확장자를 가진 마크다운 파일입니다. 에이전트 파일은 YAML 프론트매터(메타데이터)와 마크다운 명령어 두 부분으로 구성됩니다.

> 💡 **YAML 프론트매터가 처음이신가요?** 파일 맨 위에 `---` 태그로 둘러싸인 작은 설정 블록입니다. YAML은 `키: 값` 쌍으로 이루어져 있습니다. 파일의 나머지 부분은 일반 마크다운입니다.

다음은 최소한의 에이전트 예시입니다.

```markdown
---
name: my-reviewer
description: 버그와 보안 문제에 집중한 코드 리뷰어입니다
---

# Code Reviewer

당신은 버그와 보안 문제를 찾는 데 집중하는 코드 리뷰어입니다.

코드를 검토할 때는 항상 다음을 확인하세요.:
- SQL 인젝션 취약점
- 오류 처리 누락
- 하드코딩된 비밀
```

> 💡 **필수 vs 선택**: `description` 필드는 필수입니다. `name`, `tools`, `model` 등의 필드는 선택 사항입니다.

## 에이전트 파일 위치

| 위치 | 범위 | 최적 사용처 |
|----------|-------|----------|
| `.github/agents/` | 프로젝트별 | 프로젝트 규칙을 따르는 팀 공유 에이전트 |
| `~/.copilot/agents/` | 전역 (모든 프로젝트) | 어디에서나 사용하는 개인 에이전트 |

**이 프로젝트에는 `.github/agents/` 폴더에 샘플 에이전트 파일이 포함되어 있습니다.** 직접 에이전트 파일을 작성하거나 제공된 파일을 사용자 지정할 수 있습니다.

<details>
<summary>📂 이 과정에서 샘플 에이전트를 확인하세요</summary>

| 파일 | 설명 |
|------|-------------|
| `hello-world.agent.md` | 최소 예제 - 여기서 시작하세요 |
| `python-reviewer.agent.md` | Python 코드 품질 검토자 |
| `pytest-helper.agent.md` | Pytest 테스트 전문가 |

```bash
# 또는 개인 에이전트 폴더(모든 프로젝트에서 사용 가능)에 복사하세요.
cp .github/agents/python-reviewer.agent.md ~/.copilot/agents/
```

더 많은 커뮤니티 에이전트를 보려면 [github/awesome-copilot](https://github.com/github/awesome-copilot)을 참조하세요.

</details>


## 🚀 사용자 지정 상담원 사용 방법 두 가지

### 대화형 모드
대화형 모드에서 `/agent` 명령어를 사용하여 상담원 목록을 확인하고, 작업을 시작할 상담원을 선택합니다.

대화를 계속할 상담원을 선택합니다.

```bash
copilot
> /agent
```

다른 에이전트로 변경하거나 기본 모드로 되돌리려면 `/agent` 명령을 다시 사용하십시오.

### 프로그래밍 방식 모드

Agent와 바로 새 세션을 시작하세요.

```bash
copilot --agent python-reviewer
> Review @samples/book-app-project/books.py
```

> 💡**에이전트 전환**: `/agent` 또는 `--agent`를 다시 사용하여 언제든지 다른 에이전트로 전환할 수 있습니다. 표준 Copilot CLI 환경으로 돌아가려면 `/agent`를 사용하고 **에이전트 없음**을 선택하십시오.

---

# 에이전트와 더 깊이 소통하기

<img src="images/creating-custom-agents.png" alt="Robot being assembled on a workbench surrounded by components and tools representing custom agent creation" width="800"/>

> 💡 **이 섹션은 선택 사항입니다.** 내장 에이전트(`/plan`, `/review`)는 대부분의 워크플로에 충분히 강력합니다. 작업 전반에 걸쳐 일관되게 적용되는 전문적인 지식이 필요한 경우 사용자 지정 에이전트를 생성하세요.

아래 각 항목은 독립적입니다. **관심 있는 항목만 선택하세요. 한 번에 모두 읽을 필요는 없습니다.**

| 지금 하고 싶은 것... | 바로가기 |
|---|---|
| 에이전트가 일반 프롬프트보다 나은 이유 알아보기 | [전문가 vs 일반](#specialist-vs-generic-see-the-difference) |
| 기능에 에이전트 결합하기 | [여러 에이전트 사용](#working-with-multiple-agents) |
| 에이전트 구성, 이름 지정 및 공유하기 | [에이전트 구성 및 공유](#organizing--sharing-agents) |
| 상시 작동 프로젝트 컨텍스트 설정하기 | [Copilot용 프로젝트 구성](#configuring-your-project-for-copilot) |
| YAML 속성 및 도구 찾아보기 | [에이전트 파일 참조](#agent-file-reference) |

아래 시나리오를 선택하여 자세히 살펴보세요.

---

<a id="specialist-vs-generic-see-the-difference"></a>
<details>
<summary><strong>Specialist vs Generic: See the Difference</strong> - Why agents produce better output than generic prompts</summary>

## Specialist vs Generic: See the Difference

This is where agents prove their value. Watch the difference:

### Without an Agent (Generic Copilot)

```bash
copilot

> Add a function to search books by year range in the book app
```

**Generic output**:
```python
def search_by_year_range(books, start_year, end_year):
    results = []
    for book in books:
        if book['year'] >= start_year and book['year'] <= end_year:
            results.append(book)
    return results
```

Basic. Works. But missing a lot.

---

### With the Python Reviewer Agent

```bash
copilot

> /agent
# Select "python-reviewer"

> Add a function to search books by year range in the book app
```

**Specialist output**:
```python
from typing import List, Dict, Any

def search_by_year_range(
    books: List[Dict[str, Any]],
    start_year: int,
    end_year: int
) -> List[Dict[str, Any]]:
    """
    Search for books within a specified year range.

    Args:
        books: List of book dictionaries
        start_year: Starting year (inclusive)
        end_year: Ending year (inclusive)

    Returns:
        List of books published within the year range

    Raises:
        ValueError: If start_year is greater than end_year
        TypeError: If year values are not integers
    """
    if not isinstance(start_year, int) or not isinstance(end_year, int):
        raise TypeError("Year values must be integers")

    if start_year > end_year:
        raise ValueError(f"Start year ({start_year}) cannot be greater than end year ({end_year})")

    return [
        book for book in books
        if isinstance(book.get('year'), int)
        and start_year <= book['year'] <= end_year
    ]
```

**What the python-reviewer agent automatically includes**:
- ✅ Type hints on all parameters and return values
- ✅ Comprehensive docstring with Args/Returns/Raises
- ✅ Input validation with proper error handling
- ✅ List comprehension for better performance
- ✅ Edge case handling (missing/invalid year values)
- ✅ PEP 8 compliant formatting
- ✅ Defensive programming practices

**The difference**: Same prompt, dramatically better output. The agent brings expertise you'd forget to ask for.

</details>

---

<a id="working-with-multiple-agents"></a>
<details>
<summary><strong>Working with Multiple Agents</strong> - Combine specialists, switch mid-session, agent-as-tools</summary>

## Working with Multiple Agents

The real power comes when specialists work together on a feature.

### Example: Building a Simple Feature

```bash
copilot

> I want to add a "search by year range" feature to the book app

# Use python-reviewer for design
> /agent
# Select "python-reviewer"

> @samples/book-app-project/books.py Design a find_by_year_range method. What's the best approach?

# Switch to pytest-helper for test design
> /agent
# Select "pytest-helper"

> @samples/book-app-project/tests/test_books.py Design test cases for a find_by_year_range method.
> What edge cases should we cover?

# Synthesize both designs
> Create an implementation plan that includes the method implementation and comprehensive tests.
```

**The key insight**: You're the architect directing specialists. They handle the details, you handle the vision.

<details>
<summary>🎬 See it in action!</summary>

![Python Reviewer Demo](images/python-reviewer-demo.gif)

*Demo output varies - your model, tools, and responses will differ from what's shown here.*

</details>

### Agent as Tools

When agents are configured, Copilot can also call them as tools during complex tasks. If you ask for a full-stack feature, Copilot may automatically delegate parts to the appropriate specialist agents.

</details>

---

<a id="organizing--sharing-agents"></a>
<details>
<summary><strong>Organizing & Sharing Agents</strong> - Naming, file placement, instruction files, and team sharing</summary>

## Organizing & Sharing Agents

### Naming Your Agents

When you create agent files, the name matters. It's what you'll type after `/agent` or `--agent`, and what your teammates will see in the agent list.

| ✅ Good Names | ❌ Avoid |
|--------------|----------|
| `frontend` | `my-agent` |
| `backend-api` | `agent1` |
| `security-reviewer` | `helper` |
| `react-specialist` | `code` |
| `python-backend` | `assistant` |

**Naming conventions:**
- Use lowercase with hyphens: `my-agent-name.agent.md`
- Include the domain: `frontend`, `backend`, `devops`, `security`
- Be specific when needed: `react-typescript` vs just `frontend`

---

### Sharing with Your Team

Place agent files in `.github/agents/` and they're version controlled. Push to your repo and every team member gets them automatically. But agents are just one type of file Copilot reads from your project. It also supports **instruction files** that apply automatically to every session, without anyone needing to run `/agent`.

Think of it this way: agents are specialists you call on, and instruction files are team rules that are always active.

### Where to Put Your Files

You already know the two main locations (see [Where to put agent files](#where-to-put-agent-files) above). Use this decision tree to choose:

<img src="images/agent-file-placement-decision-tree.png" alt="Decision tree for where to put agent files: experimenting → current folder, team use → .github/agents/, everywhere → ~/.copilot/agents/" width="800"/>

**Start simple:** Create a single `*.agent.md` file in your project folder. Move it to a permanent location once you're happy with it.

Beyond agent files, Copilot also reads **project-level instruction files** automatically, no `/agent` needed. See [Configuring Your Project for Copilot](#configuring-your-project-for-copilot) below for `AGENTS.md`, `.instructions.md`, and `/init`.

</details>

---

<a id="configuring-your-project-for-copilot"></a>
<details>
<summary><strong>Configuring Your Project for Copilot</strong> - AGENTS.md, instruction files, and /init setup</summary>

## Configuring Your Project for Copilot

Agents are specialists you invoke on demand. **Project configuration files** are different: Copilot reads them automatically in every session to understand your project's conventions, tech stack, and rules. No one needs to run `/agent`; the context is always active for everyone working in the repo.

### Quick Setup with /init

The fastest way to get started is to let Copilot generate configuration files for you:

```bash
copilot
> /init
```

Copilot will scan your project and create tailored instruction files. You can edit them afterwards.

### Instruction File Formats

| File | Scope | Notes |
|------|-------|-------|
| `AGENTS.md` | Project root or nested | **Cross-platform standard** - works with Copilot and other AI assistants |
| `.github/copilot-instructions.md` | Project | GitHub Copilot specific |
| `.github/instructions/*.instructions.md` | Project | Granular, topic-specific instructions |
| `CLAUDE.md`, `GEMINI.md` | Project root | Supported for compatibility |

> 🎯 **Just getting started?** Use `AGENTS.md` for project instructions. You can explore the other formats later as needed.

### AGENTS.md

`AGENTS.md` is the recommended format. It's an [open standard](https://agents.md/) that works across Copilot and other AI coding tools. Place it in your repository root and Copilot reads it automatically. This project's own [AGENTS.md](../AGENTS.md) is a working example.

A typical `AGENTS.md` describes your project context, code style, security requirements, and testing standards. Use `/init` to generate one, or write your own following the pattern in our example file.

### Custom Instruction Files (.instructions.md)

For teams that want more granular control, split instructions into topic-specific files. Each file covers one concern and applies automatically:

```
.github/
└── instructions/
    ├── python-standards.instructions.md
    ├── security-checklist.instructions.md
    └── api-design.instructions.md
```

> 💡 **Note**: Instruction files work with any language. This example uses Python to match our course project, but you can create similar files for TypeScript, Go, Rust, or any technology your team uses.

**Finding community instruction files**: Browse [github/awesome-copilot](https://github.com/github/awesome-copilot) for pre-made instruction files covering .NET, Angular, Azure, Python, Docker, and many more technologies.

### Disabling Custom Instructions

If you need Copilot to ignore all project-specific configurations (useful for debugging or comparing behavior):

```bash
copilot --no-custom-instructions
```

</details>

---

<a id="agent-file-reference"></a>
<details>
<summary><strong>Agent File Reference</strong> - YAML properties, tool aliases, and complete examples</summary>

## Agent File Reference

### A More Complete Example

You've seen the [minimal agent format](#-add-your-agents) above. Here's a more comprehensive agent that uses the `tools` property. Create `~/.copilot/agents/python-reviewer.agent.md`:

```markdown
---
name: python-reviewer
description: Python code quality specialist for reviewing Python projects
tools: ["read", "edit", "search", "execute"]
---

# Python Code Reviewer

You are a Python specialist focused on code quality and best practices.

**Your focus areas:**
- Code quality (PEP 8, type hints, docstrings)
- Performance optimization (list comprehensions, generators)
- Error handling (proper exception handling)
- Maintainability (DRY principles, clear naming)

**Code style requirements:**
- Use Python 3.10+ features (dataclasses, type hints, pattern matching)
- Follow PEP 8 naming conventions
- Use context managers for file I/O
- All functions must have type hints and docstrings

**When reviewing code, always check:**
- Missing type hints on function signatures
- Mutable default arguments
- Proper error handling (no bare except)
- Input validation completeness
```

### YAML Properties

| Property | Required | Description |
|----------|----------|-------------|
| `name` | No | Display name (defaults to filename) |
| `description` | **Yes** | What the agent does - helps Copilot understand when to suggest it |
| `tools` | No | List of allowed tools (omit = all tools available). See tool aliases below. |
| `target` | No | Limit to `vscode` or `github-copilot` only |

### Tool Aliases

Use these names in the `tools` list:
- `read` - Read file contents
- `edit` - Edit files
- `search` - Search files (grep/glob)
- `execute` - Run shell commands (also: `shell`, `Bash`)
- `agent` - Invoke other custom agents

> 📖 **Official docs**: [Custom agents configuration](https://docs.github.com/copilot/reference/custom-agents-configuration)
>
> ⚠️ **VS Code Only**: The `model` property (for selecting AI models) works in VS Code but is not supported in GitHub Copilot CLI. You can safely include it for cross-platform agent files. GitHub Copilot CLI will ignore it.

### More Agent Templates

> 💡 **Note for beginners**: The examples below are templates. **Replace the specific technologies with whatever your project uses.** The important thing is the *structure* of the agent, not the specific technologies mentioned.

This project includes working examples in the [.github/agents/](../.github/agents/) folder:
- [hello-world.agent.md](../.github/agents/hello-world.agent.md) - Minimal example, start here
- [python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md) - Python code quality reviewer
- [pytest-helper.agent.md](../.github/agents/pytest-helper.agent.md) - Pytest testing specialist

For community agents, see [github/awesome-copilot](https://github.com/github/awesome-copilot).

</details>

---

# 연습

<img src="../images/practice.png" alt="코드가 표시되는 모니터, 램프, 커피잔, 헤드폰이 갖춰진 편안한 책상 환경" width="800"/>

나만의 에이전트를 만들고 실제로 작동하는 모습을 확인해 보세요.

---

## ▶️ 직접 시도해 보세요

```bash

# Create the agents directory (if it doesn't exist)
mkdir -p .github/agents

# Create a code reviewer agent
cat > .github/agents/reviewer.agent.md << 'EOF'
---
name: reviewer
description: Senior code reviewer focused on security and best practices
---

# Code Reviewer Agent

You are a senior code reviewer focused on code quality.

**Review priorities:**
1. Security vulnerabilities
2. Performance issues
3. Maintainability concerns
4. Best practice violations

**Output format:**
Provide issues as a numbered list with severity tags:
[CRITICAL], [HIGH], [MEDIUM], [LOW]
EOF

# Create a documentation agent
cat > .github/agents/documentor.agent.md << 'EOF'
---
name: documentor
description: Technical writer for clear and complete documentation
---

# Documentation Agent

You are a technical writer who creates clear documentation.

**Documentation standards:**
- Start with a one-sentence summary
- Include usage examples
- Document parameters and return values
- Note any gotchas or limitations
EOF

# Now use them
copilot --agent reviewer
> Review @samples/book-app-project/books.py

# Or switch agents
copilot
> /agent
# Select "documentor"
> Document @samples/book-app-project/books.py
```

---

## 📝 과제

### 주요 과제: 전문 에이전트 팀 구축

실습 예제에서는 `검토자` 및 `문서 작성자` 에이전트를 생성했습니다. 이제 다른 작업을 위해 에이전트를 만들고 사용하는 연습을 해보세요. 바로 도서 앱의 데이터 유효성 검사를 개선하는 것입니다.

1. 도서 앱에 맞게 에이전트 파일 3개(각 에이전트당 1개씩, 총 3개)를 생성하여 `.github/agents/` 폴더에 배치합니다.
2. 에이전트:

- **데이터 유효성 검사기**: `data.json` 파일에서 누락되거나 형식이 잘못된 데이터(저자 필드가 비어 있거나, 연도가 0이거나, 필드가 누락된 경우 등)를 검사합니다.

- **오류 처리기**: 일관성 없는 오류 처리를 보이는 Python 코드를 검토하고 통일된 접근 방식을 제안합니다.

- **문서 작성기**: 문서 문자열과 README 파일 내용을 생성하거나 업데이트합니다.
3. 도서 앱에서 각 에이전트를 사용합니다.

- `데이터 유효성 검사기` → `@samples/book-app-project/data.json` 파일 감사

- `오류 처리기` → `@samples/book-app-project/books.py` 및 `@samples/book-app-project/utils.py` 파일 검토

- `문서 작성기` → 문서 문자열 추가 `@samples/book-app-project/books.py`
4. 협업: `error-handler`를 사용하여 오류 처리의 부족한 부분을 파악한 다음, `doc-writer`를 사용하여 개선된 접근 방식을 문서화합니다.

**성공 기준**: 일관되고 고품질의 출력을 생성하는 3개의 에이전트가 있으며, `/agent`를 사용하여 에이전트 간에 전환할 수 있습니다.

<details>
<summary>💡 Hints (click to expand)</summary>

**Starter templates**: create one file per agent in `.github/agents/`:

`data-validator.agent.md`:
```markdown
---
description: Analyzes JSON data files for missing or malformed entries
---

You analyze JSON data files for missing or malformed entries.

**Focus areas:**
- Empty or missing author fields
- Invalid years (year=0, future years, negative years)
- Missing required fields (title, author, year, read)
- Duplicate entries
```

`error-handler.agent.md`:
```markdown
---
description: Reviews Python code for error handling consistency
---

You review Python code for error handling consistency.

**Standards:**
- No bare except clauses
- Use custom exceptions where appropriate
- All file operations use context managers
- Consistent return types for success/failure
```

`doc-writer.agent.md`:
```markdown
---
description: Technical writer for clear Python documentation
---

You are a technical writer who creates clear Python documentation.

**Standards:**
- Google-style docstrings
- Include parameter types and return values
- Add usage examples for public methods
- Note any exceptions raised
```

**Testing your agents:**

> 💡 **Note:** You should already have `samples/book-app-project/data.json` in your local copy of this repo. If it is missing, download the original version from the source repo:
> [data.json](https://github.com/github/copilot-cli-for-beginners/blob/main/samples/book-app-project/data.json)

```bash
copilot
> /agent
# Select "data-validator" from the list
> @samples/book-app-project/data.json Check for books with empty author fields or invalid years
```

**Tip:** The `description` field in the YAML frontmatter is required for agents to work.

</details>

### 보너스 과제: 명령어 라이브러리

이미 필요에 따라 호출하는 에이전트를 구축했습니다. 이제 그 반대, 즉 Copilot이 매 세션마다 자동으로 읽는 **명령어 파일**을 만들어 보세요. `/agent` 명령어는 필요하지 않습니다.

`.github/instructions/` 폴더에 다음 명령어 파일을 최소 3개 이상 생성하세요.
- `python-style.instructions.md`: PEP 8 및 타입 힌트 규칙 준수
- `test-standards.instructions.md`: 테스트 파일에서 pytest 규칙 준수
- `data-quality.instructions.md`: JSON 데이터 항목 유효성 검사

각 명령어 파일을 도서 앱 코드에 적용하여 테스트하세요.

---

<details>
<summary>🔧 <strong>Common Mistakes & Troubleshooting</strong> (click to expand)</summary>

### Common Mistakes

| Mistake | What Happens | Fix |
|---------|--------------|-----|
| Missing `description` in agent frontmatter | Agent won't load or won't be discoverable | Always include `description:` in YAML frontmatter |
| Wrong file location for agents | Agent not found when you try to use it | Place in `~/.copilot/agents/` (personal) or `.github/agents/` (project) |
| Using `.md` instead of `.agent.md` | File may not be recognized as an agent | Name files like `python-reviewer.agent.md` |
| Overly long agent prompts | May hit the 30,000 character limit | Keep agent definitions focused; use skills for detailed instructions |

### Troubleshooting

**Agent not found** - Check that the agent file exists in one of these locations:
- `~/.copilot/agents/`
- `.github/agents/`

List available agents:

```bash
copilot
> /agent
# Shows all available agents
```

**Agent not following instructions** - Be explicit in your prompts and add more detail to agent definitions:
- Specific frameworks/libraries with versions
- Team conventions
- Example code patterns

**Custom instructions not loading** - Run `/init` in your project to set up project-specific instructions:

```bash
copilot
> /init
```

Or check if they're disabled:
```bash
# Don't use --no-custom-instructions if you want them loaded
copilot  # This loads custom instructions by default
```

</details>

---

# 요약

## 🔑 핵심 요점

1. **내장 에이전트**: `/plan` 및 `/review`가 직접 호출됩니다. 탐색 및 작업은 자동으로 수행됩니다.
2. **사용자 지정 에이전트**는 `.agent.md` 파일에 정의된 전문가입니다.
3. **우수한 에이전트**는 명확한 전문성, 표준 및 출력 형식을 갖추고 있습니다.
4. **다중 에이전트 협업**은 전문성을 결합하여 복잡한 문제를 해결합니다.
5. **지침 파일**(`.instructions.md`)은 자동 적용을 위한 팀 표준을 인코딩합니다.
6. **일관된 출력**은 잘 정의된 에이전트 지침에서 비롯됩니다.

> 📋 **빠른 참조**: 전체 명령 및 바로 가기 목록은 [GitHub Copilot CLI 명령 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참조하세요.

---

## ➡️ 다음 단계

에이전트는 Copilot이 코드에서 특정 작업을 수행하는 방식(*어떻게 접근하고 실행하는지*)을 변경합니다. 다음으로는 Copilot이 따르는 단계(*어떤 단계를 따르는지*)를 변경하는 **스킬**에 대해 알아보겠습니다. 에이전트와 스킬의 차이점이 궁금하신가요? 5장에서 자세히 다룹니다.

**[5장: 스킬 시스템](../05-skills/README.md)**에서는 다음 내용을 학습합니다.

- 프롬프트에서 스킬이 자동으로 실행되는 방법(슬래시 명령 필요 없음)
- 커뮤니티 스킬 설치
- SKILL.md 파일을 사용하여 사용자 지정 스킬 생성
- 에이전트, 스킬, MCP의 차이점
- 각각을 사용해야 하는 시점

---

**[← 3장으로 돌아가기](../03-development-workflows/README.md)** | **[5장으로 계속 →](../05-skills/README.md)**
