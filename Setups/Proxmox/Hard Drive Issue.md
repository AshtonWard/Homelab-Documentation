# Hard Drive / RAID Issue

> [!NOTE]
> Incomplete historical troubleshooting note. I am keeping this because storage problems are worth documenting, but I do not have enough recorded evidence from the original incident to pretend I know the final diagnosis.

I originally opened this note after running into a problem involving a hard drive and the RAID configuration. One of the drives involved was an 8 TB WD Red.

Unfortunately, the original note stopped there.

Rather than inventing the rest of the story, this is the troubleshooting process I would use if I encountered the issue again.

## First: Do Not Make It Worse

Before changing RAID configuration:

- identify every physical disk
- identify the controller
- identify existing arrays/virtual disks
- determine whether any disk contains needed data
- avoid initializing or formatting anything until the layout is understood

Storage troubleshooting is a bad place for experimental clicking.

## Check Linux Visibility

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
```

```bash
ls -l /dev/disk/by-id/
```

This helps distinguish physical devices from partitions, logical volumes, and other storage layers.

## Check SMART Where Supported

```bash
smartctl -a /dev/<DEVICE>
```

Hardware RAID controllers may require controller-specific options or management tools before SMART data is directly available.

## Identify the Layer

```text
Physical Drive
     ↓
RAID / HBA Controller
     ↓
Linux Block Device
     ↓
LVM / ZFS / Filesystem
     ↓
Proxmox Storage
     ↓
VM / Container
```

The failure needs to be located in that stack before changing configuration.

## What I Learned

The original note is mostly useful because it reminds me **not to document a storage fix until I know exactly what fixed it**.

When I revisit this issue or encounter the next one, I want to record:

- symptoms
- controller state
- disk identifiers
- SMART evidence
- storage layout
- actual root cause
- fix
- verification

Until then, this stays intentionally incomplete rather than confidently wrong.
