---
weight: 7050
title: "Tailscale"
description: "A private network between your machines that ignores NAT, firewalls, and where they happen to be — WireGuard with the hard parts handled."
icon: "vpn_lock"
date: "2026-08-25"
lastmod: "2026-08-25"
draft: false
---

You have a laptop, a home server, a VPS, and a colleague who needs to see the staging box.
The traditional answers are all heavy: a VPN concentrator someone maintains, port
forwarding on a router you don't control, a jump host, a firewall exception request that
takes a week.

[Tailscale](https://tailscale.com/) makes those machines behave as if they were on one
small private network. It's WireGuard underneath, with the two things WireGuard makes you
build yourself — key exchange and NAT traversal — handled for you. Traffic goes **directly
between devices** where it can; a relay carries it only when the network refuses to
cooperate.

## Set it up

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Log in, repeat on the next machine, and they can reach each other. That's the whole
setup — no ports opened, no certificates issued, no static IPs.

```bash
tailscale status                 # who's on the tailnet, and direct or relayed
tailscale ip -4                  # this machine's 100.x address
tailscale ping laptop            # is the path direct?
ssh user@server                  # MagicDNS: the machine name just resolves
```

**MagicDNS** is the quality-of-life feature you notice immediately: devices are reachable
by name, and the name doesn't change when the network does.

## The features worth knowing on day one

**Tailscale SSH.** Let the tailnet authenticate the connection instead of managing
`authorized_keys` across machines:

```bash
sudo tailscale up --ssh
```

Access is then decided by policy, and the session can be recorded. For a fleet of small
servers this removes an entire category of key-rotation chores.

**Exit nodes.** Route all traffic through one machine — your home connection from a café,
or a VPS with a stable address for an API that whitelists IPs:

```bash
sudo tailscale up --advertise-exit-node        # on the node
sudo tailscale up --exit-node=home-server      # on the client
```

**Subnet routers.** Expose a whole network — a printer, a NAS, a legacy box that will
never run an agent:

```bash
sudo tailscale up --advertise-routes=192.168.1.0/24
```

**Serve and Funnel.** These overlap with [tunnelling](/docs/network/tunneling/), and the
difference between them is exactly one thing — the audience:

```bash
tailscale serve 3000        # HTTPS to localhost:3000, visible to your tailnet only
tailscale funnel 3000       # the same, published to the public internet
```

`serve` is how a colleague reviews your local branch without a deploy. `funnel` is the
webhook-testing case, and it's the one to be deliberate about — it puts a port on the
open internet, on one of the allowed ports (443, 8443, 10000). For an unauthenticated dev
server, prefer `serve`.

## Cost, and the shape of the free plan

The Personal plan is free and generous: several users on one tailnet, unlimited personal
devices, with limits on tagged resources and ACL groups. A homelab, a side project, and a
couple of friends fit inside it comfortably; a company doesn't, and shouldn't try.

## Four things to get right

- **Tighten the ACLs.** A new tailnet allows everything to reach everything. That's fine
  for your own three machines and wrong the moment a second person or a server joins.
  The policy file is JSON in the admin console, and reviewing it is a ten-minute job that
  ages well.
- **Use tags for servers, not user logins.** A machine authenticated as *you* stops
  working when your key expires; a tagged device doesn't.
- **Key expiry is a feature that surprises people.** Devices drop off after the expiry
  period unless you disable it per device. Set it deliberately for anything unattended.
- **It's a coordination service.** Tailscale never sees your traffic, but it does hold the
  control plane and your identity mapping. If that's unacceptable, run
  [Headscale](https://github.com/juanfont/headscale) — an open-source, self-hosted
  implementation of the control server that the official clients talk to.

## Where it fits alongside the rest

| Want | Use |
|---|---|
| Your machines to reach each other, anywhere | Tailscale |
| One local port on the public internet, briefly | [ngrok or cloudflared](/docs/network/tunneling/), or `tailscale funnel` |
| Private access to a cloud VPC | Subnet router, or your cloud's own VPN |
| An open-source mesh you fully own | Headscale, NetBird, or plain WireGuard |

The honest summary: plain WireGuard is excellent and free, and the work Tailscale does is
the part around it — discovery, keys, NAT traversal, and policy. If you've ever built that
by hand, you know what an afternoon here is worth.

## Next

For pulling data out of sites instead of pushing requests at them →
[AutoCLI](/docs/network/autocli/)
