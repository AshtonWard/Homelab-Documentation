# Adding a Network Interface to Proxmox

I originally documented this almost entirely with screenshots. That worked while I remembered exactly what each screenshot meant. Future me deserves slightly better documentation than "just like it is shown."

## The Important Concept

A Proxmox Linux bridge behaves a lot like a virtual switch.

Conceptually:

```text
Physical NIC
    │
    ▼
Linux Bridge
    │
    ├── Proxmox Host
    └── Virtual Machines
```

The bridge port is the physical interface connected to that bridge.

![[Pasted image 20260624202947.png]]

## Before Changing Anything

Identify the interfaces:

```bash
ip link
```

Review the current network configuration:

```bash
cat /etc/network/interfaces
```

If I am working remotely, I am especially careful here. Breaking the management bridge while connected through the management bridge is a very efficient way to turn a software task into a walk to the server.

## Bridge Configuration

In the Proxmox UI, create or edit the Linux bridge and select the intended physical NIC as its bridge port.

![[Pasted image 20260624203037.png]]

If the Proxmox host itself needs an address on this network, configure the bridge—not the enslaved physical interface—with the appropriate host networking.

![[Pasted image 20260624203219.png]]

## VLAN-Aware Bridges

If the bridge will transport multiple VLANs to VMs, enable VLAN awareness and make sure the physical switch port is configured consistently.

A mismatch between:

- switch trunk
- allowed VLANs
- Proxmox bridge
- VM VLAN tag

can look like a routing or firewall problem even though the packet never made it that far.

## Verify

After making changes:

```bash
ip addr
ip link
ip route
```

Then verify:

- management connectivity
- gateway reachability
- DNS
- VM connectivity
- expected VLAN behavior

## What I Learned

The physical NIC is not necessarily where the Proxmox management address belongs. Once a NIC participates in a bridge, the bridge becomes the logical interface the host and VMs use.

That distinction mattered later when I started trunking VLANs into the virtualization hosts.
