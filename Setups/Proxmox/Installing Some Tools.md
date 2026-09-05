1. Always update first. 
	1. `apt update`
2. Now lets installs some tools: 
	1. 
	   ```
		apt install lm-sensors
		apt install smartmontools
		apt install iperf3 -y 
			```
				This will ask you to run as a deamon, I did.
			```
		apt install ethtool
		apt install htop
		apt install tcpdump
		apt install iotop
		apt install nut -y
			```
				I have UPS so this will be useful. 
			```
	   ```
3. Admittedly i had AI write this: 
## Overview

These tools provide the Proxmox hosts with a basic command-line diagnostic and monitoring toolkit. The goal is not to install every available Linux utility, but to have a small collection of tools that can answer the most common questions when something isn't working correctly.

When troubleshooting a Proxmox host, I want to be able to determine whether the problem is related to:

- Hardware
    
- CPU or memory utilization
    
- Storage
    
- Network interfaces
    
- Network throughput
    
- Packet flow
    
- Disk I/O
    
- Power/UPS conditions
    

The Proxmox web interface provides excellent visibility into VMs, containers, storage, and overall host utilization, but these utilities provide a deeper look at what is happening underneath the Proxmox layer.

---

# Hardware Monitoring

## lm-sensors

`lm-sensors` provides access to hardware sensor information exposed by the Linux kernel. It is primarily useful for monitoring temperatures and other hardware conditions.

### Installation

```bash
apt install lm-sensors
```

### Basic usage

```bash
sensors
```

This displays the sensors detected by the system, including temperatures and other available readings.

### Use cases

`lm-sensors` is useful when:

- Checking CPU temperatures
    
- Troubleshooting overheating
    
- Verifying that cooling is functioning correctly
    
- Monitoring a heavily loaded server
    
- Checking temperatures after installing additional hardware
    
- Establishing a baseline for normal operating temperatures
    

For a server such as the Dell R730, this is particularly useful when running workloads that generate significant CPU or GPU activity.

### Example

```bash
sensors
```

If a server begins behaving strangely under heavy load, checking temperatures can help determine whether thermal conditions are contributing to the problem.
![[Pasted image 20260904221135.png|383]]

---

# Storage Health

## smartmontools

`smartmontools` provides access to SMART (Self-Monitoring, Analysis and Reporting Technology) information from supported storage devices.

This is one of the more important utilities for a homelab because storage failures can potentially result in lost VMs, containers, or data.

### Installation

```bash
apt install smartmontools
```

### Basic usage

For a SATA/SAS device:

```bash
smartctl -a /dev/sda
```

The exact device name may differ depending on the storage configuration.

### Information available

SMART information can include:

- Drive health
    
- Temperature
    
- Power-on hours
    
- Read/write error information
    
- Reallocated sectors
    
- Pending sectors
    
- Device information
    
- Self-test results
    
- Recorded failures
    

### Use cases

`smartctl` is useful when:

- Investigating a failing or suspicious drive
    
- Checking the health of used enterprise drives
    
- Establishing a baseline when adding a drive
    
- Investigating storage errors
    
- Checking drive temperatures
    
- Performing drive self-tests
    

### Example

```bash
smartctl -h
```

A useful habit is to establish a baseline when a drive is first installed. If the drive later develops errors or abnormal SMART values, there is something to compare against.

> **Note:** The device path may not always be `/dev/sda`, especially when using RAID controllers, HBAs, ZFS, or other storage configurations. Verify the actual device before running SMART commands.

---

# Network Testing

## iperf3

`iperf3` is used to measure network performance between two systems.

This is especially useful in a homelab because simply having a 1 GbE, 10 GbE, or faster link does not guarantee that the network is actually achieving the expected throughput.

### Installation

```bash
apt install iperf3
```

### Server

On one machine:

```bash
iperf3 -s
```

This starts an iperf3 server and waits for a client connection.

### Client

From another machine:

```bash
iperf3 -c <server-ip>
```

Example:

```bash
iperf3 -c 10.123.10.10
```

### Use cases

`iperf3` can be used to:

- Verify network throughput
    
- Test new network hardware
    
- Verify 1 GbE/2.5 GbE/10 GbE performance
    
- Troubleshoot unexpectedly slow connections
    
- Test VLAN connectivity
    
