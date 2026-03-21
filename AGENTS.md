# AGENTS.md

GitHub Copilot CLI를 배우는 초보자 친화적인 강좌입니다. 소프트웨어가 아닌 교육 콘텐츠입니다.

## 구조

| 경로 | 목적 |
|------|---------|
| `00-07/` | 목차: 비유 → 개념 → 실습 → 과제 → 다음 단계 |
| `samples/book-app-project/` | **주요 예제**: 모든 장에서 사용되는 Python CLI 기반 도서 수집 앱 |
| `samples/book-app-project-cs/` | 도서 수집 앱의 C# 버전 |
| `samples/book-app-project-js/` | 도서 수집 앱의 JavaScript 버전 |
| `samples/book-app-buggy/` | 디버깅 연습을 위한 **의도적인 버그** (3장) |
| `samples/agents/` | 에이전트 템플릿 예제 (python-reviewer, pytest-helper, hello-world) |
| `samples/skills/` | 스킬 템플릿 예제 (코드 체크리스트, pytest 생성, 커밋 메시지, hello-world) |
| `samples/mcp-configs/` | MCP 서버 구성 예제 |
| `samples/buggy-code/` | **선택 사항**: 보안에 초점을 맞춘 버그 코드 (JS 및 Python) |
| `samples/src/` | **선택 사항**: 이전 강좌 버전의 레거시 JS/React 샘플 |
| `appendices/` | 추가 참고 자료 |

## 해야 할 일

- 설명은 초보자가 이해하기 쉽게 작성하세요. AI/ML 용어 사용 시 설명
- bash 예제는 복사 붙여넣기 가능하도록 작성
- 어조: 친근하고, 격려적이며, 실용적
- 모든 주요 예제에 `samples/book-app-project/` 경로 사용
- 코드 예제에 Python/pytest 컨텍스트 사용

## 하지 말아야 할 것

- `samples/book-app-buggy/` 또는 `samples/buggy-code/`의 버그 수정 - 의도적인 버그입니다
- README.md의 코스 표를 업데이트하지 않고 챕터 추가
- 독자가 AI/ML 용어를 알고 있다고 가정

## 빌드

```bash
npm install && npm run release
```
