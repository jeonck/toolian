---
weight: 6040
title: "delta — diff 뷰어"
description: "Git diff에 문법 강조·줄 번호·좌우 분할을 입혀 리뷰 속도를 올리는 페이저."
icon: "difference"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

기본 `git diff`는 바뀐 줄 전체를 빨강/초록으로만 표시합니다. 한 줄에서 단어
하나만 바뀌어도 줄 전체가 물들어 무엇이 달라졌는지 눈으로 찾아야 합니다.
`delta`는 **바뀐 부분만 강조**하고 문법 강조까지 넣어줍니다.

## 설치

```bash
brew install git-delta
sudo apt install git-delta
winget install dandavison.delta
```

패키지 이름은 `git-delta`, 실행 파일 이름은 `delta`입니다. 다른 `delta` 패키지와
헷갈리지 않도록 주의하세요.

## 설정

```bash
git config --global core.pager delta
git config --global interactive.diffFilter 'delta --color-only'
git config --global delta.navigate true
git config --global delta.line-numbers true
git config --global merge.conflictStyle zdiff3
```

또는 `~/.gitconfig`에 직접:

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    line-numbers = true
    side-by-side = false
    syntax-theme = Monokai Extended

[merge]
    conflictStyle = zdiff3
```

## 사용

설정만 하면 기존 명령이 그대로 delta를 거칩니다.

```bash
git diff
git show HEAD
git log -p
```

`navigate = true`를 켜두면 페이저 안에서 `n`/`N`으로 파일 단위 점프가 됩니다.
파일이 많은 diff에서 특히 편합니다.

## 좌우 분할 보기

```bash
git diff --side-by-side       # delta.side-by-side = true 로 기본값 지정 가능
```

넓은 모니터에서는 좌우 분할이 읽기 좋고, 노트북에서는 기본 세로 보기가 낫습니다.
상황에 따라 켜고 끄려면 별칭을 만들어 둡니다.

```bash
alias gds='git -c delta.side-by-side=true diff'
```

## 다른 도구에서 쓰기

```bash
# 임의의 두 파일 비교
delta old.json new.json

# lazygit 페이저로 (config.yml)
# git.paging.pager: delta --dark --paging=never
```

## 테마

```bash
delta --list-syntax-themes          # 사용 가능한 테마
delta --show-config                 # 현재 설정 확인
```

라이트/다크 터미널에 맞춰 `--light` 또는 `--dark`를 지정합니다.

## 다음 단계

diff를 읽기 전에 문제를 걸러내려면 → [pre-commit 훅](/docs/git/pre-commit/)
