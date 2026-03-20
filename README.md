![GitHub Copilot CLI for Beginners](./images/copilot-banner.png)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)&ensp;
[![Open project in GitHub Codespaces](https://img.shields.io/badge/Codespaces-Open-blue?style=flat-square&logo=github)](https://codespaces.new/github/copilot-cli-for-beginners?hide_repo_select=true&ref=main&quickstart=true)&ensp;
[![Official Copilot CLI documentation](https://img.shields.io/badge/GitHub-CLI_Documentation-00a3ee?style=flat-square&logo=github)](https://docs.github.com/en/copilot/how-tos/copilot-cli)&ensp;
[![Join AI Foundry Discord](https://img.shields.io/badge/Discord-AI_Community-blue?style=flat-square&logo=discord&color=5865f2&logoColor=fff)](https://aka.ms/foundry/discord)

🎯 [What You'll Learn](#what-youll-learn) &ensp; ✅ [Prerequisites](#prerequisites) &ensp; 🤖 [Copilot Family](#understanding-the-github-copilot-family) &ensp; 📚 [Course Structure](#course-structure) &ensp; 📋 [Command Reference](#-github-copilot-cli-command-reference)

# GitHub Copilot CLI 초보자용

> **✨ AI 기반 명령줄 지원으로 개발 워크플로우를 강화하는 방법을 배워보세요.**

GitHub Copilot CLI는 AI 지원 기능을 터미널에서 바로 사용할 수 있도록 해줍니다. 브라우저나 코드 편집기로 전환할 필요 없이 명령줄에서 바로 질문하고, 모든 기능을 갖춘 애플리케이션을 생성하고, 코드를 검토하고, 테스트를 생성하고, 문제를 디버깅할 수 있습니다.

마치 24시간 내내 코드를 분석하고, 복잡한 패턴을 설명해주고, 작업 속도를 높여줄 수 있는 지식이 풍부한 동료가 있는 것과 같다고 생각하시면 됩니다!

이 과정은 다음을 위해 설계되었습니다.:

- 명령줄에서 AI를 사용하려는 **소프트웨어 개발자**
- IDE 통합보다 키보드 기반 워크플로를 선호하는 **터미널 사용자**
- **AI 기반 코드 검토 및 개발 방식을 표준화하려는 팀**

<a href="https://aka.ms/githubcopilotdevdays" target="_blank">
  <picture>
    <img src="./images/copilot-dev-days.png" alt="GitHub Copilot Dev Days - Find or host an event" width="100%" />
  </picture>
</a>

## 🎯 배우게 될 것

이 실습 중심 과정은 GitHub Copilot CLI를 처음 접하는 사람도 바로 활용할 수 있도록 도와줍니다. 모든 챕터에서 하나의 Python 도서 컬렉션 앱을 사용하며, AI 기반 워크플로를 통해 점진적으로 개선해 나갑니다. 과정을 마치면 터미널에서 AI를 활용하여 코드 검토, 테스트 생성, 문제 디버깅, 워크플로 자동화 등을 자신 있게 수행할 수 있게 됩니다.

**AI 경험은 필요 없습니다.** 터미널을 사용할 수 있다면 배울 수 있습니다.

**개발자, 학생, 그리고 소프트웨어 개발 경험이 있는 모든 분들께 완벽함.

## ✅ Prerequisites

시작하기 전에 반드시 확인하세요:

- **GitHub 계정**: [Create one free](https://github.com/signup)<br>
- **GitHub Copilot 접근 권한**: [Free offering](https://github.com/features/copilot/plans), [Monthly subscription](https://github.com/features/copilot/plans), or [Free for students/teachers](https://education.github.com/pack)<br>
- **터미널 기본 사용법**: `cd`, `ls` 명령어 사용 및 명령 실행에 익숙해야 함

## 🤖 GitHub Copilot 가족 이해하기

GitHub Copilot은 이제 AI 기반 도구 모음으로 발전했습니다. 각 도구의 위치는 다음과 같습니다.:

| 제품 | 실행 환경 | 설명 |
|---------|---------------|----------|
| [**GitHub Copilot CLI**](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started)<br>(this course) | Your terminal |  터미널 네이티브 AI 코딩 어시스턴트  |
| [**GitHub Copilot**](https://docs.github.com/copilot) | VS Code, Visual Studio, JetBrains, etc. | 에이전트 모드, 채팅, 인라인 제안  |
| [**Copilot on GitHub.com**](https://github.com/copilot) | GitHub | 저장소에 대한 몰입감 있는 대화, 에이전트 생성 등 |
| [**GitHub Copilot coding agent**](https://docs.github.com/copilot/using-github-copilot/using-copilot-coding-agent-to-work-on-tasks) | GitHub  | 이슈를 에이전트에게 할당하고, PR을 되찾으세요 |

이 과정은 **GitHub Copilot CLI**에 중점을 두어 AI 지원을 직접 터미널로 제공합니다.

## 📚 과정 구조

![GitHub Copilot CLI Learning Path](images/learning-path.png)

| 챕터 | 제목 | 만들게 될 것 |
|:-------:|-------|-------------------|
| 00 | 🚀 [Quick Start](./00-quick-start/README.md) | 설치 및 검증 |
| 01 | 👋 [First Steps](./01-setup-and-first-steps/README.md) | 라이브 데모 + 세 가지 상호작용 모드 |
| 02 | 🔍 [Context and Conversations](./02-context-conversations/README.md) | 다중 파일 프로젝트 분석 |
| 03 | ⚡ [Development Workflows](./03-development-workflows/README.md) | 코드 리뷰, 디버그, 테스트 생성|
| 04 | 🤖 [Create Specialized AI Assistants](./04-agents-custom-instructions/README.md) | 워크플로우에 맞는 맞춤 에이전트 |
| 05 | 🛠️ [Automate Repetitive Tasks](./05-skills/README.md) | 자동으로 로드되는 스킬들 |
| 06 | 🔌 [Connect to GitHub, Databases & APIs](./06-mcp-servers/README.md) | MCP 서버 통합 |
| 07 | 🎯 [Putting It All Together](./07-putting-it-together/README.md) | 완전한 기능 워크플로우 |

## 📖 이 과정의 작동 방식

각 장은 같은 패턴을 따릅니다:

1. **Real-World Analogy**: 친숙한 비교를 통해 개념을 이해하세요
2. **Core Concepts**: 필수 지식을 배우세요
3. **Hands-On Examples**: 실제 명령을 실행해 보고 결과를 확인하세요
4. **Assignment**: 배운 것을 연습하세요
5. **What's Next**: 다음 장 미리보기

**코드 예제는 실행 가능합니다.** 이 강좌의 모든 Copilot 텍스트 블록은 복사하여 터미널에서 실행할 수 있습니다.

## 📋 GitHub Copilot CLI 명령어 참조

**[GitHub Copilot CLI 명령어 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)**는 Copilot CLI를 효과적으로 사용하는 데 도움이 되는 명령어와 단축키를 찾는 데 유용합니다.

## 🙋 도움 요청

- 🐛 **버그를 발견하셨나요?** [이슈 제출](https://github.com/github/copilot-cli-for-beginners/issues)
- 🤝 **기여하고 싶으신가요?** PR을 환영합니다!

- 📚 **공식 문서:** [GitHub Copilot CLI 문서](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)

## 라이선스

이 프로젝트는 MIT 오픈 소스 라이선스 조건에 따라 배포됩니다. 전체 조건은 [LICENSE](./LICENSE) 파일을 참조하십시오.

