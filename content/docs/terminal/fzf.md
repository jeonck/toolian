---
weight: 2040
title: "fzf 퍼지 검색"
description: "히스토리·파일·브랜치를 대화형으로 골라내는 만능 필터. 설치 5분, 효과는 매일."
icon: "filter_alt"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

fzf는 "목록을 받아서 사람이 고르게 해주는" 도구입니다. 목록이 무엇이든 상관없기
때문에 히스토리, 파일, Git 브랜치, 프로세스, 도커 컨테이너 등 어디에나 붙습니다.

## 설치

```bash
brew install fzf
$(brew --prefix)/opt/fzf/install     # 셸 키 바인딩 설치 (전부 y)
```

```bash
sudo apt install fzf
```

## 설치만 해도 생기는 세 가지 키

| 키 | 동작 |
|---|---|
| `Ctrl+R` | 명령 히스토리를 퍼지 검색 (기본 히스토리 검색을 대체) |
| `Ctrl+T` | 현재 디렉터리 하위 파일을 골라 커맨드라인에 삽입 |
| `Alt+C` | 하위 디렉터리를 골라 바로 `cd` |

특히 `Ctrl+R`은 설치 직후 가장 크게 체감됩니다. 예전에 친 긴 `docker run ...`
명령을 단어 두어 개로 찾아냅니다.

## 파이프로 무엇이든 고르기

```bash
# 파일 하나 골라서 에디터로 열기
code "$(fzf)"

# 프로세스 골라서 종료
kill -9 $(ps aux | fzf | awk '{print $2}')
```

## 미리보기 붙이기

`bat`이 설치되어 있다면 파일 내용을 옆에 띄울 수 있습니다.

```bash
# ~/.zshrc
export FZF_DEFAULT_OPTS="--height 60% --layout=reverse --border --preview 'bat --color=always --style=numbers {}'"
export FZF_DEFAULT_COMMAND='fd --type f --hidden --exclude .git'
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
```

`fd`와 `bat`은 [파일 & 검색](/docs/files/) 카테고리에서 다룹니다.

## 실전 함수 세 개

`~/.zshrc`에 넣어두면 바로 쓸 수 있습니다.

```bash
# 브랜치 골라서 체크아웃
fbr() {
  local branch
  branch=$(git branch --all | grep -v HEAD | sed 's/.* //' | fzf) || return
  git checkout "${branch#remotes/origin/}"
}

# 커밋 골라서 상세 보기
fshow() {
  git log --oneline --color=always |
    fzf --ansi --preview 'git show --color=always {1}' |
    awk '{print $1}' | xargs git show
}

# 실행 중인 도커 컨테이너에 접속
dsh() {
  local cid
  cid=$(docker ps --format '{{.ID}}\t{{.Names}}\t{{.Image}}' | fzf | cut -f1) || return
  docker exec -it "$cid" sh
}
```

## 검색 문법

fzf 입력창에서 쓸 수 있는 기호입니다.

| 입력 | 의미 |
|---|---|
| `abc` | abc 글자가 순서대로 들어간 항목 |
| `'abc` | abc를 정확히 포함 |
| `^abc` | abc로 시작 |
| `abc$` | abc로 끝남 |
| `!abc` | abc를 포함하지 않음 |
| `abc \| def` | 둘 중 하나 |

## 다음 단계

명령을 찾는 시간을 줄였다면 디렉터리 이동도 줄여봅니다 →
[zoxide로 디렉터리 이동](/docs/terminal/zoxide/)
