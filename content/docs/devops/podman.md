---
weight: 8030
title: "Podman"
description: "Running containers without a root daemon — and why an organisation might require it instead of Docker."
icon: "inventory_2"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Docker](/docs/devops/docker/) runs a daemon as root, and every container you start is
started by that daemon on your behalf. It works, and it's also a single privileged
process that anyone in the `docker` group can ask to mount your filesystem.

[Podman](https://podman.io/) does the same job **daemonless and rootless**: containers are
ordinary child processes of your shell, running as your user. Red Hat maintains it,
Apache 2.0, and the CLI is deliberately identical — `alias docker=podman` genuinely works
for most day-to-day commands.

Two practical reasons teams end up here: a security policy that forbids a root daemon,
and Docker Desktop's licence, which is paid for larger companies. Podman Desktop is not.

## Install and run

```bash
brew install podman            # macOS/Windows also need a VM:
podman machine init
podman machine start

# Linux
sudo dnf install podman        # or apt install podman

podman run -d -p 8080:80 nginx
podman ps
podman logs -f <id>
podman images
```

If those commands look like Docker's, that's the point — the CLI is compatible, it reads
the same `Dockerfile`, and it pulls from the same registries.

## What's genuinely different

| | Docker | Podman |
|---|---|---|
| Architecture | Client → root daemon | Fork/exec, no daemon |
| Default user | Root inside the daemon | Your user, rootless |
| Compose | Built in | `podman compose`, or run Docker Compose against Podman's socket |
| Grouping | — | **Pods**: several containers sharing a network namespace, like Kubernetes |
| Service management | `restart: always` via the daemon | **Quadlet** — containers as systemd units |

**Pods** are the feature the name comes from, and they make local work map onto
Kubernetes more honestly: `podman play kube deployment.yaml` runs a Kubernetes manifest
locally, and `podman generate kube` produces one from what you're running.

**Quadlet** is how you run a container as a real service on a Linux box:

```ini
# ~/.config/containers/systemd/app.container
[Container]
Image=ghcr.io/example/app:1.4.2
PublishPort=8080:8080

[Service]
Restart=always

[Install]
WantedBy=default.target
```

`systemctl --user daemon-reload && systemctl --user start app` — logs go to journald,
restarts are systemd's job, and there's no daemon in the middle. For a single-server
deployment this beats a hand-written `docker run` in a `screen` session by a distance.

## The friction to expect

- **Rootless means unprivileged.** Ports below 1024 need a sysctl or a reverse proxy, and
  anything expecting real root inside the container may need `--privileged` — which
  usually means the image is doing something worth questioning.
- **Volume permissions surprise people.** User namespaces remap UIDs; `:Z` (SELinux) and
  `--userns=keep-id` fix most of it.
- **Tooling that speaks to the Docker socket** — Testcontainers, some CI runners — needs
  Podman's compatible socket enabled (`podman system service`), and then it works.

## Which one to install

If nothing forces the choice, either is fine and Docker has more third-party integration.
Choose Podman deliberately when the policy forbids a root daemon, when Docker Desktop's
licensing applies to your company, or when you want containers managed as systemd units
on a Linux host.

## Next

Before a real cluster, run one on your laptop →
[Local Kubernetes](/docs/devops/local-kubernetes/)
