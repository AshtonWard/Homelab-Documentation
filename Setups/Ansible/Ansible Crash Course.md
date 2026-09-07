# Homelab Edition

## 1. The mental model

Think of Ansible as:

```text
                    Ansible Controller
                         ansible01
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
           pve01          pve02        bootstrap-test01
              │              │              │
              └─────── Managed targets ─────┘
```

The **controller** is where Ansible runs.

The **targets** are the machines Ansible manages.

Ansible generally:

1. Connects to a target
    
2. Executes something
    
3. Collects the result
    
4. Reports back
    

For Linux, that usually means SSH.

---

## 2. Inventory

Your inventory tells Ansible **who exists**.

Example:

```ini
[proxmox]
pve01
pve02

[ubuntu]
ansible01
wazuh01
docker01
bootstrap-test01

[network]
opnsense01
```

You can also give connection information:

```ini
[ubuntu]
ansible01 ansible_host=10.123.10.20
wazuh01 ansible_host=10.123.10.30
```

And variables:

```ini
[ubuntu:vars]
ansible_user=user
```

The inventory is essentially:

> "These are my machines, and these are the groups they belong to."

---

## 3. `ansible` vs `ansible-playbook`

This is one of the most important distinctions.

### `ansible`

Used for **ad-hoc commands**.

```bash
ansible pve01 -m ping
```

or:

```bash
ansible pve01 -m shell -a "uname -r"
```

Think:

> "Do this thing right now."

### `ansible-playbook`

Used for **repeatable automation**.

```bash
ansible-playbook bootstrap.yml
```

Think:

> "Follow this documented procedure."

That's why your Docker installation belongs in a playbook rather than you manually running `apt install docker` on every machine.

---

## 4. The commands we've been using

### Test connectivity

```bash
ansible all -m ping
```

Specific host:

```bash
ansible bootstrap-test01 -m ping
```

Multiple hosts:

```bash
ansible pve01,pve02 -m ping
```

Specific group:

```bash
ansible ubuntu -m ping
```

---

## 5. Run shell commands

We've used this one quite a bit:

```bash
ansible bootstrap-test01 -m shell -a "hostname"
```

Multiple commands:

```bash
ansible bootstrap-test01 -m shell -a "hostname; python3 --version; git --version"
```

Across multiple machines:

```bash
ansible pve01,pve02 -m shell -a "uname -r"
```

You can therefore do:

```bash
ansible all -m shell -a "uptime"
```

or:

```bash
ansible ubuntu -m shell -a "df -h"
```

or:

```bash
ansible ubuntu -m shell -a "free -h"
```

### Important

`-m shell` means:

> Use Ansible's shell module.

`-a` means:

> These are the arguments I'm giving the module.

So:

```bash
ansible wazuh01 -m shell -a "free -h"
```

is basically:

```text
ansible
  target = wazuh01
  module = shell
  arguments = "free -h"
```

---

## 6. `command` vs `shell`

You'll eventually encounter:

```bash
-m command
```

instead of:

```bash
-m shell
```

Example:

```bash
ansible ubuntu -m command -a "hostname"
```

`command` executes commands without going through a shell.

`shell` gives you shell features such as:

```bash
ansible ubuntu -m shell -a "cat /etc/os-release | grep VERSION"
```

For simple commands, prefer `command`.

For actual shell syntax, use `shell`.

But in **playbooks**, you should usually use the appropriate Ansible module instead of either.

For example, don't do:

```yaml
- name: Install Git
  ansible.builtin.shell: apt install git -y
```

Use:

```yaml
- name: Install Git
  ansible.builtin.apt:
    name: git
    state: present
```

That's a major Ansible concept.

---

## 7. Modules

Modules are Ansible's building blocks.

Some you'll use constantly:

|Module|Purpose|
|---|---|
|`ping`|Test Ansible connectivity|
|`shell`|Run shell commands|
|`command`|Run commands|
|`apt`|Manage Debian/Ubuntu packages|
|`dnf`|Manage Fedora/RHEL packages|
|`copy`|Copy files|
|`template`|Generate files from templates|
|`file`|Manage files/directories/permissions|
|`service`|Manage services|
|`systemd`|Manage systemd|
|`user`|Manage users|
|`group`|Manage groups|
|`git`|Clone/manage Git repositories|
|`lineinfile`|Modify individual config lines|
|`stat`|Check file information|
|`uri`|Interact with HTTP APIs|

