# Ansible Crash Course — Homelab Edition

This started as my cheat sheet while I was learning Ansible and gradually became one of the references I use most often. The examples are intentionally homelab-focused.

The main idea I had to learn was simple: **stop thinking in commands and start thinking in desired state.**

## The Mental Model

```text
                 Ansible Controller
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
      Proxmox Host   Docker Host   Linux VM
```

The controller runs Ansible. Targets are the systems it manages. For Linux, Ansible normally connects over SSH, performs the requested work, and reports the result. No Ansible agent is required on each target.

## Inventory

Inventory answers two questions:

1. What systems exist?
2. What configuration groups do they belong to?

My current approach uses YAML inventory plus `host_vars` and `group_vars`, rather than the `inventory.ini` layout I started with.

Example:

```yaml
all:
  hosts:
    example01:
  children:
    linux:
      hosts:
        example01:
    docker:
      hosts:
        example01:
```

A host can belong to more than one group. I try to make groups answer **"What configuration should this machine receive?"**

Useful commands:

```bash
ansible-inventory --graph
ansible-inventory --host <HOSTNAME>
ansible-inventory --list
```

## `ansible` vs. `ansible-playbook`

Use `ansible` for an ad-hoc action:

```bash
ansible <HOSTNAME> -m ping
ansible <HOSTNAME> -m command -a "uptime"
```

Use `ansible-playbook` for repeatable configuration:

```bash
ansible-playbook playbooks/bootstrap.yml --limit <HOSTNAME>
```

My shorthand:

> **Ad-hoc:** do this thing now.  
> **Playbook:** this is how this system should be configured.

## Modules

Modules are preferable to shell commands when a module already understands the resource being managed.

Instead of:

```yaml
- name: Install Git
  ansible.builtin.shell: apt install git -y
```

use:

```yaml
- name: Install Git
  ansible.builtin.apt:
    name: git
    state: present
```

Modules I commonly reach for include `apt`, `copy`, `template`, `file`, `systemd`, `user`, `git`, `uri`, `command`, and `shell`.

Look one up locally with:

```bash
ansible-doc ansible.builtin.apt
```

### `command` vs. `shell`

Use `command` when shell features are unnecessary:

```bash
ansible <HOSTNAME> -m command -a "hostname"
```

Use `shell` when pipes, redirects, expansion, or other shell behavior is actually required:

```bash
ansible <HOSTNAME> -m shell -a "cat /etc/os-release | grep VERSION"
```

For playbooks, I still prefer a purpose-built module whenever one exists.

## Anatomy of a Playbook

```yaml
---
- name: Bootstrap Linux systems
  hosts: linux
  become: true

  tasks:
    - name: Install prerequisites
      ansible.builtin.apt:
        name:
          - ca-certificates
          - curl
          - git
        state: present
        update_cache: true
```

`hosts` selects targets. `become: true` provides privilege escalation. Tasks describe desired changes, and modules perform them.

## Idempotency

This is the concept that made Ansible click for me.

I do not want automation that says:

> Run these commands every time.

I want automation that says:

> Make sure the machine looks like this.

If Docker is already installed, a good task should report `ok`, not reinstall Docker for entertainment.

Typical recap:

```text
ok=15
changed=2
unreachable=0
failed=0
```

A second run should generally produce fewer changes. Repeated unexpected changes are worth investigating.

## Safe Testing

Syntax check:

```bash
ansible-playbook playbooks/<PLAYBOOK>.yml --syntax-check
```

Preview changes where supported:

```bash
ansible-playbook playbooks/<PLAYBOOK>.yml --check --diff
```

Limit a playbook to one test host:

```bash
ansible-playbook playbooks/<PLAYBOOK>.yml --limit <TEST_HOST>
```

That last command has saved me from turning "let's see if this works" into "why did I just change the entire lab?"

## Variables

Variables let the same automation work across multiple systems.

```yaml
package_name: docker-ce
```

