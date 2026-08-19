---
weight: 6030
title: "lazygit"
description: "터미널 안의 Git GUI. 스테이징·리베이스·충돌 해결을 키보드로 빠르게."
icon: "dashboard"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

lazygit은 `git status`, `git add -p`, `git log`, `git rebase -i`를 한 화면에
합쳐 놓은 TUI입니다. 특히 **부분 스테이징과 대화형 리베이스**에서 손이 훨씬
빨라집니다.

## 설치와 실행

```bash
brew install lazygit
sudo apt install lazygit
winget install JesseDuffield.lazygit

cd ~/projects/my-app
lazygit                     # 또는 별칭: alias lg='lazygit'
```

## 화면 구조

왼쪽 세로로 다섯 개의 패널이 있고, `1`~`5` 또는 `Tab`으로 이동합니다.

| 번호 | 패널 | 용도 |
|---|---|---|
| 1 | Status | 저장소 상태, 설정 |
| 2 | Files | 변경 파일, 스테이징 |
| 3 | Branches | 브랜치 전환·병합 |
| 4 | Commits | 히스토리, 리베이스 |
| 5 | Stash | 임시 저장 |

## 필수 키

| 키 | 동작 |
|---|---|
| `Space` | 파일/조각 스테이징 토글 |
| `Enter` | 파일 안으로 들어가 줄 단위 스테이징 |
| `c` | 커밋 |
| `A` | 직전 커밋에 추가 (amend) |
| `p` | pull |
| `P` | push |
| `d` | 삭제 / 변경 버리기 |
| `?` | 현재 패널의 키 도움말 |
| `q` | 나가기 |

키를 외울 필요 없이 `?`를 누르면 그 자리에서 목록이 뜹니다.

## 줄 단위 스테이징

`git add -p`보다 직관적입니다.

1. Files 패널에서 파일 선택 후 `Enter`
2. 방향키로 조각 이동, `Space`로 조각 스테이징
3. `v`로 범위 선택 후 `Space`를 누르면 원하는 줄만 스테이징
4. `Esc`로 나와서 `c`로 커밋

디버깅 코드가 섞인 파일에서 필요한 변경만 골라 커밋할 때 가장 유용합니다.

## 대화형 리베이스

Commits 패널(`4`)에서:

| 키 | 동작 |
|---|---|
| `s` | 아래 커밋과 합치기 (squash) |
| `f` | 합치되 메시지는 버리기 (fixup) |
| `r` | 메시지 수정 (reword) |
| `d` | 커밋 삭제 |
| `Ctrl+J` / `Ctrl+K` | 커밋 순서 이동 |

정리가 끝나면 자동으로 리베이스가 적용됩니다. 충돌이 나면 Files 패널에서 해결한 뒤
`m` 메뉴에서 continue를 고릅니다.

## 설정

`lazygit --print-config-dir`로 경로를 확인한 뒤 `config.yml`을 만듭니다.

```yaml
gui:
  showFileTree: true
  mouseEvents: true
  theme:
    selectedLineBgColor: ["reverse"]
git:
  paging:
    colorArg: always
    pager: delta --dark --paging=never
```

`delta`를 페이저로 지정하면 diff가 훨씬 읽기 쉬워집니다 → 다음 문서.

## 다음 단계

diff 자체를 읽기 좋게 만들기 → [delta — diff 뷰어](/docs/git/delta/)
