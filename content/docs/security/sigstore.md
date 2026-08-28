---
weight: 8730
title: "Sigstore"
description: "Signing artifacts without managing keys — keyless signatures, a public transparency log, and a verify step your cluster can enforce."
icon: "fingerprint"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Scanning tells you an image has no known CVEs. It tells you nothing about **where the
image came from**. If someone can push to your registry, they can push an image with your
tag, and every check you run will pass because the artifact is genuinely clean — just not
yours.

[Sigstore](https://www.sigstore.dev/) closes that gap, and its trick is removing the part
everyone gets wrong: the keys. With **keyless signing**, your CI proves its identity with
an OIDC token, receives a short-lived certificate, signs, and the signature is recorded in
a public transparency log. There is no private key to store, rotate, or leak.

## Sign an image

```bash
brew install cosign          # or a release binary

# in CI, with an OIDC token available — no key material at all
cosign sign ghcr.io/example/myapp@sha256:abc123...

# verify, pinned to who signed it and from where
cosign verify ghcr.io/example/myapp@sha256:abc123... \
  --certificate-identity-regexp '^https://github.com/example/myapp/' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

The two `--certificate-*` flags are the whole security property, and skipping them is the
common mistake: without them you've verified that *somebody* signed it, which is not a
claim worth anything. Always assert **which** identity, from **which** issuer.

Sign by digest, not by tag. Tags move; digests don't.

## In a GitHub Actions job

```yaml
permissions:
  id-token: write          # the OIDC token cosign needs
  packages: write

steps:
  - uses: sigstore/cosign-installer@v3
  - run: cosign sign --yes ${IMAGE}@${DIGEST}
  - run: cosign attest --yes --predicate sbom.json --type spdx ${IMAGE}@${DIGEST}
```

`attest` is the second half: attach a signed statement *about* the artifact — an SBOM
from [Trivy](/docs/devops/trivy/), test results, a SLSA provenance record saying which
workflow built it from which commit. Now "what's in this image and where did it come
from" has a cryptographic answer instead of an institutional memory.

## Make the verification mean something

A signature nobody checks is decoration. Put the check where it can refuse:

```yaml
# Kyverno — reject unsigned images at admission
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata: { name: verify-images }
spec:
  validationFailureAction: Audit        # Enforce once it's clean
  rules:
    - name: check-signature
      match:
        any: [{ resources: { kinds: [Pod] } }]
      verifyImages:
        - imageReferences: ["ghcr.io/example/*"]
          attestors:
            - entries:
                - keyless:
                    subject: "https://github.com/example/*"
                    issuer: "https://token.actions.githubusercontent.com"
```

That's [policy as code](/docs/devops/policy-as-code/) doing the enforcing and Sigstore
doing the proving. Start in `Audit`, as always, and switch to `Enforce` once every
legitimate image passes.

## What to know before adopting it

- **Keyless signatures are public.** The certificate and the log entry go into Rekor, a
  public transparency log — including the identity that signed and the repository name. For
  private projects that may be more disclosure than you want; a private Sigstore
  deployment or key-based signing is the alternative.
- **Verification needs the log to be reachable.** Air-gapped environments need the bundle
  attached to the artifact and offline verification configured.
- **It proves origin, not quality.** A signed image can still be vulnerable. Signing and
  scanning answer different questions and you want both.
- **Cosign also signs blobs, Helm charts, and files**, not only images — the same flow
  covers release binaries.

## Next

Everything so far inspects artifacts. Now test the thing while it's running →
[ZAP](/docs/security/zap/)
