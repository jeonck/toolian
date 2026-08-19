---
weight: 2010
title: "터미널 앱 고르기"
description: "iTerm2, Windows Terminal, Ghostty, Warp — 무엇이 다르고 무엇을 먼저 설정할지."
icon: "web_asset"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

터미널 앱은 셸(zsh, bash)을 담는 창입니다. 창이 좋아진다고 명령이 빨라지진
않지만, 탭·분할·검색·복사가 편해지면 흐름이 끊기지 않습니다.

## 선택지 비교

| 앱 | 플랫폼 | 특징 |
|---|---|---|
| **iTerm2** | macOS | 오래된 표준. 분할, 검색, 세션 복원, 트리거 등 기능이 가장 많음 |
| **Ghostty** | macOS/Linux | GPU 가속으로 매우 빠름. 설정이 텍스트 파일 하나 |
| **Windows Terminal** | Windows | 탭·분할 지원. WSL·PowerShell·cmd를 한 창에서 |
| **Warp** | macOS/Linux/Windows | 블록 단위 출력, AI 명령 제안. 계정 로그인 필요 |
| **기본 터미널** | 전 플랫폼 | 가볍고 어디에나 있음. 분할·검색이 아쉬움 |

처음이라면 macOS는 iTerm2 또는 Ghostty, Windows는 Windows Terminal이 무난합니다.

## 설치

```bash
# macOS
brew install --cask iterm2
brew install --cask ghostty
```

```powershell
# Windows (보통 이미 설치되어 있음)
winget install Microsoft.WindowsTerminal
```

## 설치 직후 손볼 것 네 가지

1. **글꼴**: 프롬프트 아이콘이 깨지지 않도록 Nerd Font 계열을 씁니다.
   ```bash
   brew install --cask font-jetbrains-mono-nerd-font
   ```
   앱 설정의 Font에서 `JetBrainsMono Nerd Font`를 선택합니다.
2. **스크롤백**: 기본값이 작으면 긴 로그가 잘립니다. 10,000줄 이상으로 늘립니다.
3. **키 반복 속도**: macOS `시스템 설정 → 키보드`에서 반복 속도를 최대, 반복 지연을
   최소로 두면 커서 이동이 확연히 빨라집니다.
4. **분할 단축키 익히기**: iTerm2는 `⌘D`(세로) / `⌘⇧D`(가로),
   Windows Terminal은 `Alt+Shift++` / `Alt+Shift+-`.

## 어디서나 통하는 단축키

| 동작 | macOS | Windows/Linux |
|---|---|---|
| 줄 처음/끝으로 | `Ctrl+A` / `Ctrl+E` | 동일 |
| 커서 앞/뒤 지우기 | `Ctrl+U` / `Ctrl+K` | 동일 |
| 단어 단위 삭제 | `Ctrl+W` | 동일 |
| 실행 중 명령 중단 | `Ctrl+C` | 동일 |
| 화면 지우기 | `Ctrl+L` | 동일 |
| 히스토리 검색 | `Ctrl+R` | 동일 |

이 여섯 개만 손에 붙어도 방향키를 누르는 시간이 사라집니다.

## 다음 단계

창을 정했으면 그 안의 프롬프트를 정리할 차례입니다 →
[zsh와 Starship 프롬프트](/docs/terminal/zsh-starship/)
