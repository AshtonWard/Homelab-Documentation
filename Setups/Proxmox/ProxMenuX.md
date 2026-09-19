# Trying ProxMenux on My Secondary Proxmox Node

I wanted to try ProxMenux without immediately installing another management tool everywhere, so I started with a secondary Proxmox node.

That gives me somewhere to experiment without making the primary virtualization host the first volunteer for every new idea I find on the Internet.

## Installation

As usual, update first:

```bash
apt update
```

I used the project's stable installer:

```bash
bash -c "$(wget -qLO - https://raw.githubusercontent.com/MacRimi/ProxMenux/main/install_proxmenux.sh)"
```

I initially accepted the defaults.

> [!NOTE]
> A remote installer piped directly into a shell is convenient, but it also places trust in the remote project and the content being served at that moment. For higher-assurance environments, inspect or pin scripts before execution.

## First Login

After installation, ProxMenux exposed its own web interface.

I set a strong password and started exploring the monitoring and maintenance features. One of the first things that caught my attention was the security-update visibility.

## Separate from Proxmox

One detail worth remembering is that ProxMenux does not replace the native Proxmox web service.

Conceptually:

```text
Proxmox Host
   ├── Native Proxmox UI
   └── ProxMenux
```

They are separate services with separate purposes.

I intentionally do not record the live management address here. If I cannot find my own Proxmox node without a Markdown file telling me the IP, I have larger problems.

## Why I Tested It This Way

I like introducing management tooling gradually:

1. Install it on a non-primary node.
2. Understand what it changes.
3. Determine what privileges it has.
4. Decide whether it provides enough value.
5. Expand only if there is a reason.

That is safer than installing every interesting tool across the entire environment immediately.

## What I Learned

ProxMenux is useful as an additional management/monitoring layer, but Proxmox remains the authoritative virtualization platform.

The distinction matters when troubleshooting: if the helper UI says something strange, I verify the underlying Proxmox/Linux state rather than assuming the wrapper is the source of truth.

## Related

- [[../../Services/Pulse and ProxMenux]]
- [[../../Services/Proxmox]]
