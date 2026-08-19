---
weight: 4030
title: "JetBrains IDE"
description: "IntelliJ·PyCharm·GoLand의 강점인 리팩터링과 디버깅을 최대한 활용하기."
icon: "build"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

JetBrains 계열 IDE는 코드를 텍스트가 아니라 **구조**로 이해합니다. 그래서 이름
변경, 메서드 추출, 사용처 추적 같은 작업이 정확합니다. 규모가 큰 프로젝트나
정적 타입 언어에서 특히 유리합니다.

## 제품 고르기

| 제품 | 주 언어 |
|---|---|
| IntelliJ IDEA | Java, Kotlin, Scala |
| PyCharm | Python |
| GoLand | Go |
| WebStorm | JavaScript, TypeScript |
| DataGrip | SQL, 데이터베이스 |
| Rider | .NET, C# |

여러 언어를 오간다면 IntelliJ IDEA Ultimate 하나로 플러그인을 통해 대부분
커버됩니다. 설치와 버전 관리는 **JetBrains Toolbox**로 하는 편이 편합니다.

```bash
brew install --cask jetbrains-toolbox
```

## 핵심 단축키

| 동작 | macOS | Windows/Linux |
|---|---|---|
| 무엇이든 검색 | `Shift` 두 번 | `Shift` 두 번 |
| 액션 검색 | `⌘⇧A` | `Ctrl+Shift+A` |
| 정의로 이동 | `⌘B` | `Ctrl+B` |
| 사용처 찾기 | `⌥F7` | `Alt+F7` |
| 이름 변경 | `⇧F6` | `Shift+F6` |
| 리팩터링 메뉴 | `⌃T` | `Ctrl+Alt+Shift+T` |
| 자동 수정 | `⌥⏎` | `Alt+Enter` |
| 최근 파일 | `⌘E` | `Ctrl+E` |

`⌥⏎`(Alt+Enter)는 IDE가 감지한 문제를 그 자리에서 고쳐주는 만능 키입니다.
빨간 줄이나 노란 줄이 보이면 일단 눌러보세요.

## 리팩터링 활용

| 리팩터링 | 언제 |
|---|---|
| Rename | 이름이 하는 일과 안 맞을 때 (참조 전부 자동 수정) |
| Extract Method | 함수가 화면을 넘어갈 때 |
| Extract Variable | 조건식이 길어 읽기 어려울 때 |
| Change Signature | 파라미터 추가·순서 변경 (호출부 자동 수정) |
| Inline | 한 번만 쓰이는 변수·함수를 없앨 때 |

찾아 바꾸기(`sed`)와 달리 문자열이나 주석 안의 같은 단어는 건드리지 않습니다.

## 디버거

브레이크포인트를 찍고 실행하면 변수 상태를 그대로 볼 수 있습니다. 자주 쓰는 기능:

- **조건부 브레이크포인트**: 브레이크포인트 우클릭 → `Condition`에 `i == 42`
- **Evaluate Expression** (`⌥F8`): 멈춘 상태에서 임의의 코드 실행
- **Watch**: 특정 식을 계속 관찰
- **Drop Frame**: 방금 들어간 함수 호출을 되돌려 다시 실행

`print` 디버깅보다 대체로 빠릅니다.

## 무거울 때

- `Help → Change Memory Settings`에서 힙을 2~4GB로 올립니다.
- 인덱싱에서 빌드 산출물 폴더를 제외합니다 (우클릭 → `Mark Directory as →
  Excluded`).
- 안 쓰는 플러그인을 끕니다. 기본 번들 플러그인 중에도 안 쓰는 것이 많습니다.

## 다음 단계

에디터를 무엇을 쓰든 통하는 기술로 마무리합니다 →
[에디터 공통 편집 기술](/docs/editor/editing-skills/)
