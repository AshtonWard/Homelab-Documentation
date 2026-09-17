# Homelab Documentation

Technical documentation and knowledge base for my homelab environment.

This repository is maintained as an Obsidian vault and contains documentation created while building, configuring, troubleshooting, and maintaining the environment.

## Purpose

The goal of this repository is to maintain reusable technical documentation rather than track project status.

Documentation may include:

- Installation procedures
- Configuration guides
- Architecture decisions
- Networking documentation
- Troubleshooting procedures
- Command references
- Lessons learned
- Diagrams and screenshots
- Technology-specific notes

Project planning and future work are tracked separately through the Issues in my `.github` repository.

## Documentation

### Ansible

Documentation covering Ansible installation, SSH configuration, common commands, playbooks, inventory concepts, and homelab automation.

### OPNsense

Firewall, routing, DNS, DHCP, VLAN, and other network configuration documentation.

### Proxmox

Documentation for the Proxmox virtualization environment, VM configuration, templates, networking, and related infrastructure.

### Wazuh

Documentation covering the Wazuh security monitoring environment, server configuration, agents, connectivity, and troubleshooting.

### Other Setups

Additional system installation and configuration notes that don't require their own major documentation section.

## Repository Structure

```text
Homelab-Documentation/
│
├── .obsidian/
│   └── Obsidian vault configuration
│
├── Attachments/
│   └── Images and other documentation assets
│
└── Setups/
    ├── Ansible/
    ├── OPNsense/
    ├── Proxmox/
    ├── Wazuh/
    └── Other system documentation
```

The structure will expand as additional technologies are introduced into the homelab.

## Documentation vs. Automation

This repository documents how systems are designed, configured, and operated.

Infrastructure automation is maintained separately in `AnsiblePlaybooks`.

```text
Homelab-Documentation
        │
        │ explains
        ▼
   Homelab Infrastructure
        ▲
        │ configures
        │
 AnsiblePlaybooks
```

This separation keeps executable infrastructure code separate from operational and technical documentation.

## Notes

Documentation reflects a working homelab environment and may evolve as the architecture changes.

Sensitive information such as passwords, private keys, tokens, and other credentials should never be stored in this repository.
