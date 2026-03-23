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

## Workflow 1: Bug Investigation and Fix

Real-world bug fixing with full tool integration:

```bash
copilot

# PHASE 1: Understand the bug from GitHub (MCP provides this)
> Get the details of issue #1

# Learn: "find_by_author doesn't work with partial names"

# PHASE 2: Research best practice (deep research with web + GitHub sources)
> /research Best practices for Python case-insensitive string matching

# PHASE 3: Find related code
> @samples/book-app-project/books.py Show me the find_by_author method

# PHASE 4: Get expert analysis
> /agent
# Select "python-reviewer"

> Analyze this method for issues with partial name matching

# Agent identifies: Method uses exact equality instead of substring matching

# PHASE 5: Fix with agent guidance
> Implement the fix using lowercase comparison and 'in' operator

# PHASE 6: Generate tests
> /agent
# Select "pytest-helper"

> Generate pytest tests for find_by_author with partial matches
> Include test cases: partial name, case variations, no matches

# PHASE 7: Commit and PR
> Generate a commit message for this fix

> Create a pull request linking to issue #1
```

---

## Workflow 2: Code Review Automation (Optional)

> 💡 **This section is optional.** Pre-commit hooks are useful for teams but not required to be productive. Skip this if you're just getting started.
>
> ⚠️ **Performance note**: This hook calls `copilot -p` for each staged file, which takes several seconds per file. For large commits, consider limiting to critical files or running reviews manually with `/review` instead.

A **git hook** is a script that Git runs automatically at certain points, For example, right before a commit. You can use this to run automated checks on your code. Here's how to set up an automated Copilot review on your commits:

```bash
# Create a pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# Get staged files (Python files only)
STAGED=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.py$')

if [ -n "$STAGED" ]; then
  echo "Running Copilot review on staged files..."

  for file in $STAGED; do
    echo "Reviewing $file..."

    # Use timeout to prevent hanging (60 seconds per file)
    # --allow-all auto-approves file reads/writes so the hook can run unattended.
    # Only use this in automated scripts. In interactive sessions, let Copilot ask for permission.
    REVIEW=$(timeout 60 copilot --allow-all -p "Quick security review of @$file - critical issues only" 2>/dev/null)

    # Check if timeout occurred
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

  echo "Review passed"
fi
EOF

chmod +x .git/hooks/pre-commit
```

> ⚠️ **macOS users**: The `timeout` command is not included by default on macOS. Install it with `brew install coreutils` or replace `timeout 60` with a simple invocation without a timeout guard.