- Compare physical and virtual networking
    
- Test different network paths
    
- Establish a performance baseline
    

### Homelab example

A useful test could be:

```text
Proxmox R730
     |
     | 10 GbE
     |
   Switch
     |
     | 10 GbE
     |
Another Server
```

Run `iperf3 -s` on one server and `iperf3 -c <IP>` from the other.

If the link is supposed to provide 10 GbE but the test produces significantly lower throughput, further investigation can begin with `ethtool`, switch configuration, cabling, VLAN configuration, CPU utilization, and other factors.

---

# Network Interface Diagnostics

## ethtool

`ethtool` provides detailed information about Ethernet interfaces.

This is one of the most useful tools for troubleshooting physical network connectivity.

### Installation

```bash
apt install ethtool
```

### Basic usage

```bash
ethtool eno1
```

This can show information such as:

- Link detected
    
- Link speed
    
- Duplex
    
- Auto-negotiation
    
- Supported link modes
    
- Advertised link modes
    

### Driver information

```bash
ethtool -i eno1
```

This can show:

- NIC driver
    
- Driver version
    
- Firmware version
    
- PCI bus information
    

### Offload information

```bash
ethtool -k eno1
```

This displays various network offloading features.

### Use cases

`ethtool` is useful when:

- A NIC is not connecting
    
- A 10 GbE NIC is only negotiating at 1 GbE
    
- Investigating duplex problems
    
- Checking NIC drivers
    
- Checking firmware information
    
- Troubleshooting physical link issues
    
- Investigating unexpected network performance
    

### Example troubleshooting workflow

If a Proxmox host is supposed to have a 10 GbE connection but performance is poor:

```bash
ethtool eno1
```

Check:

```text
Speed:
Duplex:
Link detected:
```

Then:

```bash
ethtool -i eno1
```

Check the driver and firmware.

Finally, use `iperf3` to determine whether the connection actually achieves the expected throughput.

This creates a useful troubleshooting chain:

```text
ethtool
   ↓
Is the NIC/link configured correctly?
   ↓
iperf3
   ↓
Is the network actually performing correctly?
```

---

# System Resource Monitoring

## htop

`htop` provides an interactive view of system resources and running processes.

### Installation

```bash
apt install htop
```

### Usage

```bash
htop
```

It provides a real-time view of:

- CPU utilization
    
- Memory utilization
    
- Processes
    
- Process CPU usage
    
- Process memory usage
    
- System load
    
- Process IDs
    

### Use cases

`htop` is useful when:

- A VM appears unusually slow
    
- The host has high CPU utilization
    
- Memory usage is unexpectedly high
    
- A process is consuming excessive resources
    
- Investigating system load
    

For example, if the Proxmox GUI reports unusually high CPU utilization, `htop` can help determine which processes are actually responsible.

---

# Packet Capture & Network Troubleshooting

## tcpdump

`tcpdump` captures and displays network packets directly from a network interface.

This is one of the most powerful troubleshooting tools in this collection because it allows network traffic to be observed rather than inferred.

### Installation

```bash
apt install tcpdump
```

### Basic usage

```bash
tcpdump -i eno1
```

This captures packets seen on `eno1`.

### More readable output

```bash
tcpdump -i eno1 -n
```

The `-n` option prevents DNS lookups, which makes packet capture faster and avoids introducing additional DNS traffic into the troubleshooting process.

### Capture specific traffic

For example, DNS:

```bash
tcpdump -i eno1 port 53
```

DHCP:

```bash
tcpdump -i eno1 port 67 or port 68
```

ICMP/ping:

```bash
tcpdump -i eno1 icmp
```

### Use cases

`tcpdump` is useful for troubleshooting:

- VLANs
    
- DNS
    
- DHCP
    
- Routing
    
- Firewall rules
    
- VM connectivity
    
- Network services
    
- Unexpected traffic
    
- Failed connections
    

### Example

If a VM claims it cannot resolve DNS, rather than immediately changing DNS configuration, packet capture can help answer:

```text
Did the DNS request leave the VM?
        ↓
Did it reach the Proxmox host?
        ↓
Did it reach the DNS server?
        ↓
Did the DNS server respond?
        ↓
Did the response return?
```

This changes troubleshooting from guessing to observing what is actually happening on the network.

