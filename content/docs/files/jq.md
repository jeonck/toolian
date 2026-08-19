---
weight: 3050
title: "jq — JSON 가공"
description: "API 응답과 설정 파일에서 원하는 값만 뽑고 형태를 바꾸는 JSON 전용 필터."
icon: "data_object"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

API 응답을 눈으로 훑다가 필요한 필드를 놓친 적이 있다면 `jq`가 답입니다. JSON을
읽기 좋게 정렬하고, 필요한 부분만 뽑고, 다른 구조로 바꿉니다.

## 설치

```bash
brew install jq
sudo apt install jq
winget install jqlang.jq
```

## 기본 사용

```bash
# 보기 좋게 정렬
curl -s https://api.github.com/repos/jeonck/toolian | jq

# 필드 하나
jq '.name' repo.json

# 중첩 필드
jq '.owner.login' repo.json

# 배열 전체 순회
jq '.[] | .name' repos.json

# 여러 필드를 새 객체로
jq '.[] | {name, stars: .stargazers_count}' repos.json
```

## 자주 쓰는 필터

| 필터 | 의미 |
|---|---|
| `.` | 입력 전체 |
| `.foo` | 필드 |
| `.foo?` | 없으면 에러 대신 null |
| `.[]` | 배열/객체 순회 |
| `.[2:5]` | 배열 슬라이스 |
| `\|` | 파이프 (앞 결과를 뒤로) |
| `select(조건)` | 조건에 맞는 것만 |
| `map(식)` | 배열 각 요소에 적용 |
| `length` | 길이 |
| `keys` | 키 목록 |
| `sort_by(.field)` | 정렬 |
| `group_by(.field)` | 묶기 |
| `-r` | 따옴표 없는 원시 출력 |

## 실전 예시

```bash
# 별 100개 넘는 저장소 이름만
jq -r '.[] | select(.stargazers_count > 100) | .name' repos.json

# 배열을 CSV로
jq -r '.[] | [.name, .language, .stargazers_count] | @csv' repos.json

# 상태 코드별 개수 집계
jq -r '.[].status' logs.json | sort | uniq -c | sort -rn

# 값 수정해서 새 파일로
jq '.version = "2.0.0"' package.json > tmp && mv tmp package.json

# 두 파일 합치기
jq -s '.[0] * .[1]' base.json override.json
```

## 안전하게 쓰는 요령

- **원본 덮어쓰기 금지**: `jq ... file.json > file.json` 은 파일을 비웁니다.
  임시 파일을 거치거나 `sponge`(moreutils)를 씁니다.
- **없는 키**: `.a.b`에서 `a`가 없으면 에러입니다. `.a?.b?` 또는 `// "기본값"`으로
  방어합니다.
- **디버깅**: 긴 필터는 파이프 단계별로 잘라 실행해 보면 원인을 빨리 찾습니다.

## YAML도 다루려면

```bash
brew install yq
yq '.services.web.image' docker-compose.yml
yq -o=json '.' config.yaml | jq
```

## 다음 단계

파일 다루기가 익숙해졌다면 이제 코드를 쓰는 자리로 갑니다 →
[에디터 & IDE](/docs/editor/)
