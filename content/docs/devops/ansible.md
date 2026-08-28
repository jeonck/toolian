---
weight: 8200
title: "Ansible"
description: "Bringing servers to a described state over SSH — no agent, no bootstrap problem, and safe to run twice."
icon: "list_alt"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

[Terraform](/docs/devops/terraform/) creates the machine. Something still has to install
the packages, write the config, and start the service — and if that something is a shell
script somebody ran once in 2023, nobody can tell you what's on the box.

[Ansible](https://docs.ansible.com/) describes the desired state of a machine in YAML
and pushes it over SSH. No agent to install, which is why it survives in the places that
matter: legacy fleets, appliances, network gear, and anywhere containers never arrived.

## Install and check connectivity

```bash
pipx install --include-deps ansible      # or: brew install ansible
```

`inventory.ini` — the machines:

```ini
[web]
web1.example.com
web2.example.com

[db]
db1.example.com ansible_user=deploy
```

```bash
ansible -i inventory.ini all -m ping
```

If that returns green, everything else is detail.

## A playbook

```yaml
- name: Configure web servers
  hosts: web
  become: true

  vars:
    app_port: 8080

  tasks:
    - name: Install packages
      ansible.builtin.package:
        name: [nginx, git]
        state: present

    - name: Write the site config
      ansible.builtin.template:
        src: templates/site.conf.j2
        dest: /etc/nginx/conf.d/site.conf
        mode: "0644"
      notify: Reload nginx

    - name: Ensure nginx is running
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded
```

```bash
ansible-playbook -i inventory.ini site.yml --check --diff   # dry run first
ansible-playbook -i inventory.ini site.yml
ansible-playbook -i inventory.ini site.yml --limit web1.example.com
```

Two ideas carry the whole tool. **Idempotence:** tasks describe a state, so running the
playbook twice changes nothing the second time — that's what makes it safe to run on a
schedule. **Handlers:** the reload fires only if the template actually changed, so you
don't bounce nginx on every run.

## The habits that keep it maintainable

- **`--check --diff` before every real run.** It prints what would change. On a fleet
  you don't know well, this is the difference between a change and an incident.
- **Roles, once a playbook passes about fifty lines.** `ansible-galaxy init roles/web`
  gives the standard layout, and roles are the unit you reuse and test.
- **Encrypt secrets with Vault.** `ansible-vault encrypt group_vars/prod/secrets.yml`
  keeps them in Git safely — see [Secrets management](/docs/devops/secrets/) for when to
  outgrow that.
- **Prefer modules to `command`.** A `shell:` task is a script with none of the
  idempotence; when you must use one, give it `creates:` or `changed_when:`.
- **Lint it.** `ansible-lint` catches the deprecated syntax and the unsafe patterns
  before your colleagues do.

## Where it fits today

| Situation | Tool |
|---|---|
| Create cloud resources | [Terraform](/docs/devops/terraform/) |
| Configure an OS on a long-lived machine | Ansible |
| Ship an application repeatedly | A container image and [Helm](/docs/devops/helm/) |
| One-off task across 200 hosts | `ansible all -m ...`, ad hoc |

The honest summary: if your workload is containers on Kubernetes, you may never need it.
If you have servers — and most organisations do, somewhere — Ansible is how you stop
those servers being folklore.

## Next

The one thing that must never sit in a playbook in plaintext →
[Secrets management](/docs/devops/secrets/)
