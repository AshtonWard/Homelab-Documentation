# Building VLANs in OPNsense

This page started as the notes I wrote while creating the segmented network. The design changed as the lab matured, so I have rewritten it around the process and reasoning rather than preserving every old address and temporary rule.

For the current logical design, see [[../../Architecture/Network Architecture]].

## Why I Added VLANs

I did not want the homelab to become one giant trusted LAN containing hypervisors, security tooling, applications, game servers, personal devices, and intentionally vulnerable lab systems.

I separated workloads by function:

- Management
- Security
- Services
- Game Servers
- User Space
- Lab / Testing

I have considered additional segments such as IoT, cameras, and voice services, but I do not create a VLAN just because I can. It should represent a useful boundary.

## Create the VLAN

In OPNsense:

**Interfaces → Devices → VLAN**

Create the VLAN using:

- the appropriate parent interface
- the intended VLAN tag
- a useful description

Then apply the configuration.

## Assign the Interface

Go to:

**Interfaces → Assignments**

Assign the new VLAN and enable the interface.

For a routed VLAN, the OPNsense interface needs an address from that subnet. I use predictable gateway addressing, but the exact active values belong in the network configuration rather than this walkthrough.

One correction from my original notes: I had written that the interface should use DHCP while also describing manually assigning its gateway address. Those are two different approaches. For these internal routed VLAN interfaces, I want a controlled/static interface address.

## DHCP

DHCP is enabled only where the segment needs it.

The pool must **not** include:

- the gateway
- statically assigned infrastructure
- addresses reserved for servers
- other manually managed addresses

My original notes included essentially the whole subnet as a DHCP pool. That would have eventually produced an exciting game of "which device stole my server's IP?" I no longer recommend that approach.

## Firewall Rules

During initial testing, I temporarily used broad rules so I could prove the VLANs and routing worked before tightening policy.

That is useful as a troubleshooting technique, but it is not the desired final security posture.

My normal process is:

1. Verify the VLAN exists.
2. Verify the interface is configured.
3. Verify the switch transports the VLAN.
4. Verify the endpoint is on the expected VLAN.
5. Prove routing works.
6. Replace broad test access with the narrow rules actually required.

Conceptually:

```text
Source Network
      │
      ▼
Required Destination
      │
      ▼
Required Service
      │
      ▼
Explicit Allow Rule
```

I avoid documenting the complete live ruleset here. The point is to explain the design, not publish a ready-made map of the environment.

## Switching Matters

Creating a VLAN in OPNsense is only one piece.

The physical switch and Proxmox networking also have to agree about:

- VLAN IDs
- tagged/trunk links
- access ports
- allowed VLANs
- native/untagged behavior

When a VLAN does not work, I check the path rather than repeatedly clicking things in OPNsense and hoping the network becomes intimidated.

## Testing

Useful checks include:

```bash
ip addr
ip route
ping <GATEWAY>
dig <HOSTNAME>
traceroute <DESTINATION>
```

If necessary, packet capture can prove whether traffic is actually reaching the expected interface.

## What I Learned

VLANs are not the security policy by themselves.

The useful model is:

> **VLAN = boundary. Firewall rule = permission across the boundary.**

Separating the networks made the lab easier to reason about, but the real value came from becoming deliberate about which systems should be allowed to communicate.
