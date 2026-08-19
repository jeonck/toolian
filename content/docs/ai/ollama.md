---
weight: 5040
title: "Ollama — 로컬 LLM"
description: "인터넷 없이 내 컴퓨터에서 모델을 돌리는 방법과, 현실적인 기대치."
icon: "dns"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

보안 정책상 코드를 외부로 보낼 수 없거나, 오프라인에서 작동해야 하거나, 대량
요청 비용을 줄이고 싶을 때 로컬 모델이 선택지가 됩니다. Ollama는 그 과정을
`docker run` 수준으로 단순화합니다.

## 설치

```bash
brew install ollama
ollama serve          # 백그라운드 서비스 시작
```

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Windows는 공식 설치 파일을 사용합니다.

## 모델 실행

```bash
ollama pull llama3.2          # 모델 내려받기
ollama run llama3.2           # 대화형 실행
ollama list                   # 설치된 모델 목록
ollama rm llama3.2            # 삭제
```

프롬프트를 파이프로 넘길 수도 있습니다.

```bash
cat error.log | ollama run llama3.2 "이 로그에서 근본 원인을 찾아줘"
```

## 모델 고르기

| 파라미터 규모 | 필요 메모리(대략) | 용도 |
|---|---|---|
| 1~3B | 4GB | 요약, 분류, 간단한 변환 |
| 7~8B | 8~16GB | 일반 질의, 코드 설명 |
| 13~14B | 16~32GB | 좀 더 복잡한 추론 |
| 30B+ | 32GB 이상 | 품질 우선, 속도 포기 |

양자화 버전(`:q4_K_M` 등)은 메모리를 줄이는 대신 품질이 조금 떨어집니다. 노트북
환경이라면 7~8B 양자화 모델이 현실적인 출발점입니다.

## API로 붙이기

Ollama는 로컬 HTTP 서버를 띄웁니다.

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "SELECT 문에서 인덱스가 안 타는 흔한 이유 3가지",
  "stream": false
}'
```

OpenAI 호환 엔드포인트도 제공하므로 기존 SDK 코드를 주소만 바꿔 쓸 수 있습니다.

```bash
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.2","messages":[{"role":"user","content":"안녕"}]}'
```

## 커스텀 모델 정의

`Modelfile`로 시스템 프롬프트와 파라미터를 고정할 수 있습니다.

```
FROM llama3.2
PARAMETER temperature 0.2
SYSTEM """
너는 코드 리뷰어다. 한국어로 답하고, 지적은 근거와 함께 3개 이내로 한다.
"""
```

```bash
ollama create reviewer -f Modelfile
ollama run reviewer
```

## 현실적인 기대치

- **품질은 클라우드 상용 모델보다 낮습니다.** 복잡한 리팩터링이나 긴 문맥 추론에는
  아직 부족합니다.
- **잘 맞는 일**: 로그 요약, 분류, 번역 초안, 형식 변환, 커밋 메시지 초안.
- **속도**: GPU 또는 Apple Silicon 통합 메모리가 있어야 실용적입니다. CPU만으로는
  체감이 매우 느립니다.
- **디스크**: 모델 하나에 수 GB씩 차지합니다. `ollama list`로 주기적으로 정리하세요.

## 다음 단계

AI 도구가 만든 변경을 안전하게 관리하려면 버전 관리가 필수입니다 →
[Git & 협업](/docs/git/)
