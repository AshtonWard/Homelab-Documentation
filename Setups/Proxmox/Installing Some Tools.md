# My Proxmox Diagnostic Toolkit

The Proxmox web interface is excellent for managing VMs, containers, storage, and host resources. But when something gets weird, I want tools that let me look underneath the GUI.

This is the small toolkit I install or keep available for answering questions such as:

- Is the host overheating?
- Is a disk unhealthy?
- Is the NIC negotiating at the expected speed?
- Is the network actually delivering the throughput I expect?
- Are packets reaching the interface?
- Is a process hammering the disk?
- What is consuming CPU or RAM?
- What happens when utility power disappears?

## Install

On a Debian-based Proxmox host:

```bash
apt update
apt install -y lm-sensors smartmontools iperf3 ethtool htop tcpdump iotop nut
```

I do not necessarily configure every tool immediately. Installing NUT, for example, does not magically configure a safe UPS shutdown strategy.

---

## Hardware Monitoring — `lm-sensors`

`lm-sensors` exposes hardware sensor data made available by the kernel.

```bash
sensors
```

Useful for:

- CPU temperatures
- thermal troubleshooting
- checking cooling behavior under load
- establishing a normal baseline

![[Pasted image 20260904221135.png|383]]

Not every enterprise server exposes every sensor through `lm-sensors`, so vendor management interfaces can still be important.

---

## Storage Health — `smartmontools`

SMART data can provide early evidence of drive problems.

```bash
smartctl -a /dev/<DEVICE>
```

I look for things such as:

- overall health
- temperature
- power-on hours
- reallocated sectors
- pending sectors
- recorded errors
- self-test history

The exact device path and SMART access method depend on whether the storage is behind a RAID controller, HBA, ZFS stack, or direct SATA/SAS connection.

A useful habit is checking a used drive when I install it. "It was already like that" is much easier to determine when I actually have a baseline.

---

## Network Throughput — `iperf3`

`iperf3` answers a very useful question:

> The link says it is fast. Is it actually fast?

On one system:

```bash
iperf3 -s
```

From another:

```bash
iperf3 -c <SERVER>
```

Useful for:

- validating network upgrades
- testing physical vs. virtual networking
- comparing network paths
- checking VLAN performance
- investigating unexpectedly slow transfers

If a link negotiates at the expected speed but `iperf3` performs badly, I can move on to CPU load, switching, cabling, MTU, offloads, virtualization, and other layers with actual evidence.

---

## NIC Diagnostics — `ethtool`

Basic link information:

```bash
ethtool <INTERFACE>
```

Driver and firmware:

```bash
ethtool -i <INTERFACE>
```

Offload features:

```bash
ethtool -k <INTERFACE>
```

I commonly check:

- link detected
- speed
- duplex
- auto-negotiation
- driver
- firmware

A useful sequence is:

```text
ethtool
   ↓
Is the link what I think it is?
   ↓
iperf3
   ↓
Does it perform like I think it should?
```

---

## CPU, Memory, and Processes — `htop`

```bash
htop
```

Useful when:

- a host feels slow
- CPU utilization spikes
- memory usage looks suspicious
- I need to identify a resource-heavy process

The GUI can tell me the host is busy. `htop` helps tell me **who is responsible**.

---

## Packet Capture — `tcpdump`

When I get tired of guessing whether a packet exists, I capture it.

```bash
tcpdump -i <INTERFACE> -n
```

Examples:

DNS:

```bash
tcpdump -i <INTERFACE> -n port 53
```

DHCP:

```bash
tcpdump -i <INTERFACE> -n 'port 67 or port 68'
```

ICMP:

```bash
tcpdump -i <INTERFACE> -n icmp
```

This is especially useful for VLAN, DNS, DHCP, routing, and firewall troubleshooting.

Instead of:

> "I think OPNsense is blocking it."

I can ask:

```text
Did the request leave?
      ↓
Did it cross the expected interface?
      ↓
Did the destination receive it?
      ↓
Was there a response?
      ↓
Did the response return?
```

Packet capture has ruined a lot of perfectly good theories by showing me what was actually happening.

---

## Disk I/O — `iotop`

```bash
iotop
```

Useful for identifying:

- heavy disk writers
- backup activity
- logging spikes
- VM-related I/O
- storage contention

A "network problem" can occasionally turn out to be a system waiting on storage. Looking at multiple layers prevents tunnel vision.

---

## UPS Monitoring — NUT

Network UPS Tools can monitor supported UPS hardware and coordinate controlled shutdown behavior.

Install:

```bash
apt install nut
```

The intended outcome is:

```text
Utility Power Fails
        ↓
UPS Runs on Battery
        ↓
NUT Detects State
        ↓
Configured Threshold Reached
        ↓
Controlled Shutdown
        ↓
No Surprise Power Loss
```

> [!CAUTION]
> Installing NUT alone does not create this behavior. The UPS driver, communication method, monitoring mode, shutdown logic, and testing all need to be configured for the actual hardware.

That configuration is still something I want to document separately once it is fully implemented and tested.

---

## How I Troubleshoot

The tools are more valuable together than individually.

```text
Something is wrong
       ↓
Host health
  htop / sensors
       ↓
Storage
 smartctl / iotop
       ↓
Physical network
     ethtool
       ↓
Throughput
      iperf3
       ↓
Packet flow
      tcpdump
       ↓
Actual evidence
```

I do not follow that exact order for every incident. The point is to identify the layer instead of immediately changing whatever component I happen to be looking at.

## Quick Reference

| Tool | Primary use | First command |
|---|---|---|
| `lm-sensors` | temperatures/sensors | `sensors` |
| `smartmontools` | drive health | `smartctl -a /dev/<DEVICE>` |
| `iperf3` | network throughput | `iperf3 -c <SERVER>` |
| `ethtool` | NIC/link diagnostics | `ethtool <INTERFACE>` |
| `htop` | CPU/RAM/processes | `htop` |
| `tcpdump` | packet capture | `tcpdump -i <INTERFACE> -n` |
| `iotop` | disk I/O | `iotop` |
| NUT | UPS monitoring | `upsc <UPS_NAME>` |

## What I Learned

The GUI is a management layer, not reality itself.

Underneath Proxmox are Linux, drivers, interfaces, disks, processes, and physical hardware. Learning how to inspect those layers has been more useful than memorizing where every button lives in the web interface.

```text
Applications
    ↓
VMs / Containers
    ↓
Proxmox
    ↓
Linux
    ↓
CPU / RAM / Storage / Network
    ↓
Physical Hardware
```

When something breaks, I want evidence before configuration changes.
