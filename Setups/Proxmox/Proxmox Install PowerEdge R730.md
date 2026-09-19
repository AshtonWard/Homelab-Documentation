# Rebuilding My Dell PowerEdge R730 with Proxmox

> [!NOTE]
> Historical build log. This captures the rebuild that became the foundation of the current lab. Exact network values from that stage are intentionally not treated as current documentation.

At one point the homelab needed a reset.

Conveniently—or depending on how you look at it, inconveniently—the power went out for several hours. I took that as a sign to stop patching the old layout and rebuild the PowerEdge R730 properly, including the storage configuration.

The R730 became the primary Proxmox host.

## Starting with Storage

I powered the server down and entered the Dell system setup during boot.

My general path through the lifecycle/controller tooling was:

1. Enter **System Setup**.
2. Open **Device Settings**.
3. Select the RAID controller.
4. Review the existing disk configuration.
5. Reconfigure the disks for the intended layout.
6. Create the desired virtual disk/RAID configuration.
7. Verify the selected physical drives before applying changes.

> [!CAUTION]
> RAID configuration can destroy data. "Agree to all" was part of my original scratch notes because I was intentionally rebuilding from zero. That is absolutely not universal advice.

If I were following this today, I would first record:

- controller model
- physical disk identifiers
- disk health
- existing virtual disks
- data that needs preservation
- intended RAID level and why

## Install Proxmox

I wrote the Proxmox ISO to a USB drive and booted the R730 from it.

The installation itself followed the normal process:

1. Install Proxmox VE using the graphical installer.
2. Accept the EULA.
3. Verify the installation target.
4. Configure location and keyboard.
5. Set a strong administrative password.
6. Select the correct management NIC.
7. Configure the hostname and management network.
8. Complete installation and reboot.

The original build used an older management subnet. The current network design is documented elsewhere, so I do not repeat the old address as though it were still authoritative.

## Verify the Management Plane

After reboot:

- verify the host is reachable
- verify DNS
- verify the default route
- open the native Proxmox web interface
- verify storage and network interfaces

Useful local commands:

```bash
ip addr
ip route
hostname -f
pvesm status
```

## Post-Install Changes

I evaluated community Proxmox helper scripts during the build.

They can simplify repository and post-install configuration, but they are third-party code and can change over time. I no longer treat "paste this remote shell command and answer yes to everything" as durable documentation.

If I use one, I want to know:

- which project it came from
- what it changes
- whether it is still maintained
- whether those changes match my intended configuration

That is less exciting than blindly piping the Internet into Bash, but generally produces fewer surprises.

## Clustering

The R730 became part of the multi-node Proxmox environment.

I kept cluster-related configuration separate because clustering introduces Corosync, quorum, and failure modes that deserve their own documentation.

See [[Creating a Proxmox cluster]].

## Why This Rebuild Mattered

This rebuild was more than reinstalling a hypervisor.

It became the point where the lab started moving toward:

- intentional VLAN segmentation
- repeatable VM templates
- Ansible configuration management
- GitHub Actions
- centralized security monitoring
- container orchestration
- documented architecture

The R730 stopped being "the server I run stuff on" and became part of an actual infrastructure design.

## What I Learned

A clean rebuild can be faster than preserving years of accidental architecture.

The important part is making sure the new environment does not simply recreate the same undocumented decisions with newer software.

Also: if the router is a VM on the server I am shutting down, I should have a plan for how I am going to administer the server after I shut down the router.

That lesson arrived exactly as gracefully as you would expect.

## Related

- [[Creating a Proxmox cluster]]
- [[Adding a new network card]]
- [[Setting Up VLANs in Proxmox]]
- [[../../Architecture/Homelab Architecture]]
- [[../../Services/Proxmox]]