You can see the documentation from your machine:

```bash
ansible-doc ansible.builtin.apt
```

or:

```bash
ansible-doc ansible.builtin.copy
```

That's a very useful command to remember.

---

## 8. Your Bootstrap Playbook

The playbook you've been working with is essentially:

```yaml
---
- name: Bootstrap Ubuntu systems
  hosts: ubuntu
  become: true

  tasks:

    - name: Install prerequisites
      ansible.builtin.apt:
        name:
          - ca-certificates
          - curl
          - gnupg
        state: present
        update_cache: true
```

Let's break that apart.

### Playbook

```yaml
---
```

YAML document start.

### Play

```yaml
- name: Bootstrap Ubuntu systems
```

Human-readable name.

### Targets

```yaml
  hosts: ubuntu
```

Run against the `ubuntu` inventory group.

### Privilege escalation

```yaml
  become: true
```

Equivalent conceptually to:

```bash
sudo
```

### Tasks

```yaml
  tasks:
```

Things Ansible should do.

### Task

```yaml
    - name: Install prerequisites
```

Human-readable task name.

### Module

```yaml
      ansible.builtin.apt:
```

Use the built-in APT module.

### Arguments

```yaml
        name:
          - ca-certificates
          - curl
          - gnupg
```

Packages to install.

---

## 9. Why Ansible is better than shell scripts

Suppose you run:

```bash
apt install docker.io
```

Ansible doesn't simply blindly execute that every time.

You tell Ansible the **desired state**:

```yaml
- name: Install Docker
  ansible.builtin.apt:
    name: docker.io
    state: present
```

Meaning:

> Docker should be installed.

If it's already installed:

```text
changed=0
```

If it isn't:

```text
changed=1
```

That's **idempotency**.

It's one of the most important Ansible concepts.

---

## 10. `changed` vs `ok`

You'll see output like:

```text
ok=5
changed=2
failed=0
```

### `ok`

Ansible checked something and it was already correct.

### `changed`

Ansible had to make a change.

### `failed`

Something went wrong.

### `unreachable`

Ansible couldn't communicate with the target.

This distinction is extremely useful when troubleshooting.

---

## 11. Running your playbook

Basic:

```bash
ansible-playbook bootstrap.yml
```

With your vault:

```bash
ansible-playbook bootstrap.yml --ask-vault-pass
```

Check syntax first:

```bash
ansible-playbook bootstrap.yml --syntax-check
```

This is something I recommend doing routinely.

---

## 12. Dry-run / check mode

One of the best Ansible features:

```bash
ansible-playbook bootstrap.yml --check
```

This asks Ansible:

> "What would you change?"

without actually making the changes where the module supports check mode.

You can also combine it with diff:

```bash
ansible-playbook bootstrap.yml --check --diff
```

Very useful before making changes to a fleet.

---

## 13. Limit where a playbook runs

Suppose:

```yaml
hosts: ubuntu
```

but you only want to test against:

```text
bootstrap-test01
```

Use:

```bash
ansible-playbook bootstrap.yml --limit bootstrap-test01
```

This is **extremely useful** for your homelab.

You can develop against:

```text
bootstrap-test01
```

before touching:

```text
wazuh01
docker01
ansible01
```

---

## 14. Tags

You can tag tasks:

```yaml
- name: Install Docker
  ansible.builtin.apt:
    name: docker.io
    state: present
  tags:
    - docker
```

Then:

```bash
ansible-playbook site.yml --tags docker
```

Or skip something:

```bash
ansible-playbook site.yml --skip-tags docker
```

As your playbooks grow, tags become very useful.

---

## 15. Variables

Instead of hardcoding:

```yaml
- name: Install package
  ansible.builtin.apt:
    name: docker.io
```

You can define:

```yaml
docker_package: docker.io
```

and:

```yaml
- name: Install Docker
  ansible.builtin.apt:
    name: "{{ docker_package }}"
    state: present
```

The:

```text
{{ variable }}
```

syntax is Jinja2.

---

## 16. Facts

Ansible automatically gathers information about machines.

Run:

```bash
ansible bootstrap-test01 -m setup
```

You'll get an enormous amount of information.

Examples include:

```text
ansible_hostname
ansible_distribution
ansible_distribution_version
ansible_processor_vcpus
ansible_memtotal_mb
ansible_default_ipv4
```