> 📚 **Official Documentation**: [Use hooks](https://docs.github.com/copilot/how-tos/copilot-cli/use-hooks) and [Hooks configuration reference](https://docs.github.com/copilot/reference/hooks-configuration) for the complete hooks API.
>
> 💡 **Built-in alternative**: Copilot CLI also has a built-in hooks system (`copilot hooks`) that can run automatically on events like pre-commit. The manual git hook above gives you full control, while the built-in system is simpler to configure. See the docs above to decide which approach fits your workflow.

Now every commit gets a quick security review:

```bash
git add samples/book-app-project/books.py
git commit -m "Update book collection methods"

# Output:
# Running Copilot review on staged files...
# Reviewing samples/book-app-project/books.py...
# Critical issues found in samples/book-app-project/books.py:
# - Line 15: File path injection vulnerability in load_from_file
#
# Fix the issue and try again.
```

---

## Workflow 3: Onboarding to a New Codebase

When joining a new project, combine context, agents, and MCP to ramp up fast:

```bash
# Start Copilot in interactive mode
copilot

# PHASE 1: Get the big picture with context
> @samples/book-app-project/ Explain the high-level architecture of this codebase

# PHASE 2: Understand a specific flow
> @samples/book-app-project/book_app.py Walk me through what happens
> when a user runs "python book_app.py add"

# PHASE 3: Get expert analysis with an agent
> /agent
# Select "python-reviewer"

> @samples/book-app-project/books.py Are there any design issues,
> missing error handling, or improvements you would recommend?

# PHASE 4: Find something to work on (MCP provides GitHub access)
> List open issues labeled "good first issue"

# PHASE 5: Start contributing
> Pick the simplest open issue and outline a plan to fix it
```

This workflow combines `@` context, agents, and MCP into a single onboarding session, exactly the integration pattern from earlier in this chapter.

---

# Best Practices & Automation

Patterns and habits that make your workflows more effective.

---

## Best Practices

### 1. Start with Context Before Analysis

Always gather context before asking for analysis:

```bash
# Good
> Get the details of issue #42
> /agent
# Select python-reviewer
> Analyze this issue

# Less effective
> /agent
# Select python-reviewer
> Fix login bug
# Agent doesn't have issue context
```

### 2. Know the Difference: Agents, Skills, and Custom Instructions

Each tool has a sweet spot:

```bash
# Agents: Specialized personas you explicitly activate
> /agent
# Select python-reviewer
> Review this authentication code for security issues

# Skills: Modular capabilities that auto-activate when your prompt
# matches the skill's description (you must create them first — see Ch 05)
> Generate comprehensive tests for this code
# If you have a testing skill configured, it activates automatically

# Custom instructions (.github/copilot-instructions.md): Always-on
# guidance that applies to every session without switching or triggering
```

> 💡 **Key point**: Agents and skills can both analyze AND generate code. The real difference is **how they activate** — agents are explicit (`/agent`), skills are automatic (prompt-matched), and custom instructions are always on.

### 3. Keep Sessions Focused

Use `/rename` to label your session (makes it easy to find in history) and `/exit` to end it cleanly:

```bash
# Good: One feature per session
> /rename list-unread-feature
# Work on list unread
> /exit

copilot
> /rename export-csv-feature
# Work on CSV export
> /exit

# Less effective: Everything in one long session
```

### 4. Make Workflows Reusable with Copilot

Instead of just documenting workflows in a wiki, encode them directly in your repo where Copilot can use them:

- **Custom instructions** (`.github/copilot-instructions.md`): Always-on guidance for coding standards, architecture rules, and build/test/deploy steps. Every session follows them automatically.
- **Prompt files** (`.github/prompts/`): Reusable, parameterized prompts your team can share — like templates for code reviews, component generation, or PR descriptions.
- **Custom agents** (`.github/agents/`): Encode specialized personas (e.g., a security reviewer or a docs writer) that anyone on the team can activate with `/agent`.
- **Custom skills** (`.github/skills/`): Package step-by-step workflow instructions that auto-activate when relevant.

> 💡 **The payoff**: New team members get your workflows for free — they're built into the repo, not locked in someone's head.

---

## Bonus: Production Patterns

These patterns are optional but valuable for professional environments.

### PR Description Generator

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

### CI/CD Integration

For teams with existing CI/CD pipelines, you can automate Copilot reviews on every pull request using GitHub Actions. This includes posting review comments automatically and filtering for critical issues.

> 📖 **Learn more**: See [CI/CD Integration](../appendices/ci-cd-integration.md) for complete GitHub Actions workflows, configuration options, and troubleshooting tips.

---

# Practice

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

Put the complete workflow into practice.

---

## ▶️ Try It Yourself

After completing the demos, try these variations:

1. **End-to-End Challenge**: Pick a small feature (e.g., "list unread books" or "export to CSV"). Use the full workflow:
   - Plan with `/plan`
   - Design with agents (python-reviewer, pytest-helper)
   - Implement
   - Generate tests
   - Create PR

2. **Automation Challenge**: Set up the pre-commit hook from the Code Review Automation workflow. Make a commit with an intentional file path vulnerability. Does it get blocked?

3. **Your Production Workflow**: Design your own workflow for a common task you do. Write it down as a checklist. What parts could be automated with skills, agents, or hooks?

**Self-Check**: You've completed the course when you can explain to a colleague how agents, skills, and MCP work together - and when to use each.

---

## 📝 Assignment

### Main Challenge: End-to-End Feature

The hands-on examples walked through building a "list unread books" feature. Now practice the full workflow on a different feature: **search books by year range**:

1. Start Copilot and gather context: `@samples/book-app-project/books.py`
2. Plan with `/plan Add a "search by year" command that lets users find books published between two years`
3. Implement a `find_by_year_range(start_year, end_year)` method in `BookCollection`
4. Add a `handle_search_year()` function in `book_app.py` that prompts the user for start and end years
5. Generate tests: `@samples/book-app-project/books.py @samples/book-app-project/tests/test_books.py Generate tests for find_by_year_range() including edge cases like invalid years, reversed range, and no results.`
6. Review with `/review`
7. Update the README: `@samples/book-app-project/README.md Add documentation for the new "search by year" command.`
8. Generate a commit message

Document your workflow as you go.

**Success criteria**: You've completed the feature from idea to commit using Copilot CLI, including planning, implementation, tests, documentation, and review.

> 💡 **Bonus**: If you have agents set up from Chapter 04, try creating and using custom agents. For example, an error-handler agent for implementation review and a doc-writer agent for the README update.

<details>
<summary>💡 Hints (click to expand)</summary>

**Follow the pattern from the ["Idea to Merged PR"](#idea-to-merged-pr-in-one-session) example** at the top of this chapter. The key steps are:

1. Gather context with `@samples/book-app-project/books.py`
2. Plan with `/plan Add a "search by year" command`
3. Implement the method and command handler
4. Generate tests with edge cases (invalid input, empty results, reversed range)
5. Review with `/review`
6. Update README with `@samples/book-app-project/README.md`
7. Generate commit message with `-p`

**Edge cases to think about:**
- What if the user enters "2000" and "1990" (reversed range)?
- What if no books match the range?
- What if the user enters non-numeric input?

**The key is practicing the full workflow** from idea → context → plan → implement → test → document → commit.

</details>

---

<details>
<summary>🔧 <strong>Common Mistakes</strong> (click to expand)</summary>

| Mistake | What Happens | Fix |
|---------|--------------|-----|
| Jumping straight to implementation | Miss design issues that are costly to fix later | Use `/plan` first to think through the approach |
| Using one tool when multiple would help | Slower, less thorough results | Combine: Agent for analysis → Skill for execution → MCP for integration |
| Not reviewing before committing | Security issues or bugs slip through | Always run `/review` or use a [pre-commit hook](#workflow-2-code-review-automation-optional) |
| Forgetting to share workflows with team | Each person reinvents the wheel | Document patterns in shared agents, skills, and instructions |

</details>

---

# Summary

## 🔑 Key Takeaways

1. **Integration > Isolation**: Combine tools for maximum impact
2. **Context first**: Always gather required context before analysis
3. **Agents analyze, Skills execute**: Use the right tool for the job
4. **Automate repetition**: Hooks and scripts multiply your effectiveness
5. **Document workflows**: Shareable patterns benefit the whole team

> 📋 **Quick Reference**: See the [GitHub Copilot CLI command reference](https://docs.github.com/en/copilot/reference/cli-command-reference) for a complete list of commands and shortcuts.

---

## 🎓 Course Complete!

Congratulations! You've learned:

| Chapter | What You Learned |
|---------|-------------------|
| 00 | Copilot CLI installation and Quick Start |
| 01 | Three modes of interaction |
| 02 | Context management with @ syntax |
| 03 | Development workflows |
| 04 | Specialized agents |
| 05 | Extensible skills |
| 06 | External connections with MCP |
| 07 | Unified production workflows |

You're now equipped to use GitHub Copilot CLI as a genuine force multiplier in your development workflow.

## ➡️ What's Next

Your learning doesn't stop here:

1. **Practice daily**: Use Copilot CLI for real work
2. **Build custom tools**: Create agents and skills for your specific needs
3. **Share knowledge**: Help your team adopt these workflows
4. **Stay updated**: Follow GitHub Copilot updates for new features

### Resources

- [GitHub Copilot CLI Documentation](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [Community Skills](https://github.com/topics/copilot-skill)

---

**Great job! Now go build something amazing.**

**[← Back to Chapter 06](../06-mcp-servers/README.md)** | **[Return to Course Home →](../README.md)**
