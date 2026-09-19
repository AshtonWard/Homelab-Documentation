# Setting Up VLANs in Proxmox

My original note for this topic contained one sentence:

> First make sure that you have VLANs in your router.

Technically true. Not exactly the runbook of the year.

Once the homelab became properly segmented, VLAN-aware Proxmox networking became important enough to document correctly.

## The Mental Model

A VM's VLAN path can cross several components:

```text
VM
 │
 ▼
Virtual NIC
 │
 ▼
Proxmox VLAN-Aware Bridge
 │
 ▼
Physical NIC
 │
 ▼
Switch Trunk
 │
 ▼
Firewall / Router
 │
 ▼
VLAN Interface
```

Every layer has to agree about the VLAN.

## Prerequisites

Before changing Proxmox:

- the VLAN should exist in the network design
- the firewall/router should have the appropriate VLAN interface
- the switch should transport the VLAN toward the Proxmox host
- the Proxmox physical uplink should be connected to the expected trunk

Do not troubleshoot five layers simultaneously if one can be verified at a time.

## Inspect Current Networking

On the Proxmox host:

```bash
ip link
ip addr
ip route
cat /etc/network/interfaces
```

Identify:

- management bridge
- physical bridge port
- management VLAN/native behavior
- uplink interface

## Enable VLAN Awareness

For a bridge that needs to carry tagged VLAN traffic to VMs, enable **VLAN aware** in Proxmox.

The underlying configuration concept resembles:

```text
Physical NIC
    ↓
VLAN-Aware Bridge
    ↓
VMs tagged for their assigned VLAN
```

I avoid copying the live interface configuration here because NIC names and management topology are environment-specific.

## Assign a VM to a VLAN

In the VM's network device configuration, assign the appropriate VLAN tag.

The guest normally sees a regular virtual Ethernet interface. Proxmox handles the VLAN tagging unless I intentionally configure VLANs inside the guest.

## Native / Management VLAN

Management networking deserves extra care.

A host can use a native/untagged management network or a tagged management subinterface depending on the design.

The switch and Proxmox host must agree.

Changing this remotely without a fallback console is a good way to discover how quickly I can walk from my desk to the rack.

## Verify Layer by Layer

### Guest

```bash
ip addr
ip route
ping <VLAN_GATEWAY>
```

### Proxmox

```bash
bridge vlan show
ip link
```

### Switch

Verify:

- port is a trunk where required
- VLAN is allowed
- native VLAN behavior is intentional

### Firewall

Verify:

- VLAN interface exists
- gateway/interface addressing is correct
- DHCP exists if required
- firewall policy allows the intended traffic

## Common Failure Patterns

### VM cannot reach its gateway

Likely places to inspect:

- VM VLAN tag
- Proxmox bridge
- switch trunk
- firewall VLAN interface

### VM reaches gateway but not another VLAN

Now the Layer 2 path probably works. Check routing and firewall policy.

### IP works but hostname does not

Congratulations, the VLAN may be fine. Check DNS before rebuilding the network.

## What I Learned

VLAN troubleshooting became much easier once I stopped thinking of "the VLAN" as one setting.

It is a path.

If I can follow the packet from the VM to the firewall one layer at a time, the configuration stops feeling mysterious.

## Related

- [[Adding a new network card]]
- [[../OpnSense/OpnSense VLANs]]
- [[../../Architecture/Network Architecture]]