You can use facts inside playbooks.

For example:

```yaml
- name: Show OS
  ansible.builtin.debug:
    msg: "Running {{ ansible_distribution }} {{ ansible_distribution_version }}"
```

This is how you start making your playbooks intelligent.

---

## 17. Conditions

You can say:

```yaml
- name: Install Docker on Debian systems
  ansible.builtin.apt:
    name: docker.io
    state: present
  when: ansible_os_family == "Debian"
```

Now Ansible won't run the task on something like RHEL.

Another example:

```yaml
when: ansible_hostname == "wazuh01"
```

---

## 18. Loops

Instead of:

```yaml
- name: Install Git
  ansible.builtin.apt:
    name: git
    state: present

- name: Install curl
  ansible.builtin.apt:
    name: curl
    state: present

- name: Install wget
  ansible.builtin.apt:
    name: wget
    state: present
```

Do:

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

You'll use loops constantly.

---

## 19. Files

Create a directory:

```yaml
- name: Create Docker directory
  ansible.builtin.file:
    path: /opt/docker
    state: directory
    mode: "0755"
```

Create a file:

```yaml
- name: Create configuration file
  ansible.builtin.file:
    path: /opt/app/config.yml
    state: touch
```

Copy a file:

```yaml
- name: Copy configuration
  ansible.builtin.copy:
    src: config.yml
    dest: /opt/app/config.yml
```

---

## 20. Templates

This is where Ansible becomes really powerful.

You might have:

```text
templates/
    docker-compose.yml.j2
```

Inside:

```yaml
services:
  app:
    image: "{{ docker_image }}"
    ports:
      - "{{ app_port }}:8080"
```

Then:

```yaml
- name: Deploy Docker Compose file
  ansible.builtin.template:
    src: docker-compose.yml.j2
    dest: /opt/app/docker-compose.yml
```

Now the same template can produce different configurations for different machines.

---

## 21. Services

For systemd:

```yaml
- name: Ensure Docker is running
  ansible.builtin.systemd:
    name: docker
    state: started
    enabled: true
```

This means:

> Start Docker now and make Docker start automatically after reboot.

---

## 22. Handlers

This is a very important pattern.

Imagine you change an SSH configuration file.

You don't necessarily want to restart SSH every time Ansible runs.

Instead:

```yaml
- name: Update SSH configuration
  ansible.builtin.template:
    src: sshd_config.j2
    dest: /etc/ssh/sshd_config
  notify:
    - Restart SSH
```

Then:

```yaml
handlers:

  - name: Restart SSH
    ansible.builtin.systemd:
      name: ssh
      state: restarted
```

The handler only runs if the configuration task actually changed something.

That's a very Ansible-like way of thinking.

---

## 23. Become / sudo

You've already used:

```yaml
become: true
```

You can also use it on an individual task:

```yaml
- name: Install Docker
  become: true
  ansible.builtin.apt:
    name: docker.io
    state: present
```

Or globally:

```yaml
- hosts: ubuntu
  become: true
```

For your Linux fleet, I would generally use `become` at the play level when most tasks require root.

---

## 24. Vault

You've already been using:

```bash
--ask-vault-pass
```

Vault exists so you don't have to put secrets directly into Git.

For example:

```yaml
ansible_user: admin
ansible_password: supersecret
```

is obviously bad.

Instead, encrypt sensitive variables.

Common commands:

```bash
ansible-vault create secrets.yml
```

Edit:

```bash
ansible-vault edit secrets.yml
```

View:

```bash
ansible-vault view secrets.yml
```

Encrypt an existing file:

```bash
ansible-vault encrypt secrets.yml
```

Decrypt:

```bash
ansible-vault decrypt secrets.yml
```

Then:

```bash
ansible-playbook site.yml --ask-vault-pass
```

Your Git repository can contain the encrypted file without exposing the secret.

---

## 25. Useful inventory commands

List everything:

```bash
ansible-inventory --list
```

Graph the inventory:

```bash
ansible-inventory --graph
```

The graph is particularly useful for your homelab:

```text
@all
 ├── @proxmox
 │    ├── pve01
 │    └── pve02
 │
 ├── @ubuntu
 │    ├── ansible01
 │    ├── wazuh01
 │    └── docker01
 │
 └── @network
      └── opnsense01
```

---

## 26. A few commands I'd add to your toolbox

### Check Ansible version

