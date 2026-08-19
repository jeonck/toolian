---
weight: 1030
title: "설정을 dotfiles로 관리하기"
description: "셸·에디터·Git 설정을 Git 저장소 하나로 묶어 어느 컴퓨터에서든 복구하기."
icon: "settings_backup_restore"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`.zshrc`, `.gitconfig`, VS Code 설정처럼 이름이 점(.)으로 시작하는 설정 파일을
dotfiles라고 부릅니다. 이것들을 Git 저장소 하나에 모아두면 새 장비 세팅이 몇 분
만에 끝나고, "예전에 잘 되던 설정"을 되돌릴 수 있습니다.

## 가장 단순한 방법: 저장소 + 심볼릭 링크

```bash
mkdir -p ~/dotfiles && cd ~/dotfiles
git init

# 실제 파일을 저장소로 옮기고, 원래 위치에는 링크만 남긴다
mv ~/.zshrc ~/dotfiles/zshrc
ln -s ~/dotfiles/zshrc ~/.zshrc

mv ~/.gitconfig ~/dotfiles/gitconfig
ln -s ~/dotfiles/gitconfig ~/.gitconfig

git add -A && git commit -m "dotfiles 시작"
```

새 컴퓨터에서는 저장소를 클론하고 링크만 다시 걸면 됩니다.

```bash
git clone https://github.com/<사용자명>/dotfiles ~/dotfiles
ln -sf ~/dotfiles/zshrc ~/.zshrc
ln -sf ~/dotfiles/gitconfig ~/.gitconfig
```

## 링크 걸기를 자동화: GNU Stow

파일이 늘어나면 링크를 손으로 거는 것도 일입니다. `stow`는 디렉터리 구조를 그대로
홈에 링크로 펼쳐줍니다.

```bash
brew install stow

# ~/dotfiles/zsh/.zshrc 구조로 정리한 뒤
cd ~/dotfiles && stow zsh git nvim
```

`stow -D zsh`로 되돌릴 수 있습니다.

## 무엇을 넣고 무엇을 빼는가

| 넣는다 | 뺀다 |
|---|---|
| `.zshrc`, `.bashrc`, `.profile` | SSH 개인키, `.aws/credentials` |
| `.gitconfig` (사용자 정보 제외) | 토큰이 들어간 `.npmrc`, `.netrc` |
| 에디터 설정·키맵 | 캐시, 히스토리 파일 |
| `Brewfile`, 패키지 목록 | 회사 전용 내부 URL |

비밀 정보는 저장소에 절대 넣지 않습니다. 실수를 막으려면 `.gitignore`에 미리
패턴을 넣어 두세요.

```
*.pem
*_rsa
.env
credentials
```

## 컴퓨터마다 다른 설정 분리하기

회사 장비와 개인 장비의 Git 이메일이 다르다면, 공용 설정에서 로컬 파일을
불러오도록 합니다.

```bash
# ~/dotfiles/gitconfig
[include]
    path = ~/.gitconfig.local
```

`~/.gitconfig.local`은 저장소에 넣지 않고 장비마다 따로 둡니다.

## 다음 단계

준비가 끝났습니다. 이제 [터미널 & 셸](/docs/terminal/)부터 실제 도구를 하나씩
얹어 봅니다.
