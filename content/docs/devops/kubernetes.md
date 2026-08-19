---
weight: 8030
title: "kubectl과 k9s"
description: "쿠버네티스 클러스터를 들여다보고 디버깅하는 데 필요한 최소한의 명령."
icon: "hub"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

쿠버네티스를 직접 운영하지 않더라도, 배포된 서비스의 로그를 보거나 왜 파드가
안 뜨는지 확인할 일은 생깁니다. 이 문서는 **읽고 디버깅하는 데 필요한 명령**에
집중합니다.

## 설치

```bash
brew install kubectl k9s
brew install kubectx        # 컨텍스트/네임스페이스 전환
```

## 컨텍스트 확인 — 가장 먼저 할 일

```bash
kubectl config get-contexts
kubectl config current-context
kubectx staging             # 컨텍스트 전환
kubens my-namespace         # 네임스페이스 전환
```

**운영 클러스터에 붙어 있는 줄 모르고 명령을 날리는 것**이 가장 흔한 사고입니다.
프롬프트에 현재 컨텍스트를 띄워두면 예방됩니다 (Starship의 `kubernetes` 모듈).

## 조회

```bash
kubectl get pods                      # 현재 네임스페이스
kubectl get pods -A                   # 전체 네임스페이스
kubectl get pods -o wide              # 노드·IP 포함
kubectl get pods -w                   # 변화 실시간 관찰
kubectl get deploy,svc,ingress        # 여러 리소스 한 번에

kubectl describe pod <이름>            # 이벤트 포함 상세 (문제 진단의 시작)
kubectl get pod <이름> -o yaml         # 전체 정의
```

## 로그와 접속

```bash
kubectl logs <파드>                    # 로그
kubectl logs -f <파드>                 # 실시간
kubectl logs <파드> -c <컨테이너>       # 사이드카가 있을 때
kubectl logs <파드> --previous         # 재시작 전 로그 (크래시 원인)
kubectl logs -l app=api --tail=100     # 라벨로 여러 파드 동시에

kubectl exec -it <파드> -- sh          # 컨테이너 안으로
kubectl port-forward svc/api 8080:80   # 로컬에서 접근
```

`--previous`는 CrashLoopBackOff를 디버깅할 때 거의 항상 필요합니다.

## 파드 상태 읽기

| 상태 | 의미와 확인할 것 |
|---|---|
| `Pending` | 스케줄 안 됨. 리소스 부족이나 노드 셀렉터 → `describe`의 Events |
| `ImagePullBackOff` | 이미지 이름 오타 또는 레지스트리 인증 |
| `CrashLoopBackOff` | 컨테이너가 계속 죽음 → `logs --previous` |
| `OOMKilled` | 메모리 한도 초과 → limits 조정 |
| `Running` 인데 요청 실패 | readiness 프로브 또는 서비스 셀렉터 확인 |

## k9s — 터미널 대시보드

```bash
k9s
```

| 키 | 동작 |
|---|---|
| `:pods` `:deploy` `:svc` | 리소스 종류 전환 |
| `/문자열` | 필터 |
| `l` | 로그 보기 |
| `d` | describe |
| `s` | 셸 접속 |
| `Ctrl+D` | 삭제 |
| `:ctx` / `:ns` | 컨텍스트·네임스페이스 전환 |
| `?` | 도움말 |

`kubectl get` → `describe` → `logs`를 반복하는 흐름을 키 한 번씩으로 줄여줍니다.
클러스터를 자주 들여다본다면 k9s부터 켜는 편이 빠릅니다.

## 안전 수칙

- `delete`는 항상 `--dry-run=client`로 먼저 확인합니다.
- 운영 컨텍스트에서는 `kubectl apply` 전에 `kubectl diff -f`로 변경분을 봅니다.
- 클러스터 리소스를 직접 수정(`edit`)하면 다음 배포에서 덮어써집니다. 매니페스트
  저장소를 고치는 것이 원칙입니다.

## 다음 단계

배포 자체를 자동화하려면 → [GitHub Actions](/docs/devops/github-actions/)
