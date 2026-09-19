# Turning a Dell OptiPlex 7010 into a Proxmox Node

> [!NOTE]
> Historical build log. The addressing and some implementation details from this build predate the current segmented network.

I picked up a Dell OptiPlex 7010 from a thrift store and decided it deserved a second life in the homelab.

The plan was to install Proxmox, add it as another node, and use the hardware for storage/NAS-related workloads.

## Hardware Preparation

The machine originally had a smaller drive installed. During the build I decided to swap in a larger disk that I already had available.

Before installing anything, I verified that the BIOS detected the intended drive.

## Boot the Installer

On this OptiPlex:

1. Power on the system.
2. Press **F2** for BIOS/setup.
3. Verify UEFI boot configuration.
4. Select the installer USB.
5. Boot the Proxmox VE graphical installer.

During installation:

- accept the EULA
- verify the target disk
- set location/time/keyboard
- configure administrative credentials
- select the intended NIC
- configure the hostname and initial management networking

The exact address from the original build is intentionally omitted because the lab has since been redesigned.

## The NIC Problem

After installation, I could not reach the Proxmox web interface.

This became the useful part of the build.

I knew the physical cable was probably good because I could see behavior change upstream when I moved connections. From the console, I checked the interfaces:

```bash
ip link
```

The interfaces were not in the state I expected.

I then inspected Proxmox's network configuration:

```bash
cat /etc/network/interfaces
```

The key was matching the physical NIC I was actually using with the interface configured as the bridge port.

Conceptually:

```text
Physical Cable
      ↓
Correct NIC
      ↓
Proxmox Bridge Port
      ↓
Linux Bridge
      ↓
Management Connectivity
```

If necessary, an interface can be brought up for testing:

```bash
ip link set <INTERFACE> up
```

I edited the network configuration:

```bash
nano /etc/network/interfaces
```

and corrected the bridge-port assignment.

After verifying the configuration, I reloaded/restarted networking from the local console and confirmed connectivity.

> [!CAUTION]
> Restarting networking on a remote Proxmox host can disconnect the session. I prefer local console access when changing the management bridge.

## Post-Install

Once connectivity was restored, I could reach the native Proxmox web interface and continue configuration.

I also evaluated community post-install tooling. Third-party helper scripts are convenient, but I treat them as external code: review the project and understand the changes before running them.

## Why This Build Was Useful

The installation itself was ordinary. The networking failure was the valuable part.

The problem forced me to understand that a Proxmox bridge is not just a GUI setting. The physical NIC, Linux bridge, host address, switch port, and eventually VLAN configuration all have to agree.

That lesson became much more important later when the lab moved to VLAN-aware networking.

## What I Learned

When the web interface is unreachable after a Proxmox install, I now check:

1. physical link
2. `ip link`
3. bridge configuration
4. management address
5. route/gateway
6. switch configuration
7. firewall/routing upstream

"Proxmox is down" is not a diagnosis.

Sometimes Proxmox is perfectly healthy and I simply told it to use the wrong NIC.

## Related

- [[Adding a new network card]]
- [[Setting Up VLANs in Proxmox]]
- [[../../Services/Proxmox]]
