---
weight: 8120
title: "Jenkins"
description: "The CI server that still runs the enterprise — self-hosted, plugin-driven, and happy inside an air-gapped network."
icon: "engineering"
date: "2026-08-23"
lastmod: "2026-08-23"
draft: false
---

If you learned CI in the last five years, Jenkins can look like something you'd only meet
in a museum. Then you join a bank, a manufacturer, or any company with a decade of build
history, and there it is — running thousands of jobs a day, wired into hardware nobody
will describe over email.

The reason is not inertia. Jenkins is **software you run yourself**, on your machines,
inside your network, with a plugin for every system your company bought in 2011. Hosted
CI cannot do that, and for a regulated network with no route to the internet, that is the
whole decision.

## Run one locally

```bash
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts-jdk21

docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Open `http://localhost:8080`, paste the password, take the suggested plugins. The volume
is the important part: `/var/jenkins_home` **is** your Jenkins — jobs, credentials,
plugins, history. Back it up and you can rebuild the server; lose it and you have
nothing.

## A pipeline as code

Old Jenkins was configured by clicking through web forms, which is why so many teams
ended up with a server nobody dared touch. Put a `Jenkinsfile` in the repository instead:

```groovy
pipeline {
  agent { docker { image 'node:22-alpine' } }

  options { timeout(time: 30, unit: 'MINUTES') }

  stages {
    stage('Install') { steps { sh 'npm ci' } }
    stage('Test')    { steps { sh 'npm test' } }

    stage('Deploy') {
      when { branch 'main' }
      environment { API_TOKEN = credentials('prod-api-token') }
      steps { sh './deploy.sh' }
    }
  }

  post {
    always  { junit 'reports/**/*.xml' }
    failure { mail to: 'team@example.com', subject: "Failed: ${env.JOB_NAME}" }
  }
}
```

Declarative syntax (`pipeline { }`) is the one to learn. The older scripted syntax is
full Groovy — powerful, and the reason some organisations have 800-line build scripts
only one person understands.

## The pieces you'll actually touch

| Concept | What it is |
|---|---|
| **Controller** | The server: schedules work, holds config and history |
| **Agent** | A machine that runs builds. Never build on the controller |
| **Multibranch pipeline** | Discovers branches and PRs, runs each one's `Jenkinsfile` |
| **Credentials** | Secret store; reference by id, and Jenkins masks the value in logs |
| **Shared library** | Common pipeline code imported by many repositories |

Agents are how Jenkins scales and how it stays safe: builds run untrusted code, and code
running on the controller can read every credential you own.

## Configuration as Code

The cure for the snowflake server is the JCasC plugin — the controller's own settings in
a YAML file, versioned like everything else:

```yaml
jenkins:
  systemMessage: "Managed by JCasC. Do not configure by hand."
  numExecutors: 0          # force builds onto agents
  securityRealm:
    ldap:
      configurations:
        - server: "ldaps://ldap.example.com"
```

Pin plugin versions in `plugins.txt` and bake them into your image. "Update all plugins"
on a Friday is a genuine outage pattern.

## What goes wrong

- **Plugin sprawl.** Every plugin is third-party code with access to your build system.
  Install what you need, watch the security advisories, and remove what you stopped
  using.
- **Backups nobody tested.** Snapshot `$JENKINS_HOME`, and restore it somewhere once so
  you know it works.
- **Builds on the controller.** Set executors to zero and mean it.
- **The one person who knows the Groovy.** Shared libraries with tests, reviewed like
  application code, are how that person goes on holiday.

## Would you choose it today?

For a new project with code on GitHub, no — [GitHub Actions](/docs/devops/github-actions/)
is less work by an order of magnitude. Choose Jenkins deliberately when the build must
run on your own hardware, reach systems no SaaS runner can see, or satisfy an auditor who
wants to know exactly which machine touched the artifact.

## Next

The same self-hosted argument, with the repository included →
[GitLab CI/CD](/docs/devops/gitlab-ci/)
