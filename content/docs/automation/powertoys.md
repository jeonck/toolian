---
weight: 9020
title: "PowerToys (Windows)"
description: "마이크로소프트가 만든 무료 유틸리티 모음. 창 배치, 런처, 키 재매핑, 텍스트 추출."
icon: "widgets"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

PowerToys는 여러 개의 작은 유틸리티를 한 앱에 모아둔 오픈소스 도구입니다. 필요한
모듈만 켜서 쓰면 됩니다.

## 설치

```powershell
winget install Microsoft.PowerToys
```

## 켜둘 만한 모듈

| 모듈 | 하는 일 |
|---|---|
| **FancyZones** | 화면을 원하는 격자로 나누고 창을 스냅 |
| **PowerToys Run** | `Alt+Space` 런처. 앱·파일·계산기·명령 |
| **Keyboard Manager** | 키와 단축키 재매핑 |
| **Text Extractor** | 화면 어디서나 OCR로 글자 추출 (`Win+Shift+T`) |
| **PowerRename** | 정규식 기반 대량 파일명 변경 |
| **Always On Top** | 창 고정 (`Win+Ctrl+T`) |
| **Color Picker** | 화면 색 추출 (`Win+Shift+C`) |
| **Awake** | 절전 모드 임시 방지 |

## FancyZones

`Win+Shift+\``로 편집기를 열어 레이아웃을 만들고, 창을 드래그하면서 `Shift`를
누르면 구역에 붙습니다. 와이드 모니터에서 창 세 개를 3:5:4 비율로 고정해 두면
드래그로 크기를 맞추는 일이 사라집니다.

설정에서 "창을 방향키로 구역 간 이동"을 켜면 `Win+방향키`로도 이동합니다.

## Keyboard Manager

가장 흔한 활용은 `CapsLock`을 다른 키로 바꾸는 것입니다.

| 원래 키 | 바꿀 키 | 이유 |
|---|---|---|
| CapsLock | Ctrl | 손목 부담 감소, Vim 사용자에게 유용 |
| CapsLock | Esc | Vim/Neovim 사용 시 |
| Insert | (없음) | 실수로 덮어쓰기 모드 진입 방지 |

앱별 단축키 재매핑도 지원하므로, 특정 프로그램에서만 다른 키를 쓰게 할 수 있습니다.

## PowerRename

파일 여러 개를 선택하고 우클릭 → PowerRename.

| 찾기 | 바꾸기 | 결과 |
|---|---|---|
| `IMG_(\d+)` | `photo_$1` | `IMG_0231.jpg` → `photo_0231.jpg` |
| ` ` | `_` | 공백을 밑줄로 |
| `(.*)\.jpeg` | `$1.jpg` | 확장자 통일 |

정규식 체크박스를 켜야 캡처 그룹(`$1`)이 동작합니다. 미리보기에서 결과를 확인한
뒤 적용하세요.

## Text Extractor

이미지나 영상 속 코드·에러 메시지를 손으로 옮겨 적을 필요가 없습니다.
`Win+Shift+T` → 영역 드래그 → 클립보드에 텍스트가 들어옵니다. 스크린샷으로 온
로그를 다룰 때 특히 유용합니다.

## WSL과 함께

Windows에서 리눅스 CLI 도구를 쓰려면 WSL이 가장 편합니다.

```powershell
wsl --install -d Ubuntu
```

설치 후 Windows Terminal에서 Ubuntu 탭을 열고, 이 사이트의 리눅스 설치 명령을
그대로 쓸 수 있습니다. 파일은 `\\wsl$\Ubuntu\home\사용자`로 탐색기에서 접근됩니다.

## 다음 단계

프로젝트 명령을 한곳에 모으려면 → [Make와 Makefile](/docs/automation/make/)
