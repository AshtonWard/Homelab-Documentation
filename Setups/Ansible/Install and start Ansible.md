# Building My Ansible Controller

I wanted one system to act as the configuration-management brain of the homelab. If I needed Git installed, a Wazuh agent deployed, Docker configured, or a baseline changed across several machines, I did not want the long-term answer to be "SSH into everything and do it manually."

That became `ansible01`.

> [!NOTE]
> This page started with an Ubuntu 24.04-era deployment. The lab has since evolved, so exact OS versions and resource assignments are historical rather than requirements.

## What I Wanted

The controller needed to support:

- repeatable Linux configuration
- package and dependency installation
- security tooling
- Docker host configuration
- updates and maintenance
- Git-backed automation
- future GitHub Actions integration

## Initial VM

My first controller VM was intentionally modest:

- 2 vCPU
- 4 GB RAM
- 50 GB disk
- Ubuntu Server
- OpenSSH Server enabled

Ansible itself is not particularly resource hungry. The important part is reliable connectivity to the systems it manages.

## Install the Tooling

On a Debian/Ubuntu controller:

```bash
sudo apt update
sudo apt install -y ansible openssh-client python3-pip git curl
```

Install `sshpass` only if a workflow actually requires password-based SSH. My preferred end state is key-based authentication.

Verify Ansible:

```bash
ansible --version
```

## What Came Next

Installing Ansible was the easy part. The useful work was building the surrounding structure:

```text
Ansible Controller
      │
      ├── Git repository
      ├── Inventory
      ├── host_vars
      ├── group_vars
      ├── Roles
      ├── Playbooks
      └── Vault
```

The controller eventually became part of a larger workflow where GitHub Actions can initiate operations and Ansible performs the configuration inside the lab.

## What I Learned

My original idea was "Ansible will control everything."

That was close, but not quite the architecture I ended up wanting.

Today I think of the tools as separate layers:

- **Proxmox** creates the VM.
- **Cloud-Init** gets the VM onto the network.
- **Ansible** configures the operating system.
- **Service platforms** manage their own workloads.
- **GitHub Actions** orchestrates repeatable operations.

Ansible is much more useful when I let it be excellent at configuration management instead of trying to make it impersonate every other tool in the stack.

## Related

- [[Ansible Crash Course]]
- [[Setting up for SSH]]
- [[../../Architecture/Automation Architecture]]
- [[../../Services/Ansible]]