```bash
ansible --version
```

### See configured inventory

```bash
ansible-inventory --graph
```

### Test everything

```bash
ansible all -m ping
```

### Get OS information

```bash
ansible all -m setup -a "filter=ansible_distribution*"
```

### Check disk space

```bash
ansible all -m shell -a "df -h"
```

### Check memory

```bash
ansible all -m shell -a "free -h"
```

### Check uptime

```bash
ansible all -m command -a "uptime"
```

### Check a service

```bash
ansible ubuntu -m command -a "systemctl is-active docker"
```

### Run a playbook against one machine

```bash
ansible-playbook site.yml --limit docker01
```

### Check syntax

```bash
ansible-playbook site.yml --syntax-check
```

### Preview changes

```bash
ansible-playbook site.yml --check --diff
```

---

## 27. A pattern you'll use a LOT

Your homelab will naturally evolve toward something like:

```text
Ansible
│
├── bootstrap.yml
│
├── site.yml
│
├── playbooks/
│   ├── docker.yml
│   ├── wazuh.yml
│   ├── backup.yml
│   └── hardening.yml
│
├── roles/
│   ├── docker/
│   ├── wazuh/
│   ├── hardening/
│   └── common/
│
├── templates/
│
├── files/
│
├── group_vars/
│   ├── all.yml
│   ├── ubuntu.yml
│   └── proxmox.yml
│
├── host_vars/
│   ├── wazuh01.yml
│   └── docker01.yml
│
└── inventory/
    └── hosts.yml
```

You don't need to build all of that right now.

But **that's the direction you're heading**.

---

## 28. Roles

Once a playbook gets large, you can turn functionality into a role.

For example:

```text
roles/
└── docker/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    ├── files/
    ├── defaults/
    │   └── main.yml
    └── vars/
        └── main.yml
```

Then your site playbook can simply say:

```yaml
- name: Configure Docker hosts
  hosts: docker
  become: true

  roles:
    - docker
```

That is much cleaner once you have a real fleet.

---

## 29. One concept I really want you to remember

When you're writing Ansible, ask:

> **"What state do I want this machine to be in?"**

Not:

> "What commands do I need to type?"

For example, instead of thinking:

```text
ssh docker01
apt update
apt install docker
systemctl enable docker
systemctl start docker
```

Think:

```text
docker01
    │
    ├── Docker installed
    ├── Docker running
    ├── Docker enabled
    └── Docker configured
```

Then express that desired state in Ansible.

That's the fundamental shift from **administering machines manually** to **managing infrastructure as code**.

---

## 30. Your current workflow

Based on what we've been building, I'd use this workflow:

```text
                 GitHub
                   │
                   ▼
             GitHub Actions
                   │
                   ▼
              Ansible01
                   │
             ansible-playbook
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
     PVE01        PVE02      Ubuntu VMs
                               │
                 ┌─────────────┼─────────────┐
                 ▼             ▼             ▼
              Wazuh          Docker       Other services
```

And development would look like:

```bash
# 1. Edit playbook
vim docker.yml

# 2. Validate YAML/Ansible
ansible-playbook docker.yml --syntax-check

# 3. Test against one VM
ansible-playbook docker.yml --limit bootstrap-test01 --ask-vault-pass

# 4. Verify
ansible bootstrap-test01 -m shell -a "docker --version"

# 5. Commit
git add .
git commit -m "Add Docker deployment"

# 6. Push
git push
```

Eventually, **GitHub Actions becomes the thing that performs steps 2–4**, rather than you manually doing them.

That fits very naturally with the architecture you're building.

---

# The 10 commands I'd memorize first

If you forget everything else, remember these:

```bash
ansible --version
```

```bash
ansible-inventory --graph
```

```bash
ansible all -m ping
```

```bash
ansible <host> -m shell -a "<command>"
```

```bash
ansible <group> -m ping
```

```bash
ansible-playbook <playbook>.yml
```

```bash
ansible-playbook <playbook>.yml --syntax-check
```

```bash
ansible-playbook <playbook>.yml --check
```

```bash
ansible-playbook <playbook>.yml --limit <host>
```

```bash
ansible-doc <module>
```

And the three concepts I'd prioritize learning next are **inventory/group_vars/host_vars → roles → GitHub Actions CI/CD**. Those are what will take your current Ansible setup from "I can automate a machine" to **"my homelab is infrastructure-as-code."**