---

# Disk I/O Monitoring

## iotop

`iotop` displays processes that are actively performing disk I/O.

### Installation

```bash
apt install iotop
```

### Usage

```bash
iotop
```

### Use cases

`iotop` can help identify:

- Processes generating heavy disk activity
    
- Unexpected storage utilization
    
- Backup-related I/O
    
- VM-related disk activity
    
- Processes causing storage contention
    

For example, if a Proxmox host suddenly experiences high disk utilization, `iotop` can help determine whether the cause is a VM, backup process, database, logging process, or another service.

---

# UPS Management

## Network UPS Tools (NUT)

`nut` — Network UPS Tools — provides software for monitoring and managing UPS systems.

This is particularly relevant to this homelab because the Proxmox infrastructure is protected by a Vertiv PST5 UPS.

### Installation

```bash
apt install nut
```

NUT can communicate with supported UPS devices and provide information such as:

- UPS status
    
- Battery state
    
- Input voltage
    
- Output voltage
    
- Battery charge
    
- Runtime estimates
    
- Power conditions
    

More importantly, NUT can allow the Proxmox host to respond to a prolonged power outage by performing a controlled shutdown.

### Intended use

The desired behavior is:

```text
Utility Power
     |
     v
Vertiv UPS
     |
     v
Proxmox Host
     |
     v
VMs / Containers
```

During a power outage:

```text
Power Failure
     |
     v
UPS switches to battery
     |
     v
NUT detects UPS state
     |
     v
Power remains unavailable
     |
     v
Controlled Proxmox shutdown
     |
     v
VMs/containers shut down cleanly
```

This prevents the server from simply losing power when the UPS battery becomes depleted.

> **Configuration still needs to be completed for the specific Vertiv PST5 and its communication interface.** Installing NUT alone does not automatically provide a complete UPS shutdown solution.

---

# Troubleshooting Workflow

These tools become most useful when combined rather than treated as individual utilities.

A general troubleshooting workflow can be:

```text
Something isn't working
          |
          v
    Check host health
       htop
       sensors
          |
          v
    Check storage
     smartctl
     iotop
          |
          v
    Check network link
       ethtool
          |
          v
    Test throughput
       iperf3
          |
          v
   Inspect packets
      tcpdump
          |
          v
 Identify the actual failure
```

For example, if a VM suddenly has poor network performance:

1. Check the VM's network configuration.
    
2. Check the Proxmox host's NIC with `ethtool`.
    
3. Check the physical link speed and duplex.
    
4. Test throughput with `iperf3`.
    
5. Capture traffic with `tcpdump` if the problem is related to connectivity rather than throughput.
    
6. Check CPU and system load with `htop`.
    
7. Investigate storage with `iotop` if the VM's apparent network problem may actually be caused by storage contention.
    

The purpose of this toolkit is to make troubleshooting **evidence-based**. Instead of assuming that a problem is DNS, VLANs, storage, or hardware, these tools provide a way to progressively narrow down the actual cause.

---

# Quick Reference

|Tool|Primary Purpose|First Command|
|---|---|---|
|`lm-sensors`|Hardware temperatures/sensors|`sensors`|
|`smartmontools`|Drive health/SMART|`smartctl -a /dev/sda`|
|`iperf3`|Network throughput|`iperf3 -c <IP>`|
|`ethtool`|NIC/link diagnostics|`ethtool eno1`|
|`htop`|CPU/RAM/process monitoring|`htop`|
|`tcpdump`|Packet capture|`tcpdump -i eno1 -n`|
|`iotop`|Disk I/O monitoring|`iotop`|
|`nut`|UPS monitoring/shutdown|`upsc <ups-name>`|

---

# Philosophy

These utilities are intentionally kept separate from Proxmox itself. Proxmox provides the virtualization management layer, while these Linux utilities provide visibility into the underlying system.

The goal is to understand what is happening underneath the GUI:

```text
Applications
     ↓
VMs / Containers
     ↓
Proxmox
     ↓
Linux
     ↓
CPU / RAM / Storage / NIC
     ↓
Physical Hardware
```

Learning to troubleshoot at each layer is more valuable than simply learning where a setting exists in the Proxmox interface. These tools provide the foundation for diagnosing the physical host and network infrastructure that the virtualization environment depends on.