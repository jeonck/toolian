---
weight: 2020
title: "zsh와 Starship 프롬프트"
description: "자동완성·히스토리 설정과, 브랜치·언어 버전을 프롬프트에 띄우는 Starship 설정."
icon: "chevron_right"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

프롬프트는 "지금 어디서 무엇을 하고 있는가"를 보여주는 상태 표시줄입니다. 현재
브랜치나 파이썬 가상환경을 눈으로 확인할 수 있으면 `git branch`, `which python`을
치는 횟수가 확 줄어듭니다.

## zsh 기본 설정

macOS는 zsh가 기본 셸입니다. Linux에서 바꾸려면:

```bash
sudo apt install zsh
chsh -s $(which zsh)
```

`~/.zshrc`에 다음을 넣어두면 체감이 큽니다.

```bash
# 히스토리를 넉넉히, 중복 없이, 여러 창에서 공유
HISTSIZE=50000
SAVEHIST=50000
setopt SHARE_HISTORY HIST_IGNORE_ALL_DUPS HIST_REDUCE_BLANKS

# cd 없이 디렉터리명만 입력해도 이동
setopt AUTO_CD

# 대소문자 구분 없는 탭 자동완성
autoload -Uz compinit && compinit
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}'
```

## 플러그인 두 개면 충분

무거운 프레임워크 없이도 체감 효과가 가장 큰 둘만 설치합니다.

```bash
brew install zsh-autosuggestions zsh-syntax-highlighting
```

```bash
# ~/.zshrc 맨 아래
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

- **autosuggestions**: 과거에 쳤던 명령을 회색으로 미리 보여줍니다. `→` 키로 수락.
- **syntax-highlighting**: 존재하지 않는 명령은 빨갛게 표시되어 오타를 실행 전에
  알아챕니다.

## Starship 프롬프트

셸 종류와 무관하게 동작하는 프롬프트입니다.

```bash
brew install starship
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
```

설정은 `~/.config/starship.toml` 하나입니다.

```toml
# 명령 실행 시간이 오래 걸린 경우에만 표시
[cmd_duration]
min_time = 2000
format = " [$duration]($style)"

# 디렉터리는 3단계까지만
[directory]
truncation_length = 3
truncate_to_repo = true

# 실패한 명령을 눈에 띄게
[character]
success_symbol = "[❯](green)"
error_symbol = "[❯](red)"
```

Git 브랜치, Node/Python/Go 버전, 쿠버네티스 컨텍스트 등은 해당 프로젝트 안에
들어갔을 때만 자동으로 나타납니다. 켜고 끄는 것은 모듈별 `disabled` 값으로
조절합니다.

```toml
[kubernetes]
disabled = false
```

## 자주 쓰는 별칭

```bash
alias ll='ls -alh'
alias gs='git status -sb'
alias gd='git diff'
alias ..='cd ..'
alias ...='cd ../..'
```

별칭은 편하지만 남용하면 다른 사람 장비에서 아무것도 못 하게 됩니다. 팀에 공유하는
스크립트에는 원래 명령을 씁니다.

## 다음 단계

셸이 편해졌다면 창을 여러 개 여는 대신 세션 하나로 관리해 봅니다 →
[tmux로 세션 유지하기](/docs/terminal/tmux/)
