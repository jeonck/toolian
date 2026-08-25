---
weight: 8120
title: "Secrets Management"
description: "Getting credentials out of .env files and repositories — Vault, OpenBao, and the smaller options worth trying first."
icon: "key"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Every system needs credentials, and the default place people keep them is the worst one:
a `.env` file passed around in chat, copied to a server, and eventually committed. The
problem is not that people are careless. It's that no obvious better place exists until
someone sets one up.

A secrets manager is that place: one system that stores credentials encrypted, hands
them out to identities that prove who they are, logs every access, and can rotate or
revoke without a redeploy.

## Start with what you already have

Don't reach for a cluster on day one. In order of effort:

| Situation | Use |
|---|---|
| One repo, a few CI secrets | GitHub Actions secrets, or your CI's own store |
| Files that must live in Git | SOPS with age, or Sealed Secrets on Kubernetes |
| One cloud | AWS Secrets Manager, GCP Secret Manager, Azure Key Vault |
| Many systems, many clouds, audit requirements | Vault or OpenBao |

The middle two cover most teams for years. What they share with the big options is the
thing that matters: **the secret is not in the repository in plaintext, and access is
attributable.**

## SOPS, the small step that fixes the most

```bash
brew install sops age
age-keygen -o key.txt                     # keep this key out of the repo

sops --encrypt --age <public-key> secrets.yaml > secrets.enc.yaml
sops secrets.enc.yaml                     # opens your editor, re-encrypts on save
```

`secrets.enc.yaml` is safe to commit: values are encrypted, keys stay readable, so diffs
still show *which* secret changed without revealing it. That single property is why SOPS
fits GitOps — [Argo CD](/docs/devops/argocd/) can sync a repository full of encrypted
values without anyone leaking a password to Git history.

## Vault, and OpenBao

[HashiCorp Vault](https://developer.hashicorp.com/vault) is the mature option: dynamic
credentials that are created per request and expire, PKI, encryption as a service,
per-path policies, and a full audit log.

Two facts to know before you commit to it. Vault moved to the Business Source License in
2023 and HashiCorp is now part of IBM. In response, [OpenBao](https://openbao.org/) — a
Linux Foundation fork, MPL 2.0, production-ready since 2024 — has picked up the
genuinely-open-source lane, and it is a drop-in for most Vault usage. Choose on licence
and support posture, not features, because at the level most teams use them the features
are the same.

```bash
vault server -dev                       # local only; prints a root token
export VAULT_ADDR=http://127.0.0.1:8200

vault kv put secret/app db_password=hunter2
vault kv get -field=db_password secret/app
```

The feature that changes how you work is **dynamic secrets**: Vault creates a database
user on demand, hands it to the app with a lease, and deletes it when the lease expires.
Nobody holds a long-lived database password because there isn't one.

On Kubernetes, the External Secrets Operator pulls from Vault, OpenBao, or a cloud
manager and projects the values as normal Secrets, so applications need no library and
no awareness of where the value came from.

## The rules that matter more than the tool

- **Rotate on exposure, and practise it.** A secret you cannot rotate quickly is an
  incident waiting for its trigger. Know the steps before you need them.
- **Short-lived beats stored.** OIDC federation between your CI and your cloud — GitHub
  Actions into AWS, for instance — removes the stored key entirely. That is the single
  highest-value change on this page.
- **Least privilege per identity.** One token that reads everything is a `.env` file
  with extra steps.
- **Audit logs are the point.** "Who read the production database password, and when"
  should have an answer.
- **A committed secret is burned.** Rewriting history does not un-leak it — rotate it.
  [pre-commit hooks](/docs/git/pre-commit/) with a secret scanner stop most of these
  before they land.

## Next

Credentials are one supply-chain risk; the packages and images are the other →
[Trivy](/docs/devops/trivy/)
