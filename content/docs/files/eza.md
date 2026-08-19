---
weight: 3040
title: "eza — 파일 목록"
description: "색상·아이콘·트리·Git 상태를 함께 보여주는 ls 대체 도구."
icon: "list"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`ls -alh`의 출력은 정보가 많지만 읽기가 어렵습니다. `eza`는 같은 정보를 색과
정렬로 구분해 주고, 트리 보기와 Git 상태 표시까지 지원합니다.

## 설치

```bash
brew install eza
sudo apt install eza            # Ubuntu 24.04+
cargo install eza               # 그 외
```

## 기본 사용

```bash
eza                    # 기본 목록
eza -l                 # 상세 (권한, 크기, 수정 시각)
eza -la                # 숨김 포함
eza -l --git           # Git 상태 열 추가
eza --tree --level=2   # 2단계 트리
eza -l --sort=modified --reverse   # 최근 수정 순
```

## 자주 쓰는 옵션

| 옵션 | 의미 |
|---|---|
| `-l` | 상세 보기 |
| `-a` | 숨김 파일 포함 |
| `-T` / `--tree` | 트리 형태 |
| `--level=N` | 트리 깊이 |
| `--git` | Git 상태 열 |
| `--icons` | 파일 종류 아이콘 (Nerd Font 필요) |
| `--group-directories-first` | 디렉터리를 위로 |
| `-s size` / `-s modified` | 정렬 기준 |
| `--total-size` | 디렉터리 실제 크기 계산 |

## 별칭 추천

```bash
# ~/.zshrc
alias ls='eza --group-directories-first'
alias ll='eza -l --git --group-directories-first'
alias la='eza -la --git --group-directories-first'
alias lt='eza --tree --level=2 --group-directories-first'
```

`ls`를 덮어쓰는 것이 불안하다면 `l`, `ll`만 별칭으로 두고 `ls`는 원래대로 남겨
두세요. 스크립트가 `ls` 출력을 파싱하는 경우 문제가 생길 수 있습니다.

## tree 대신 쓰기

```bash
eza --tree --level=3 --ignore-glob="node_modules|.git"
```

디렉터리 구조를 문서에 붙일 때 유용합니다.

## 다음 단계

이제 텍스트가 아니라 JSON을 다뤄봅니다 → [jq — JSON 가공](/docs/files/jq/)
