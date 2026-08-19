---
weight: 9010
title: "Raycast (macOS)"
description: "Spotlight를 대체하는 런처. 앱 실행·클립보드 히스토리·창 관리·스니펫을 한 키로."
icon: "rocket_launch"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Raycast는 `⌘Space`를 눌러 열고 몇 글자만 입력해 원하는 동작을 실행하는 런처입니다.
앱 실행만 하는 것이 아니라, **손이 마우스로 가는 상황을 대부분 없애줍니다.**

## 설치

```bash
brew install --cask raycast
```

설치 후 Spotlight 단축키를 `⌘Space`에서 해제하고(시스템 설정 → 키보드 →
단축키 → Spotlight), Raycast에 할당합니다.

## 설정 직후 켜야 할 기능 네 가지

| 기능 | 왜 |
|---|---|
| **Clipboard History** | 복사한 내용을 되돌아가 꺼낼 수 있음. 체감 1위 |
| **Window Management** | 창을 반쪽·1/4로 배치. 별도 앱 불필요 |
| **Snippets** | 자주 쓰는 문장·이메일 주소를 짧은 키워드로 |
| **Quicklinks** | 자주 여는 URL을 파라미터와 함께 |

Clipboard History는 `⌥⌘C` 같은 단축키를 직접 지정해 두면 바로 쓸 수 있습니다.

## 스니펫 예시

| 키워드 | 확장 결과 |
|---|---|
| `;email` | 본인 이메일 주소 |
| `;date` | `{date}` 오늘 날짜 자동 삽입 |
| `;sig` | 메일 서명 블록 |
| `;pr` | PR 설명 템플릿 |

## Quicklink 예시

URL에 `{query}`를 넣으면 입력값이 치환됩니다.

```
https://github.com/search?q={query}&type=code
https://translate.google.com/?text={query}
jira://browse/{query}
```

## 확장(Store)

Raycast Store에서 설치합니다. 실무에서 자주 쓰이는 것들:

- **GitHub** — PR·이슈 검색과 상태 확인
- **Homebrew** — 패키지 검색·설치
- **Kill Process** — 포트를 점유한 프로세스 종료
- **Color Picker** — 화면 어디서나 색 추출
- **Search Emoji** — 이모지 검색

## 스크립트 명령

직접 만든 셸 스크립트를 Raycast 명령으로 등록할 수 있습니다.

```bash
#!/bin/bash
# @raycast.schemaVersion 1
# @raycast.title 개발 서버 재시작
# @raycast.mode compact
# @raycast.packageName 프로젝트

cd ~/projects/my-app && docker compose restart app
```

파일에 실행 권한을 주고(`chmod +x`) Raycast 설정의 Script Commands에 폴더를
등록하면 목록에 나타납니다.

## Windows·Linux 대안

| OS | 도구 |
|---|---|
| Windows | PowerToys Run, Flow Launcher |
| Linux | Ulauncher, Albert, rofi |

## 다음 단계

Windows 환경이라면 → [PowerToys (Windows)](/docs/automation/powertoys/)
