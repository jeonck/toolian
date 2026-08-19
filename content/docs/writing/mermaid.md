---
weight: 10030
title: "Mermaid"
description: "Diagrams you write as text in a code block — drawings that show up in your diffs."
icon: "schema"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

A diagram exported as an image means hunting for the source file every time it changes,
and a diff that shows nothing. Mermaid is text, so **the change is legible right in the
pull request.**

GitHub, GitLab, Notion, Obsidian, and most documentation tools — this site included —
support it out of the box.

## Flowcharts

````markdown
```mermaid
flowchart TD
    A[Request received] --> B{Has an auth token?}
    B -->|No| C[Return 401]
    B -->|Yes| D{Token valid?}
    D -->|No| C
    D -->|Yes| E[Run the handler]
    E --> F[Return the response]
```
````

```mermaid
flowchart TD
    A[Request received] --> B{Has an auth token?}
    B -->|No| C[Return 401]
    B -->|Yes| D{Token valid?}
    D -->|No| C
    D -->|Yes| E[Run the handler]
    E --> F[Return the response]
```

Pick a direction from `TD` (top-down), `LR` (left-right), `BT`, or `RL`.

| Shape | Syntax |
|---|---|
| Rectangle | `A[text]` |
| Rounded | `A(text)` |
| Diamond (decision) | `A{text}` |
| Cylinder (database) | `A[(text)]` |
| Circle | `A((text))` |

| Arrow | Syntax |
|---|---|
| Solid | `-->` |
| Dotted | `-.->` |
| Thick | `==>` |
| Labelled | `-->\|text\|` |

## Sequence diagrams

The one you'll reach for most when explaining an API flow.

````markdown
```mermaid
sequenceDiagram
    participant C as Client
    participant A as API server
    participant D as Database

    C->>A: POST /login
    A->>D: Look up the user
    D-->>A: User record
    A->>A: Verify the password
    A-->>C: Access token
    Note over C,A: Later requests carry a Bearer token
```
````

```mermaid
sequenceDiagram
    participant C as Client
    participant A as API server
    participant D as Database

    C->>A: POST /login
    A->>D: Look up the user
    D-->>A: User record
    A->>A: Verify the password
    A-->>C: Access token
    Note over C,A: Later requests carry a Bearer token
```

`->>` is a solid arrow, `-->>` a dotted one (a response), and `->>+` / `-->>-` turn
activation boxes on and off.

## State diagrams

````markdown
```mermaid
stateDiagram-v2
    [*] --> Waiting
    Waiting --> Processing: Job received
    Processing --> Done: Success
    Processing --> Failed: Exception
    Failed --> Waiting: Retry
    Failed --> [*]: Retries exhausted
    Done --> [*]
```
````

```mermaid
stateDiagram-v2
    [*] --> Waiting
    Waiting --> Processing: Job received
    Processing --> Done: Success
    Processing --> Failed: Exception
    Failed --> Waiting: Retry
    Failed --> [*]: Retries exhausted
    Done --> [*]
```

## Entity relationships

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

## Gantt charts

````markdown
```mermaid
gantt
    title Release schedule
    dateFormat YYYY-MM-DD
    axisFormat %m/%d
    section Build
    API implementation :done, a1, 2026-08-01, 10d
    Frontend wiring :active, a2, 2026-08-11, 7d
    section Verify
    QA :a3, after a2, 5d
    Release :crit, a4, after a3, 2d
```
````

```mermaid
gantt
    title Release schedule
    dateFormat YYYY-MM-DD
    axisFormat %m/%d
    section Build
    API implementation :done, a1, 2026-08-01, 10d
    Frontend wiring :active, a2, 2026-08-11, 7d
    section Verify
    QA :a3, after a2, 5d
    Release :crit, a4, after a3, 2d
```

## Practical advice

- **Draft in the [Mermaid Live Editor](https://mermaid.live) first.** Syntax errors show
  up immediately.
- **Keep it under about ten nodes.** Past that, two diagrams read better than one.
- **Quote labels containing brackets or colons**: `A["Processing (async)"]`
- If it won't render, it's almost always indentation or an arrow typo. Deleting one line
  at a time finds the culprit fastest.

## Next

For drawings that refuse to be formalised → [Excalidraw](/docs/writing/excalidraw/)
