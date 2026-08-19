---
weight: 10010
title: "Obsidian"
description: "로컬 Markdown 파일로 굴러가는 개인 지식 관리 도구. 링크로 노트를 잇는다."
icon: "menu_book"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Obsidian의 핵심은 **노트가 그냥 폴더 안의 `.md` 파일**이라는 점입니다. 서비스가
사라져도 파일은 남고, Git으로 버전 관리하거나 다른 도구로 옮기기도 쉽습니다.

## 설치와 첫 설정

```bash
brew install --cask obsidian
```

- **볼트(vault)** 는 노트가 담긴 폴더입니다. `~/notes` 같은 경로에 하나 만듭니다.
- 설정 → Files & Links에서 "Default location for new attachments"를
  `attachments` 폴더로 지정하면 이미지가 흩어지지 않습니다.

## 폴더 구조

깊은 폴더 트리를 만들면 어디에 넣을지 고민하다가 기록을 안 하게 됩니다. 얕게
가고 링크로 잇는 편이 오래갑니다.

```
notes/
  inbox/        # 일단 여기 던진다
  daily/        # 날짜별 기록
  projects/     # 진행 중인 일
  reference/    # 정리된 지식
  attachments/
```

## 링크로 잇기

```markdown
[[Docker 네트워크]] 를 참고할 것.
[[Docker 네트워크|컨테이너 통신]] 처럼 표시 텍스트를 바꿀 수도 있다.
```

없는 노트로 링크를 걸어도 됩니다. 나중에 클릭하면 그 이름으로 새 노트가 생깁니다.
이 "일단 링크부터" 방식이 지식이 자라는 가장 자연스러운 경로입니다.

노트 아래쪽의 **Backlinks** 패널은 이 노트를 참조하는 다른 노트를 보여줍니다.
폴더 분류로는 얻을 수 없는 연결이 여기서 드러납니다.

## 데일리 노트

설정 → Core plugins → Daily notes를 켜고 템플릿을 지정합니다.

```markdown
## 오늘 한 일

## 막힌 것

## 배운 것

## 내일
```

매일 같은 틀로 기록하면 나중에 검색으로 "그때 그 에러"를 찾아냅니다.

## 쓸 만한 코어 플러그인

| 플러그인 | 용도 |
|---|---|
| Daily notes | 날짜별 노트 자동 생성 |
| Templates | 노트 템플릿 삽입 |
| Quick switcher | `⌘O`로 노트 이름 검색 |
| Command palette | `⌘P`로 모든 명령 |
| Outgoing links | 이 노트가 참조하는 노트 |
| Graph view | 연결 구조 시각화 (재미는 있지만 실용성은 낮음) |

## 커뮤니티 플러그인

| 플러그인 | 용도 |
|---|---|
| **Dataview** | 노트의 메타데이터로 표·목록 자동 생성 |
| **Templater** | 날짜·변수가 들어간 동적 템플릿 |
| **Excalidraw** | 노트 안에서 손그림 다이어그램 |
| **Obsidian Git** | 볼트를 자동으로 커밋·푸시 |

Dataview 예시 — 진행 중인 프로젝트 목록을 자동으로:

````markdown
```dataview
TABLE status, due
FROM "projects"
WHERE status != "done"
SORT due ASC
```
````

## 동기화

- **Obsidian Sync**: 유료지만 종단간 암호화되고 설정이 가장 간단합니다.
- **Git**: 무료. Obsidian Git 플러그인으로 자동 커밋·푸시. 충돌 해결이 필요할 수
  있어 텍스트 위주 볼트에 적합합니다.
- **iCloud/Dropbox**: 무료지만 동시 편집 시 충돌 파일이 생기기 쉽습니다.

Git을 쓴다면 `.gitignore`에 다음을 넣습니다.

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```

## 대안

| 도구 | 특징 |
|---|---|
| **Notion** | 협업·데이터베이스 강력, 파일은 클라우드에 |
| **Logseq** | 블록 단위 아웃라이너, 로컬 파일 |
| **Apple Notes** | 가볍고 빠름, 링크 기능은 제한적 |

혼자 쌓는 지식은 Obsidian, 팀 공유 문서는 Notion처럼 나눠 쓰는 조합이 흔합니다.

## 다음 단계

어떤 도구를 쓰든 문법은 하나입니다 → [Markdown 기본기](/docs/writing/markdown/)
