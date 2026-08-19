---
weight: 2050
title: "zoxide로 디렉터리 이동"
description: "자주 가는 폴더를 기억했다가 이름 일부만으로 이동시켜 주는 cd 대체 도구."
icon: "moving"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`cd ~/work/projects/backend/services/auth` 같은 경로를 하루에 몇 번씩 치고
있다면 zoxide가 그 타이핑을 `z auth` 로 줄여줍니다. 방문한 디렉터리에 빈도와
최근성 점수를 매겨 가장 그럴듯한 곳으로 보내줍니다.

## 설치

```bash
brew install zoxide
```

```bash
curl -sSfL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh
```

셸에 연결합니다.

```bash
# ~/.zshrc
eval "$(zoxide init zsh)"

# bash 사용자
eval "$(zoxide init bash)"
```

`cd` 자체를 대체하고 싶다면:

```bash
eval "$(zoxide init --cmd cd zsh)"
```

## 사용법

```bash
# 평소처럼 다니면 zoxide가 기록한다
cd ~/work/projects/backend

# 이후에는 조각만 입력
z backend
z proj back      # 여러 조각을 공백으로 (AND 조건)

# 후보를 직접 고르기 (fzf 필요)
zi backend
```

| 명령 | 동작 |
|---|---|
| `z <조각>` | 점수가 가장 높은 디렉터리로 이동 |
| `zi <조각>` | 후보 목록을 대화형으로 선택 |
| `z -` | 직전 디렉터리로 |
| `zoxide query -l` | 기록된 경로를 점수순으로 출력 |
| `zoxide remove <경로>` | 기록에서 제거 |

## 주의할 점

- **초반 며칠은 효과가 없습니다.** 데이터베이스가 쌓여야 정확해집니다. 일주일만
  평소대로 다니면 대부분의 이동이 두 글자로 끝납니다.
- **비슷한 이름이 많으면** `z` 대신 `zi`로 골라 확인하는 습관이 안전합니다.
  스크립트 안에서는 절대 `z`를 쓰지 말고 절대 경로를 씁니다.
- 기존 데이터베이스를 초기화하려면 `~/.local/share/zoxide/db.zo`를 지웁니다.

## 함께 쓰면 좋은 조합

```bash
# 디렉터리 이동 후 바로 파일 검색
z auth && rg "TODO"

# fzf 미리보기와 함께 후보 고르기
zi
```

## 다음 단계

이동이 빨라졌으니 이제 그 안에서 파일을 찾는 도구로 넘어갑니다 →
[ripgrep으로 코드 검색](/docs/files/ripgrep/)
