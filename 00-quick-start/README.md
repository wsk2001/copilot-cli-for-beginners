![Chapter 00: Quick Start](images/chapter-header.png)

환영합니다! 이 장에서는 GitHub Copilot CLI(명령줄 인터페이스)를 설치하고, GitHub 계정으로 로그인하고, 모든 기능이 정상적으로 작동하는지 확인합니다. 이 장은 간단한 설정 과정입니다. 설정이 완료되면 1장에서 본격적인 데모를 시작하세요!

## 🎯 Learning Objectives

이 장을 마치면 다음을 완료하게 됩니다.

- GitHub Copilot CLI 설치 완료
- GitHub 계정으로 로그인 완료
- 간단한 테스트를 통해 정상 작동 확인 완료

> ⏱️ **소요 시간**: 약 10분 (읽기 5분 + 실습 5분)
---

## ✅ 필수 조건

- Copilot 접근 권한이 있는 **GitHub 계정**. [구독 옵션 참조](https://github.com/features/copilot/plans). 학생/교사는 GitHub Education을 통해 Copilot Pro를 무료로 이용할 수 있습니다.](https://education.github.com/pack).

- **터미널 기본 사용법**: `cd`, `ls` 등의 명령어를 능숙하게 사용할 수 있어야 합니다.

### "Copilot 액세스"의 의미

GitHub Copilot CLI를 사용하려면 활성 Copilot 구독이 필요합니다. [github.com/settings/copilot](https://github.com/settings/copilot)에서 구독 상태를 확인할 수 있습니다. 다음 중 하나가 표시되어야 합니다.

- **Copilot Individual** - 개인 구독
- **Copilot Business** - 조직을 통한 구독
- **Copilot Enterprise** - 기업을 통한 구독
- **GitHub Education** - 인증된 학생/교사를 위한 무료 구독

"GitHub Copilot에 대한 액세스 권한이 없습니다."라는 메시지가 표시되면 무료 옵션을 사용하거나, 구독 플랜에 가입하거나, 액세스 권한을 제공하는 조직에 가입해야 합니다.

---

## 설치

> ⏱️ **소요 시간**: 설치는 2~5분 정도 소요됩니다. 인증에 1~2분이 추가됩니다.

### 권장: GitHub Codespaces (설치 필요 없음)

사전 요구 사항을 설치하고 싶지 않다면 GitHub Codespaces를 사용하세요. GitHub Copilot CLI가 바로 사용 가능하며(로그인 필요), Python 3.13, pytest, GitHub CLI가 사전 설치되어 있습니다.

1. [이 저장소를 포크](https://github.com/github/copilot-cli-for-beginners/fork)하여 GitHub 계정에 복사하세요.
2. **코드** > **코드스페이스** > **main에 코드스페이스 생성**을 선택하세요.
3. 컨테이너가 생성될 때까지 몇 분 정도 기다리세요.
4. 이제 사용할 준비가 되었습니다! 터미널이 코드스페이스 환경에서 자동으로 열립니다.

💡 **코드스페이스에서 확인하세요**: `cd samples/book-app-project && python book_app.py help` 명령어를 실행하여 Python과 샘플 앱이 정상적으로 작동하는지 확인하세요.

### 대안: 로컬 설치

> 💡 **어떤 방법을 선택해야 할지 모르시겠나요?** Node.js가 설치되어 있다면 `npm`을 사용하세요. 그렇지 않다면 시스템에 맞는 옵션을 선택하세요.

> 💡 **데모 실행을 위해 Python이 필요합니다.** 이 과정에서는 Python 샘플 앱을 사용합니다. 로컬에서 작업하는 경우 데모를 시작하기 전에 [Python 3.10 이상](https://www.python.org/downloads/)을 설치하세요.

> **참고:** 이 과정 전반에 걸쳐 보여지는 주요 예제는 Python(`samples/book-app-project`)을 사용하지만, JavaScript(`samples/book-app-project-js`) 및 C#(`samples/book-app-project-cs`) 버전도 제공되므로 해당 언어를 선호하는 경우 사용할 수 있습니다. 각 샘플에는 해당 언어로 앱을 실행하는 방법에 대한 지침이 포함된 README 파일이 있습니다.


시스템에 맞는 방법을 선택하세요.

### 모든 플랫폼 (npm)

```bash
# Node.js가 설치되어 있다면, 다음 명령어를 사용하여 CLI를 빠르게 설치할 수 있습니다.
npm install -g @github/copilot
```

### macOS/Linux (Homebrew)

```bash
brew install copilot-cli
```

### Windows (WinGet)

```bash
winget install GitHub.Copilot
```

### macOS/Linux (설치 스크립트)

```bash
curl -fsSL https://gh.io/copilot-install | bash
```
---

## 인증

`copilot-cli-for-beginners` 저장소의 루트 디렉토리에서 터미널 창을 열고, CLI를 실행한 다음 해당 폴더에 대한 접근을 허용하세요.

```bash
copilot
```

저장소가 포함된 폴더를 신뢰할지 묻는 메시지가 나타납니다(이미 신뢰하고 있지 않은 경우). 한 번만 신뢰하거나 향후 모든 세션에서 신뢰할 수 있도록 설정할 수 있습니다.

<img src="images/copilot-trust.png" alt="Copilot CLI를 사용하여 폴더 내 파일 신뢰" width="800"/>

폴더를 신뢰한 후 GitHub 계정으로 로그인할 수 있습니다.

```
> /login
```

**다음 단계:**

1. Copilot CLI에 일회용 인증 코드(예: `ABCD-1234`)가 표시됩니다.
2. 브라우저에 GitHub 기기 인증 페이지가 열립니다. GitHub에 로그인하지 않은 경우 로그인하세요.

3. 메시지가 나타나면 코드를 입력하세요.
4. "승인"을 선택하여 GitHub Copilot CLI에 액세스 권한을 부여하세요.
5. 터미널로 돌아가세요. 이제 로그인되었습니다!

<img src="images/auth-device-flow.png" alt="기기 인증 흐름 - 터미널 로그인부터 로그인 확인까지 5단계 프로세스" width="800"/>

*기기 인증 흐름: 터미널에서 코드가 생성되고, 브라우저에서 코드를 확인하면 Copilot CLI가 인증됩니다.*

**팁**: 로그인은 세션 간에 유지됩니다. 토큰이 만료되거나 명시적으로 로그아웃하지 않는 한 이 과정은 한 번만 수행하면 됩니다.

---

## 작동 확인

### 1단계: Copilot CLI 테스트

로그인하셨으니 이제 Copilot CLI가 제대로 작동하는지 확인해 보겠습니다. 터미널에서 CLI를 실행하세요(아직 실행하지 않았다면):

```bash
> Say hello and tell me what you can help with
```

응답을 받으면 CLI를 종료할 수 있습니다:

```bash
> /exit
```
---

<details>
<summary>🎬 실제로 그 모습을 직접 보자!</summary>

![Hello Demo](images/hello-demo.gif)

*데모 결과는 다를 수 있습니다. 사용하시는 모델, 도구 및 응답은 여기에 표시된 내용과 다를 수 있습니다.*

</details>

---

**예상 출력**: Copilot CLI의 기능을 나열하는 친절한 응답.

### 2단계: 샘플 책 앱 실행

본 과정에서는 CLI를 사용하여 탐색하고 개선할 샘플 앱을 제공합니다(코드는 /samples/book-app-project에서 확인할 수 있습니다). 시작하기 전에 *Python 책 모음 터미널 앱*이 제대로 작동하는지 확인하세요. 시스템에 따라 `python` 또는 `python3`을 실행하세요.

> **참고:** 본 과정에서 주로 사용되는 예제는 Python(`samples/book-app-project`)을 사용하지만, JavaScript(`samples/book-app-project-js`) 및 C#(`samples/book-app-project-cs`) 버전도 제공되므로 해당 언어를 선호하는 경우 사용할 수 있습니다. 각 샘플에는 해당 언어로 앱을 실행하는 방법에 대한 지침이 포함된 README 파일이 있습니다.

```bash
cd samples/book-app-project
python book_app.py list
```

**예상 출력**: "호빗", "1984", "듄"을 포함한 5권의 책 목록

### 3단계: Copilot CLI를 사용하여 책 앱 실행하기

먼저 저장소 루트로 이동합니다(2단계를 실행한 경우):

```bash
cd ../.. # 필요한 경우 저장소 루트로 이동
copilot
> @samples/book-app-project/book_app.py는 무엇을 하는 파일인가요?

``

**예상 출력**: 책 앱의 주요 기능 및 명령 요약

오류가 발생하면 아래 [문제 해결 섹션](#troubleshooting)을 참조하세요.

완료되면 Copilot CLI를 종료할 수 있습니다.

```bash
> /exit
```

---

## ✅ 준비 완료!

설치는 이제 끝입니다. 본격적인 재미는 1장에서 시작됩니다. 1장에서는 다음과 같은 내용을 배우게 됩니다.

- AI가 책 앱을 검토하고 코드 품질 문제를 즉시 찾아내는 과정을 지켜보세요.
- Copilot CLI를 사용하는 세 가지 방법을 알아보세요.
- 일반 영어 코드로 작동하는 코드를 생성해 보세요.

**[1장: 첫 단계로 이동 →](../01-setup-and-first-steps/README.md)**

---

## 문제 해결

### "copilot: 명령어를 찾을 수 없습니다"

CLI가 설치되지 않았습니다. 다른 설치 방법을 시도해 보세요.

```bash
# brew 설치가 실패하면 npm을 사용해 보세요.
npm install -g @github/copilot

# 또는 설치 스크립트를 사용해 보세요.
curl -fsSL https://gh.io/copilot-install | bash
```

### "GitHub Copilot에 액세스할 수 없습니다."

1. [github.com/settings/copilot](https://github.com/settings/copilot)에서 Copilot 구독이 있는지 확인하세요.
2. 회사 계정을 사용하는 경우 조직에서 CLI 액세스를 허용했는지 확인하세요.

### "인증 실패"

다시 인증하세요.

```bash
copilot
> /login
```

### 브라우저가 자동으로 열리지 않음

[github.com/login/device](https://github.com/login/device)에 수동으로 접속하여 터미널에 표시된 코드를 입력하세요.

### 토큰 만료

`/login`을 다시 실행하세요.

```bash
copilot
> /login
```

### 여전히 막혔나요?

- [GitHub Copilot CLI 문서](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)를 확인하세요.
- [GitHub 이슈](https://github.com/github/copilot-cli/issues)를 검색하세요.

---

## 🔑 주요 내용

1. **GitHub Codespace는 빠르게 시작할 수 있는 방법입니다.** - Python, pytest, GitHub Copilot CLI가 모두 사전 설치되어 있으므로 데모를 바로 사용할 수 있습니다.
2. **다양한 설치 방법** - 시스템에 맞는 방법(Homebrew, WinGet, npm 또는 설치 스크립트)을 선택하세요.
3. **일회성 인증** - 토큰이 만료될 때까지 로그인이 유지됩니다.
4. **책 앱이 작동합니다.** - 전체 과정에서 `samples/book-app-project`를 사용하게 됩니다.

> 📚 **공식 문서**: [Copilot 설치] [CLI](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started)에서 설치 옵션 및 요구 사항을 확인하세요.

> 📋 **빠른 참조**: 전체 명령 및 단축키 목록은 [GitHub Copilot CLI 명령 참조](https://docs.github.com/en/copilot/reference/cli-command-reference)를 참조하세요.

---

**[1장: 시작하기 →](../01-setup-and-first-steps/README.md)**
