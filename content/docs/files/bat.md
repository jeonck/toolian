---
weight: 3030
title: "bat — 파일 읽기"
description: "문법 강조와 줄 번호, Git 변경 표시가 붙은 cat 대체 도구."
icon: "description"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`cat`으로 200줄짜리 설정 파일을 열면 흑백 텍스트 덩어리가 쏟아집니다. `bat`은
같은 파일을 문법 강조·줄 번호와 함께 페이지 단위로 보여줍니다.

## 설치

```bash
brew install bat
sudo apt install bat            # 실행 파일 이름이 batcat
winget install sharkdp.bat
```

```bash
alias bat=batcat                # Ubuntu
```

## 기본 사용

```bash
bat config.yaml                 # 문법 강조 + 줄 번호 + 페이저
bat -n script.sh                # 줄 번호만 (파일명 헤더 없이)
bat -p log.txt                  # 장식 전부 제거 (plain)
bat -r 40:80 main.go            # 40~80줄만
bat -A data.txt                 # 공백·탭·개행을 눈에 보이게
```

## 다른 명령과 조합

```bash
# 파이프로 받아 언어를 지정
curl -s https://api.example.com/data | bat -l json

# git diff에 색을 입혀서
git diff --name-only | xargs bat

# less 대신 기본 페이저로 지정
export PAGER="bat -p"
```

`fzf` 미리보기로 쓰는 것이 가장 흔한 조합입니다.

```bash
export FZF_DEFAULT_OPTS="--preview 'bat --color=always --style=numbers {}'"
```

## 설정

```bash
bat --config-file        # 설정 파일 경로 확인
```

```bash
# ~/.config/bat/config
--theme="Monokai Extended"
--style="numbers,changes,header"
--italic-text=always
```

사용 가능한 테마는 `bat --list-themes`로 확인합니다.

## 사람이 볼 때만 예쁘게

`bat`은 출력이 터미널이 아니라 파이프로 갈 때 자동으로 `cat`처럼 동작합니다.
따라서 스크립트에서 `bat file | grep x` 처럼 써도 색 코드가 섞이지 않습니다.
강제로 색을 넣으려면 `--color=always`를 붙입니다.

## 다음 단계

파일 목록 자체도 읽기 좋게 바꿔봅니다 → [eza — 파일 목록](/docs/files/eza/)
