---
weight: 8140
title: "SonarQube"
description: "A quality gate in the pipeline — bugs, smells, coverage, and duplication measured on the code you just changed."
icon: "rule"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

Linters catch style. Reviewers catch design. In between sits a category of problem nobody
reliably catches: a null dereference on an error path, a resource never closed, coverage
quietly sliding down for six months, the same forty lines pasted into four services.

[SonarQube](https://www.sonarsource.com/) analyses the codebase for those and turns the
result into a **quality gate** — a pass or fail the pipeline can act on.

## Which edition

The free self-hosted edition is **Community Build**. It analyses the main branch and
reports on the project; branch analysis and pull-request decoration are paid features, as
is the deeper security analysis. SonarQube Cloud (formerly SonarCloud) is the hosted
version and is free for public repositories — which, for open source, is the best deal
here.

```bash
docker run -d --name sonarqube -p 9000:9000 sonarqube:community
```

Log in at `localhost:9000` (`admin`/`admin`), create a project, and generate a token.

## Analyse a project

```bash
# JS/TS, Python, Go, PHP… anything without a special build integration
npx sonarqube-scanner \
  -Dsonar.projectKey=my-app \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=$SONAR_TOKEN
```

```properties
# sonar-project.properties
sonar.projectKey=my-app
sonar.sources=src
sonar.tests=tests
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.exclusions=**/generated/**,**/*.min.js
```

JVM projects use the Maven or Gradle plugin instead; C/C++ needs the build wrapper.
**Feed it your coverage report** — that line above is what turns coverage from a number
in a CI log into something the gate can hold you to.

## Clean as You Code, which is the part that works

The instinct with a legacy codebase is to fix everything, and the result is a backlog of
eleven thousand issues that everyone learns to ignore.

SonarQube's default gate looks only at **new code** — what changed since the last version:
no new bugs, no new vulnerabilities, coverage on new lines above 80%, duplication under
3%. The old debt stays visible but doesn't block anyone. Every change makes the codebase
slightly better, and nobody has to stop for a cleanup quarter.

Set that gate, and resist the urge to raise the bar on old code until the new-code habit
sticks.

## In CI

```yaml
- uses: SonarSource/sonarqube-scan-action@v6
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

- uses: SonarSource/sonarqube-quality-gate-action@v1   # fail the build on a red gate
  timeout-minutes: 5
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

Run it after your tests, so the coverage report exists by the time the scanner looks for
it.

## Where it stops

- **It is not a replacement for review.** It has no idea whether the feature is right.
- **False positives happen.** Mark them as such in the UI with a reason — never by
  loosening the rule for everyone.
- **A gate that always fails gets bypassed.** If the team is routinely overriding it, the
  thresholds are wrong; fix them rather than pretending.

Lighter alternatives worth knowing: [pre-commit hooks](/docs/git/pre-commit/) with
language-native linters cover a lot for zero infrastructure, and Semgrep is a good middle
ground for security-flavoured rules.

## Next

Where the artifact you just built goes → [Nexus Repository](/docs/devops/nexus/)
