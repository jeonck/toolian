---
weight: 8740
title: "ZAP"
description: "Testing the running application from the outside — a proxy, a scanner, and a baseline scan you can put in CI."
icon: "radar"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Static analysis reads your code. Dependency scanning reads your lockfile. Neither of them
sends a request to the deployed application and sees what actually comes back — the
missing security header, the error page that leaks a stack trace, the endpoint that
forgot its auth check because it's wired up in configuration rather than code.

[ZAP](https://www.zaproxy.org/) is the open-source tool for that: an intercepting proxy
and dynamic scanner (DAST). Long known as OWASP ZAP, the project moved to the Software
Security Project in 2023 and is now maintained as **ZAP by Checkmarx** — still open
source, still free.

## The two ways to use it

**Interactively**, as a proxy. Run the desktop app, point your browser at it, and browse
the application. ZAP records every request, flags passive findings as it goes, and lets
you replay and tamper with any request. For understanding an app's actual attack surface,
an hour here beats any report.

**Automated**, in CI. This is the one to set up first, because it needs no expertise to
be useful:

```bash
docker run -t --rm -v $(pwd):/zap/wrk/:rw \
  ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py -t https://staging.example.com -r report.html
```

The **baseline scan** only spiders and observes — it doesn't attack — so it's safe against
a staging environment and finishes in minutes. It catches the unglamorous, real findings:
missing `Content-Security-Policy`, cookies without `Secure` or `HttpOnly`, verbose server
headers, mixed content, absent `X-Frame-Options`.

```yaml
- name: ZAP baseline
  uses: zaproxy/action-baseline@v0.12.0
  with:
    target: https://staging.example.com
    rules_file_name: .zap/rules.tsv        # ignore known-accepted findings, with reasons
```

For an API, `zap-api-scan.py` takes your OpenAPI or GraphQL schema and tests the endpoints
it describes — which is usually a better fit than spidering a JSON API.

## The full scan, and the rule that governs it

`zap-full-scan.py` actively attacks: injection payloads, traversal attempts, malformed
input. It finds far more, and it will create junk data, trigger emails, and occasionally
break things.

**Only run it against an environment you own and have permission to test.** Not
production without a written agreement, never a third party's service, and check your
cloud provider's policy on penetration testing first. This is the difference between
security testing and an incident report with your name on it.

Authentication is where DAST gets fiddly and where the value is: an unauthenticated scan
tests the login page and little else. ZAP's automation framework handles session
management, and it's worth the configuration time — most of your app is behind the login.

## Reading the output

Expect false positives; DAST infers from responses. Triage in this order:

1. **Anything about authentication or authorisation.** These are rarely wrong and always
   serious.
2. **Injection and traversal findings**, verified by hand before you file them.
3. **Missing headers and cookie flags.** Usually a one-line fix in your reverse proxy or
   framework — do these first, they're free.
4. **Informational noise.** Add to the rules file with a reason and a date.

## Where it fits

| Question | Tool |
|---|---|
| Is there a bad pattern in my source? | [Semgrep](/docs/security/semgrep/) |
| Do my dependencies have known CVEs? | [Trivy](/docs/devops/trivy/) |
| What does the app expose to the internet? | ZAP |
| Is something odd happening at runtime? | [Falco](/docs/devops/falco/), [Wazuh](/docs/security/wazuh/) |

ZAP is the only one on this site that tests the system as an outsider sees it, which is
why it finds a different class of problem — and why a scan is not a penetration test. It
checks known categories; it does not think about your business logic.

## Next

The last layer: the hosts themselves, watched continuously →
[Wazuh](/docs/security/wazuh/)
