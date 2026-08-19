---
weight: 3010
title: "ripgrep — 코드 검색"
description: "grep보다 훨씬 빠르고, .gitignore를 알아서 존중하는 코드 전용 검색기."
icon: "manage_search"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`rg`는 프로젝트 전체에서 문자열을 찾을 때 기본값이 가장 합리적인 도구입니다.
`node_modules`나 `.git`을 알아서 건너뛰고, 여러 코어를 써서 병렬로 훑습니다.

## 설치

```bash
brew install ripgrep          # macOS
sudo apt install ripgrep      # Ubuntu 18.10+
winget install BurntSushi.ripgrep.MSVC
```

## 기본 사용

```bash
rg "createUser"                    # 현재 폴더 전체에서 검색
rg "createUser" src/               # 특정 폴더만
rg -i "createuser"                 # 대소문자 무시
rg -w "id"                         # 단어 단위 (identity 는 안 걸림)
rg -F "a.b.c"                      # 정규식 아닌 문자 그대로
```

## 자주 쓰는 옵션

| 옵션 | 의미 |
|---|---|
| `-t py` / `-t js` | 특정 언어 파일만 (`rg --type-list`로 목록 확인) |
| `-T test` | 특정 타입 제외 |
| `-g '*.md'` | 글롭 패턴으로 포함 |
| `-g '!dist/*'` | 글롭 패턴으로 제외 |
| `-l` | 파일 이름만 출력 |
| `-c` | 파일별 매칭 수만 |
| `-n` | 줄 번호 (기본 켜짐) |
| `-A 3` / `-B 3` / `-C 3` | 뒤/앞/양쪽 문맥 줄 |
| `--hidden` | 숨김 파일 포함 |
| `-u` / `-uu` | ignore 규칙 완화 / 전부 무시 |

## 실전 예시

```bash
# TODO 주석을 파일별 개수로 집계
rg -c "TODO" | sort -t: -k2 -rn | head

# 환경 변수 사용처를 문맥과 함께
rg -C 2 "process\.env\." -t ts

# 정의만 찾기 (함수 선언 패턴)
rg "^(export )?(async )?function \w+" -t ts

# 검색 후 결과를 다른 문자열로 치환 (sd 필요)
rg -l "oldName" | xargs sd "oldName" "newName"
```

## 치환까지 한 번에

ripgrep 자체는 치환 미리보기만 제공합니다.

```bash
rg "oldName" -r "newName"     # 화면에만 치환 결과 표시 (파일은 그대로)
```

실제 파일을 바꾸려면 `sd`나 `sed`와 조합합니다.

```bash
brew install sd
rg -l "oldName" | xargs sd "oldName" "newName"
```

## 설정 파일

반복 옵션은 설정 파일로 뺍니다.

```bash
# ~/.ripgreprc
--smart-case
--hidden
--glob=!.git/*
--max-columns=200
```

```bash
# ~/.zshrc
export RIPGREP_CONFIG_PATH="$HOME/.ripgreprc"
```

## 다음 단계

내용이 아니라 파일 이름으로 찾을 때는 `fd`가 편합니다 →
[fd — 파일 찾기](/docs/files/fd/)
