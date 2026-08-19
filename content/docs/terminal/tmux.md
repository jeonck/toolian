---
weight: 2030
title: "tmux로 세션 유지하기"
description: "SSH가 끊겨도 살아남는 세션, 화면 분할, 작업별 창 관리 — 최소 명령 모음."
icon: "grid_view"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

tmux는 터미널 안에서 도는 창 관리자입니다. 가장 큰 이점 두 가지는 **SSH 연결이
끊겨도 작업이 계속된다**는 것과 **작업 묶음을 통째로 저장했다가 되살릴 수 있다**는
것입니다.

## 설치와 첫 실행

```bash
brew install tmux          # macOS
sudo apt install tmux      # Ubuntu/Debian

tmux new -s work           # "work"라는 이름의 세션 시작
```

세션에서 빠져나오려면 `Ctrl+B` 를 누른 뒤 `d`(detach). 서버 작업이 그대로 돌아갑니다.
다시 붙으려면:

```bash
tmux ls                    # 세션 목록
tmux attach -t work        # 다시 붙기
```

## 개념 세 가지

| 개념 | 설명 |
|---|---|
| **세션(session)** | 작업 단위. 프로젝트 하나에 세션 하나 |
| **윈도우(window)** | 세션 안의 탭 |
| **페인(pane)** | 윈도우를 나눈 분할 화면 |

## 필수 단축키

모든 단축키는 접두키 `Ctrl+B`를 먼저 누르고 이어서 입력합니다.

| 키 | 동작 |
|---|---|
| `c` | 새 윈도우 |
| `n` / `p` | 다음 / 이전 윈도우 |
| `0`~`9` | 번호로 윈도우 이동 |
| `%` | 세로 분할 |
| `"` | 가로 분할 |
| `방향키` | 페인 이동 |
| `z` | 현재 페인 최대화 / 복귀 |
| `x` | 페인 닫기 |
| `d` | 세션에서 빠져나오기 |
| `[` | 스크롤 모드 진입 (`q`로 종료) |

## 설정 파일로 다듬기

`~/.tmux.conf`:

```bash
# 접두키를 Ctrl+A 로 (Ctrl+B는 손이 멀다)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# 마우스로 페인 크기 조절·스크롤
set -g mouse on

# 윈도우 번호를 1부터
set -g base-index 1

# 분할 시 현재 디렉터리 유지
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"

# 설정 다시 읽기
bind r source-file ~/.tmux.conf \; display "설정을 다시 읽었습니다"
```

## 실전 패턴

원격 서버에서 오래 걸리는 작업을 돌릴 때:

```bash
ssh server
tmux new -s migration
./migrate.sh            # 몇 시간 걸리는 작업
# Ctrl+B, d 로 빠져나온 뒤 노트북을 닫아도 계속 실행됨
```

다음 날 확인:

```bash
ssh server
tmux attach -t migration
```

## 다음 단계

세션 관리까지 갖췄다면 이제 명령과 파일을 찾는 시간을 줄일 차례입니다 →
[fzf 퍼지 검색](/docs/terminal/fzf/)
