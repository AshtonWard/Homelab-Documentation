# Building My Wazuh Security Server

I wanted centralized security visibility across the homelab instead of treating every Linux host as an island.

Wazuh became the SIEM/security-monitoring platform for that job.

> [!NOTE]
> The first deployment used an older Ubuntu release and an early version of the lab's addressing plan. This walkthrough focuses on the deployment process rather than preserving those values as current instructions.

## VM Planning

My initial Wazuh VM was given considerably more resources than a normal utility VM because the stack includes components such as the manager, indexer, and dashboard.

The original build used roughly 8 vCPU, 16 GB RAM, and 128 GB disk. Those are historical choices, not universal sizing guidance. Requirements depend on agent count, retention, indexing load, and enabled features.

## Prepare the Host

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl
```

I place the Wazuh server in the security portion of the network rather than alongside ordinary application workloads.

## Install Wazuh

The general all-in-one installation pattern is:

```bash
curl -sO https://packages.wazuh.com/<VERSION>/wazuh-install.sh
sudo bash ./wazuh-install.sh -a | tee wazuh-install.log
```

I intentionally use `<VERSION>` rather than hard-coding whatever release happened to be current when I wrote the note. Before running it, I verify the current procedure against Wazuh's official documentation.

## Credentials

Installation may generate administrative credentials.

Those credentials do **not** belong in this repository. They go in the appropriate password/secret-management location.

## Verify the Stack

```bash
systemctl --type=service | grep wazuh
systemctl status wazuh-manager
```

I verify components rather than assuming a successful installer exit means the whole platform is healthy.

## DNS

I prefer agents and administrative workflows to reference the Wazuh platform through internal DNS rather than spreading a hard-coded address across the environment.

```text
Can reach the host?
      │
      ├── No → routing / firewall / host
      └── Yes
           ↓
Can resolve the name?
      │
      ├── No → DNS
      └── Yes
           ↓
Can the agent communicate?
           ↓
        Wazuh layer
```

## Agent Deployment

I eventually moved agent installation into Ansible.

```text
Ansible
   ↓
Security Role
   ↓
Wazuh Agent
   ↓
Central Wazuh Platform
```

That is a much better fit than manually repeating installation steps on every VM.

## Troubleshooting an Agent

If an agent is not reporting, I check:

1. Is the endpoint running?
2. Does DNS resolve the manager name?
3. Is routing correct?
4. Does firewall policy permit the required communication?
5. Is the agent installed?
6. Is its service running?
7. Is it configured for the intended manager?
8. What do the agent logs say?
9. What does the manager say?

I try not to reinstall the agent until I know the agent installation is actually the broken layer.

## Resource Lesson

Wazuh taught me quickly that "it's just another Linux VM" is not a useful sizing strategy for every service.

The indexer can consume meaningful memory and CPU, and telemetry grows over time. I need to think about endpoint count, event volume, retention, disk growth, memory, and index health.

## What I Learned

The interesting part of Wazuh was not installing the dashboard.

It was integrating security monitoring into the architecture: a dedicated security network, controlled cross-VLAN communication, DNS-based discovery, automated agent deployment, and centralized visibility.

That turned Wazuh from another dashboard into part of the infrastructure.

## Related

- [[../../Services/Wazuh]]
- [[../../Services/Ansible]]
- [[../../Architecture/Network Architecture]]
