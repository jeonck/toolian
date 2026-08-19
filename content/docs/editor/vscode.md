---
weight: 4010
title: "VS Code"
description: "가장 무난한 기본값. 처음 설치하고 30분 안에 손봐야 할 설정과 확장."
icon: "code"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

VS Code는 언어를 가리지 않고 확장으로 채워 쓰는 에디터입니다. 팀원과 설정을
공유하기 쉽고, 대부분의 튜토리얼이 이것을 기준으로 쓰여 있어 첫 에디터로
적합합니다.

## 설치

```bash
brew install --cask visual-studio-code
winget install Microsoft.VisualStudioCode
```

터미널에서 `code .`을 쓰려면 명령 팔레트(`⌘⇧P` / `Ctrl+Shift+P`)에서
`Shell Command: Install 'code' command in PATH`를 실행합니다.

## 반드시 익힐 단축키 여섯 개

| 동작 | macOS | Windows/Linux |
|---|---|---|
| 명령 팔레트 | `⌘⇧P` | `Ctrl+Shift+P` |
| 파일 빠른 열기 | `⌘P` | `Ctrl+P` |
| 심볼로 이동 | `⌘⇧O` | `Ctrl+Shift+O` |
| 전체 검색 | `⌘⇧F` | `Ctrl+Shift+F` |
| 멀티 커서 (같은 단어) | `⌘D` 반복 | `Ctrl+D` |
| 줄 이동 | `⌥↑` / `⌥↓` | `Alt+↑` / `Alt+↓` |

명령 팔레트 하나만 알아도 나머지 기능은 이름으로 찾아 쓸 수 있습니다.

## 처음 손볼 설정

`⌘⇧P → Preferences: Open User Settings (JSON)`:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": { "source.fixAll": "explicit" },
  "editor.rulers": [100],
  "editor.bracketPairColorization.enabled": true,
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "explorer.compactFolders": false,
  "workbench.editor.enablePreview": false,
  "terminal.integrated.fontFamily": "JetBrainsMono Nerd Font"
}
```

- `formatOnSave`는 리뷰에서 포맷 지적이 사라지게 하는 가장 큰 한 방입니다.
- `enablePreview: false`는 파일을 클릭할 때마다 탭이 바뀌는 동작을 끕니다.

## 확장은 최소로

| 확장 | 용도 |
|---|---|
| **ESLint / Prettier** | JS·TS 린트와 포맷 |
| **Python (Microsoft)** | 파이썬 언어 서버, 디버거 |
| **Error Lens** | 에러를 해당 줄에 인라인 표시 |
| **GitLens** | 줄 단위 blame, 히스토리 |
| **EditorConfig** | 팀 공통 들여쓰기 규칙 적용 |
| **Docker** | Dockerfile·컨테이너 관리 |

확장이 많아질수록 시작이 느려집니다. `Developer: Show Running Extensions`로
주기적으로 확인하고, 안 쓰는 건 지웁니다.

## 팀 설정 공유하기

프로젝트 루트에 `.vscode/settings.json`과 `.vscode/extensions.json`을 커밋하면,
팀원이 저장소를 열 때 같은 포맷 규칙과 확장 추천을 받습니다.

```json
// .vscode/extensions.json
{ "recommendations": ["dbaeumer.vscode-eslint", "esbenp.prettier-vscode"] }
```

## 원격 개발

`Remote - SSH` 확장을 쓰면 서버의 코드를 로컬 에디터처럼 편집합니다. 컨테이너
안에서 개발하려면 `Dev Containers` 확장과 `.devcontainer/devcontainer.json`을
사용합니다. 환경 차이로 생기는 문제를 크게 줄여줍니다.

## 다음 단계

키보드만으로 편집하고 싶다면 → [Neovim](/docs/editor/neovim/)
