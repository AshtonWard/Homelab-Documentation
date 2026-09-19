# Creating My Proxmox Cluster

> [!CAUTION]
> Clustering is not just a UI convenience. Corosync and quorum become part of the health of the environment. I have learned the hard way that cluster configuration deserves more respect than "I'll just delete this file and see what happens."

I wanted multiple Proxmox nodes to appear and operate as one cluster so I could manage them together and experiment with multi-node features.

## Before Creating the Cluster

Each node should have:

- a unique hostname
- working management networking
- reliable name resolution
- synchronized time
- compatible Proxmox versions where practical
- stable communication between cluster nodes

Verify connectivity in both directions.

```bash
ping <OTHER_NODE>
```

Check hostname resolution:

```bash
getent hosts <OTHER_NODE>
```

## Name Resolution

If local host entries are required, verify `/etc/hosts` carefully.

The node hostname, FQDN, and management address must agree with the intended environment.

I prefer reliable DNS rather than maintaining unnecessary duplicate mappings.

## Create the Cluster

On the node that will create the cluster:

```bash
pvecm create <CLUSTER_NAME>
```

Check status:

```bash
pvecm status
```

## Join Another Node

On the node being added:

```bash
pvecm add <EXISTING_CLUSTER_NODE>
```

Follow the prompts and then verify cluster membership:

```bash
pvecm nodes
pvecm status
```

## Quorum Matters

A small homelab cluster is not magically highly available because it contains two machines.

Quorum behavior needs to be understood before treating the cluster as production-like HA.

Before changing Corosync or forcibly removing cluster configuration, I now stop and understand the consequences first.

## What I Learned

Earlier in the lab I damaged cluster state while manually removing Corosync configuration and ended up with storage/LVM consequences I did not expect.

That experience permanently upgraded this procedure from:

> "Here are the three commands."

to:

> "Understand the cluster state before touching the cluster state."

The commands are easy. Recovery from an unnecessary cluster disaster is significantly less entertaining.

## Related

- [[../../Services/Proxmox]]
- [[../../Architecture/Homelab Architecture]]
