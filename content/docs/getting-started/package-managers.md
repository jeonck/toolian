---
weight: 1020
title: "패키지 매니저로 설치하기"
description: "Homebrew, winget, apt로 CLI 도구를 설치·업데이트·제거하는 최소 명령."
icon: "inventory_2"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

이후 문서의 설치 명령은 전부 패키지 매니저를 전제로 합니다. 웹사이트에서 설치
파일을 내려받는 방식은 업데이트와 제거가 번거롭고, 어떤 도구를 깔았는지 목록으로
남지 않습니다.

## macOS — Homebrew

설치는 한 줄입니다.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

| 목적 | 명령 |
|---|---|
| CLI 도구 설치 | `brew install ripgrep` |
| GUI 앱 설치 | `brew install --cask visual-studio-code` |
| 검색 | `brew search fzf` |
| 정보 보기 | `brew info jq` |
| 전체 업데이트 | `brew update && brew upgrade` |
| 제거 | `brew uninstall ripgrep` |
| 설치 목록 | `brew leaves` (직접 설치한 것만) |

## Windows — winget

Windows 10 이후 기본 포함입니다. 없으면 Microsoft Store에서 "앱 설치 관리자"를
설치합니다.

```powershell
winget search ripgrep
winget install BurntSushi.ripgrep.MSVC
winget upgrade --all
winget uninstall BurntSushi.ripgrep.MSVC
```

개발자 도구가 더 많은 저장소를 원하면 [Scoop](https://scoop.sh/)을 함께 씁니다.

```powershell
irm get.scoop.sh | iex
scoop install ripgrep fd bat
```

## Linux — apt / dnf

```bash
sudo apt update && sudo apt install ripgrep fd-find bat
sudo dnf install ripgrep fd-find bat
```

배포판 저장소는 버전이 오래된 경우가 있습니다. 최신이 필요하면 각 도구의 GitHub
릴리스에서 `.deb`/`.rpm`을 받거나, macOS·Linux 공통으로 Homebrew를 쓸 수 있습니다.

## 설치 목록을 파일로 남기기

새 노트북을 받았을 때 한 번에 복구하려면 목록을 저장해 둡니다.

```bash
# 저장
brew leaves > ~/dotfiles/brew-list.txt
brew list --cask > ~/dotfiles/brew-cask-list.txt

# 복구
xargs brew install < ~/dotfiles/brew-list.txt
```

Homebrew는 `Brewfile`이라는 전용 포맷도 지원합니다.

```bash
brew bundle dump --file=~/dotfiles/Brewfile --force
brew bundle install --file=~/dotfiles/Brewfile
```

## 다음 단계

설치 목록까지 파일로 남겼다면, 이제 설정 파일 자체를 관리할 차례입니다 →
[설정을 dotfiles로 관리하기](/docs/getting-started/dotfiles/)