Referenced as:

```yaml
name: "{{ package_name }}"
```

My general split is:

- role defaults for sane reusable defaults
- `group_vars` for shared group configuration
- `host_vars` for host-specific configuration
- Vault for sensitive values

## Facts and Conditions

Gather facts:

```bash
ansible <HOSTNAME> -m setup
```

Facts include operating system, memory, processors, networking, and other host information.

Conditions let tasks react to those facts:

```yaml
when: ansible_os_family == "Debian"
```

## Loops

Instead of repeating nearly identical tasks:

```yaml
- name: Install tools
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - wget
```

For package modules that accept a list directly, passing the list to `name` is often even cleaner.

## Files and Templates

Create a directory:

```yaml
- name: Create application directory
  ansible.builtin.file:
    path: /opt/example
    state: directory
    mode: "0755"
```

Templates become useful when configuration differs by host:

```yaml
- name: Deploy configuration
  ansible.builtin.template:
    src: app.conf.j2
    dest: /etc/example/app.conf
```

## Services and Handlers

Manage a service:

```yaml
- name: Ensure Docker is running
  ansible.builtin.systemd:
    name: docker
    state: started
    enabled: true
```

Handlers let a service restart only when something actually changes:

```yaml
- name: Deploy configuration
  ansible.builtin.template:
    src: example.conf.j2
    dest: /etc/example.conf
  notify: Restart example
```

That is much better than restarting services on every run.

## Vault

Secrets do not belong in plain-text Git.

Common commands:

```bash
ansible-vault create secrets.yml
ansible-vault edit secrets.yml
ansible-vault view secrets.yml
ansible-vault encrypt secrets.yml
```

The encrypted file can be version controlled. The password used to decrypt it should not be.

## Roles

As my playbooks grew, roles became the natural next step.

```text
roles/
└── example/
    ├── tasks/
    ├── handlers/
    ├── templates/
    ├── files/
    ├── defaults/
    └── vars/
```

A playbook can then become very small:

```yaml
- name: Configure Docker hosts
  hosts: docker
  become: true
  roles:
    - docker
```

## My Homelab Automation Model

The environment has evolved toward:

```text
GitHub
   ↓
GitHub Actions
   ↓
Self-Hosted Runner
   ↓
Provision / Orchestrate
   ↓
Ansible Controller
   ↓
Managed Systems
```

GitHub Actions provides the controlled remote trigger. Proxmox handles VM lifecycle. Cloud-Init gets a new Linux VM reachable. Ansible establishes the desired configuration.

Those tools overlap a little, but I try not to make them compete for the same job.

## Troubleshooting Ansible

When Ansible fails, I work upward through dependencies:

```text
VM running?
   ↓
Network?
   ↓
DNS?
   ↓
SSH?
   ↓
Authentication?
   ↓
Inventory?
   ↓
Vault / Variables?
   ↓
Playbook?
   ↓
Role / Task?
```

Ansible is very good at telling me something failed. It is less capable of fixing DNS by sheer force of personality.

## Commands I Actually Want to Remember

```bash
ansible --version
ansible-inventory --graph
ansible-inventory --host <HOSTNAME>
ansible all -m ping
ansible <HOSTNAME> -m command -a "uptime"
ansible-playbook playbooks/<PLAYBOOK>.yml --syntax-check
ansible-playbook playbooks/<PLAYBOOK>.yml --check --diff
ansible-playbook playbooks/<PLAYBOOK>.yml --limit <HOSTNAME>
ansible-doc <MODULE>
```

## What I Learned

The biggest shift was moving from **administering individual machines** to **describing infrastructure state**.

If I catch myself writing a playbook that is just a long shell script in YAML clothing, I probably need to rethink it.

The next level is not adding more commands. It is making inventory, variables, roles, secrets, and workflows fit together cleanly enough that rebuilding a system becomes boring.

Boring rebuilds are good.
