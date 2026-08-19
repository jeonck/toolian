---
weight: 10030
title: "Mermaid 다이어그램"
description: "코드 블록에 텍스트로 적으면 그림이 되는 다이어그램. 버전 관리가 되는 도식."
icon: "schema"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

이미지로 만든 다이어그램은 수정할 때마다 원본 파일을 찾아야 하고, diff에서
무엇이 바뀌었는지 보이지 않습니다. Mermaid는 텍스트라서 **PR에서 변경점이 그대로
읽힙니다.**

GitHub, GitLab, Notion, Obsidian, 그리고 이 사이트를 포함한 대부분의 문서
도구가 기본 지원합니다.

## 순서도

````markdown
```mermaid
flowchart TD
    A[요청 수신] --> B{인증 토큰 있음?}
    B -->|아니오| C[401 반환]
    B -->|예| D{토큰 유효?}
    D -->|아니오| C
    D -->|예| E[핸들러 실행]
    E --> F[응답 반환]
```
````

```mermaid
flowchart TD
    A[요청 수신] --> B{인증 토큰 있음?}
    B -->|아니오| C[401 반환]
    B -->|예| D{토큰 유효?}
    D -->|아니오| C
    D -->|예| E[핸들러 실행]
    E --> F[응답 반환]
```

방향은 `TD`(위→아래), `LR`(왼→오른쪽), `BT`, `RL` 중에 고릅니다.

| 모양 | 문법 |
|---|---|
| 사각형 | `A[텍스트]` |
| 둥근 사각형 | `A(텍스트)` |
| 마름모(조건) | `A{텍스트}` |
| 원통(DB) | `A[(텍스트)]` |
| 원 | `A((텍스트))` |

| 화살표 | 문법 |
|---|---|
| 실선 | `-->` |
| 점선 | `-.->` |
| 굵은 선 | `==>` |
| 라벨 | `-->|텍스트|` |

## 시퀀스 다이어그램

API 흐름을 설명할 때 가장 자주 씁니다.

````markdown
```mermaid
sequenceDiagram
    participant C as 클라이언트
    participant A as API 서버
    participant D as DB

    C->>A: POST /login
    A->>D: 사용자 조회
    D-->>A: 사용자 레코드
    A->>A: 비밀번호 검증
    A-->>C: 액세스 토큰
    Note over C,A: 이후 요청은 Bearer 토큰 사용
```
````

```mermaid
sequenceDiagram
    participant C as 클라이언트
    participant A as API 서버
    participant D as DB

    C->>A: POST /login
    A->>D: 사용자 조회
    D-->>A: 사용자 레코드
    A->>A: 비밀번호 검증
    A-->>C: 액세스 토큰
    Note over C,A: 이후 요청은 Bearer 토큰 사용
```

`->>`는 실선 화살표, `-->>`는 점선(응답), `->>+` / `-->>-`는 활성화 박스를
켜고 끕니다.

## 상태 다이어그램

````markdown
```mermaid
stateDiagram-v2
    [*] --> 대기
    대기 --> 처리중: 작업 수신
    처리중 --> 완료: 성공
    처리중 --> 실패: 예외
    실패 --> 대기: 재시도
    실패 --> [*]: 재시도 초과
    완료 --> [*]
```
````

```mermaid
stateDiagram-v2
    [*] --> 대기
    대기 --> 처리중: 작업 수신
    처리중 --> 완료: 성공
    처리중 --> 실패: 예외
    실패 --> 대기: 재시도
    실패 --> [*]: 재시도 초과
    완료 --> [*]
```

## ER 다이어그램

````markdown
```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "ordered in"
```
````

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "ordered in"
```

## 간트 차트

````markdown
```mermaid
gantt
    title 릴리스 일정
    dateFormat YYYY-MM-DD
    section 개발
    API 구현       :done, a1, 2026-08-01, 10d
    프론트 연동     :active, a2, 2026-08-11, 7d
    section 검증
    QA            :a3, after a2, 5d
    배포           :milestone, after a3, 0d
```
````

```mermaid
gantt
    title 릴리스 일정
    dateFormat YYYY-MM-DD
    section 개발
    API 구현       :done, a1, 2026-08-01, 10d
    프론트 연동     :active, a2, 2026-08-11, 7d
    section 검증
    QA            :a3, after a2, 5d
    배포           :milestone, after a3, 0d
```

## 실전 요령

- **먼저 [Mermaid Live Editor](https://mermaid.live)에서 만들어 보세요.** 문법
  오류를 바로 확인할 수 있습니다.
- **노드는 10개 안쪽으로.** 그보다 커지면 다이어그램 두 개로 나누는 편이 읽힙니다.
- **괄호나 콜론이 들어간 라벨은 따옴표로 감쌉니다**: `A["처리 (비동기)"]`
- **한글 라벨도 그대로 됩니다.** 다만 특수문자가 섞이면 따옴표를 쓰세요.
- 렌더링이 안 되면 대개 들여쓰기나 화살표 문법 오타입니다. 한 줄씩 지워가며
  범인을 찾는 것이 가장 빠릅니다.

## 다음 단계

정형화되지 않은 그림이 필요하다면 → [Excalidraw](/docs/writing/excalidraw/)
