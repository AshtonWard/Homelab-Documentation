# Building OPNsense as My Homelab Firewall

This is the walkthrough from when I moved OPNsense into the center of the homelab network.

It began as rough installation notes. The network has changed since then, so this version focuses on what I actually built, why I made the choices, and what I would verify if I did it again.

## Why OPNsense

I wanted the firewall/router to do more than hand out addresses.

The goal was to provide:

- routing between VLANs
- firewall policy
- DHCP
- internal DNS integration
- a clear boundary between infrastructure roles

OPNsense became the Layer 3 control point for the lab.

## VM Preparation

Before starting the VM, I gave it separate network interfaces for WAN and LAN-side connectivity.

![[Pasted image 20260624194056.png]]

![[Pasted image 20260624195415.png]]

![[Pasted image 20260624195216.png]]

I originally assigned 8 GB of RAM. That is more than a basic firewall necessarily requires, but I had the capacity available and preferred not to create artificial resource constraints while I was building the network.

## Install OPNsense

Boot the installer and log in using the installer account.

I selected ZFS for the installation and otherwise stayed fairly close to the defaults.

During setup:

1. Install OPNsense.
2. Set a strong root password.
3. Assign WAN and LAN interfaces.
4. Configure WAN according to the upstream network.
5. Give the LAN-side interface a controlled internal address.
6. Leave IPv6 disabled if it is not part of the design.
7. Keep the web interface on HTTPS.

> [!NOTE]
> The original build used an older transitional LAN subnet. The current lab uses a segmented VLAN design, so that old address is intentionally not repeated as a current instruction.

## Interface Assignment

My virtual NIC names appeared as `vtnet` interfaces.

The important lesson was not the exact interface number—it was making sure the Proxmox bridge assignment and the OPNsense WAN/LAN assignment actually matched.

A beautifully configured firewall attached to the wrong virtual NIC is still a beautifully configured firewall attached to the wrong virtual NIC.

## First Web Login

Once the LAN-side interface was reachable, I connected to the OPNsense web interface and continued configuration there.

![[Pasted image 20260624203712.png|569]]

One cosmetic change I made immediately was enabling the dark theme:

**System → Settings → General → Theme**

Not architecturally important. Considerably easier on my eyes.

## DNS and DHCP

I configure DNS and DHCP as infrastructure services rather than treating them as afterthoughts.

The important rules are:

- infrastructure addresses and DHCP pools must not overlap
- stable services should have predictable addressing
- internal DNS should provide stable names
- clients should not need to know every server's address

The exact active ranges belong in the live configuration, not this build log.

## VLANs

I initially skipped VLAN creation during the console installer and configured segmentation afterward.

That made the initial setup easier to reason about:

```text
Get Firewall Reachable
        ↓
Verify WAN/LAN
        ↓
Open Web Interface
        ↓
Create VLANs
        ↓
Configure Switching
        ↓
Apply Firewall Policy
```

See [[OpnSense VLANs]] for that process.

## What I Learned

The hardest part of virtualizing a firewall is not clicking through the OPNsense installer. It is understanding where every virtual interface goes.

The troubleshooting chain is:

```text
Physical NIC
   ↓
Proxmox Bridge
   ↓
OPNsense Virtual NIC
   ↓
OPNsense Interface
   ↓
VLAN / Routing
   ↓
Firewall Policy
```

If I cannot explain that path, I am not ready to troubleshoot it.

## Related

- [[OpnSense VLANs]]
- [[../../Architecture/Network Architecture]]
- [[../../Services/OPNsense]]
