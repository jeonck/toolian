---
weight: 8080
title: "cert-manager"
description: "TLS certificates issued and renewed automatically inside the cluster, so nobody has to remember an expiry date."
icon: "verified_user"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Certificate expiry is the outage everyone has had. It's fully predictable, it's written
down somewhere, and it still takes the site down at 3am because the person who renewed it
last time changed teams.

[cert-manager](https://cert-manager.io/) removes the human from the loop: you declare that
an Ingress needs TLS, and it obtains the certificate from Let's Encrypt (or your internal
CA), stores it as a Secret, and renews it before it expires. CNCF graduated, and close to
standard equipment in a Kubernetes cluster.

## Install

```bash
helm repo add jetstack https://charts.jetstack.io
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --set crds.enabled=true
```

## An issuer, once

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: ops@example.com
    privateKeySecretRef:
      name: letsencrypt-prod-account-key
    solvers:
      - http01:
          ingress:
            ingressClassName: nginx
```

Then every certificate is one annotation on the Ingress:

```yaml
metadata:
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
    - hosts: [app.example.com]
      secretName: app-example-com-tls
  rules:
    - host: app.example.com
      # ...
```

That's it. cert-manager sees the annotation, requests the certificate, proves you control
the domain, writes the Secret, and renews at two-thirds of the lifetime. Nothing to
diarise.

## Use the staging issuer first

Let's Encrypt rate-limits certificate issuance per domain per week, and a
misconfiguration can burn that budget in an afternoon. Point a second `ClusterIssuer` at
`https://acme-staging-v02.api.letsencrypt.org/directory`, get the flow working there —
the certificate won't be trusted by browsers, which is fine — and switch to production
once it works.

## Debugging, in the right order

Every failed issuance leaves a trail of objects, and reading them in order finds the
problem in about a minute:

```bash
kubectl describe certificate app-example-com-tls
kubectl describe certificaterequest <name>
kubectl describe order <name>
kubectl describe challenge <name>      # the actual error usually lives here
```

Nine times out of ten the challenge failure is one of: DNS not pointing at the ingress
yet, the HTTP-01 path blocked by a redirect to HTTPS, or a network policy stopping
Let's Encrypt from reaching the solver pod. Wildcards can't use HTTP-01 at all — those
need the DNS-01 solver and credentials for your DNS provider.

## Beyond public certificates

The same controller issues from an internal CA (`Issuer` of kind CA) or from Vault, which
is how internal service-to-service TLS gets automated. If you go on to run a service mesh
like [Istio](/docs/devops/istio/), the mesh handles workload identity itself — cert-manager
stays responsible for the certificates at the edge.

## Next

Traffic inside the cluster needs the same care as traffic at the edge →
[Istio](/docs/devops/istio/)
