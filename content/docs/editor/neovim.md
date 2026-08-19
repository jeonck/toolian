---
weight: 4020
title: "Neovim"
description: "키보드만으로 편집하는 모달 에디터. 서버 작업이 잦다면 투자 가치가 크다."
icon: "keyboard"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Neovim의 진짜 이점은 "빠르다"가 아니라 **어느 서버에나 있고, 손이 홈 포지션을
떠나지 않는다**는 점입니다. SSH로 파일을 고칠 일이 잦다면 기본 조작만 알아도
본전을 뽑습니다.

## 설치

```bash
brew install neovim
sudo apt install neovim
winget install Neovim.Neovim
```

## 최소 생존 명령

`vim`을 처음 열었을 때 빠져나오지 못하는 상황부터 없앱니다.

| 명령 | 동작 |
|---|---|
| `Esc` | 일반 모드로 (헷갈리면 일단 이것부터) |
| `i` / `a` | 커서 앞/뒤에서 입력 시작 |
| `:w` | 저장 |
| `:q` | 종료 |
| `:wq` 또는 `ZZ` | 저장 후 종료 |
| `:q!` | 저장하지 않고 강제 종료 |

## 이동과 편집

| 키 | 동작 |
|---|---|
| `h j k l` | 좌 하 상 우 |
| `w` / `b` | 다음 / 이전 단어 |
| `0` / `$` | 줄 처음 / 끝 |
| `gg` / `G` | 문서 처음 / 끝 |
| `{숫자}G` | 해당 줄로 |
| `dd` / `yy` / `p` | 줄 삭제 / 복사 / 붙여넣기 |
| `u` / `Ctrl+r` | 실행 취소 / 다시 실행 |
| `/문자열` → `n` | 검색 후 다음 결과 |
| `:%s/old/new/g` | 전체 치환 |

Vim의 문법은 **동사 + 대상**입니다. `d`(삭제) + `w`(단어) = `dw`,
`c`(변경) + `i"`(따옴표 안) = `ci"`. 이 조합 규칙을 이해하면 외울 것이 급격히
줄어듭니다.

## 설정 시작하기

`~/.config/nvim/init.lua`:

```lua
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.expandtab = true
vim.opt.shiftwidth = 2
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.clipboard = "unnamedplus"   -- 시스템 클립보드 공유
vim.g.mapleader = " "               -- 스페이스를 리더 키로

vim.keymap.set("n", "<leader>w", ":w<CR>")
vim.keymap.set("n", "<leader>e", ":Explore<CR>")
```

## 배포판으로 시작하기

플러그인을 직접 고르기 부담스럽다면 완성된 구성으로 시작합니다.

| 배포판 | 성격 |
|---|---|
| **LazyVim** | 균형 잡힌 기본값, 문서가 친절 |
| **kickstart.nvim** | 파일 하나에 주석으로 설명. 배우기 좋음 |
| **AstroNvim** | 기능이 많고 UI가 화려함 |

```bash
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```

## 학습 경로

1. 터미널에서 `vimtutor`를 한 번 끝까지 (30분).
2. 일주일 동안 설정 파일 편집만 Neovim으로.
3. 익숙해지면 VS Code에 Vim 확장을 켜서 두 세계를 함께 씁니다.

한 번에 주 에디터를 바꾸려 하면 대부분 실패합니다. 작은 파일부터 시작하세요.

## 다음 단계

대형 프로젝트에서 리팩터링이 잦다면 → [JetBrains IDE](/docs/editor/jetbrains/)
