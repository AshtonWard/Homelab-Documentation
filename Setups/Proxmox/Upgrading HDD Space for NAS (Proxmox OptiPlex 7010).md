# Adding Storage to the OptiPlex NAS

> [!NOTE]
> Incomplete project/build note. The original entry stopped immediately after installing the physical disk, so this rewrite intentionally does not claim that the storage was fully configured.

I had an extra 8 TB hard drive and wanted to add it to the storage/NAS side of the homelab.

Potential uses included backups, ISO storage, photos, files, and general bulk storage.

## Physical Installation

1. Shut down the Proxmox host cleanly.
2. Install the additional drive.
3. Boot the host.
4. Verify the disk is detected.

## Verify Before Writing

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
ls -l /dev/disk/by-id/
smartctl -a /dev/<DEVICE>
```

The goal is to be absolutely certain which physical disk I am about to modify.

## The Decision I Had Not Finished

The original note ended before I documented how the disk would actually be used.

"Add an 8 TB drive" could mean:

- standalone filesystem
- Proxmox directory storage
- ZFS
- LVM
- passthrough to a NAS VM
- backup target
- application data disk

Those choices have different tradeoffs for redundancy, snapshots, recovery, portability, and performance.

I am leaving this page at the decision point until the actual storage design is confirmed.

## Before Continuing

I need to decide:

1. Is this primary storage, backup storage, or both?
2. Does the data need redundancy?
3. Should Proxmox own the filesystem or should a NAS workload own it?
4. How will the data be backed up?
5. What happens if this node fails?
6. Do I need snapshots?
7. What filesystem best fits the workload?

## What I Learned

Finding a large disk is not the same thing as having a storage architecture.

The interesting decision starts after Linux detects the drive.

Once the project is completed, this page should record the actual design, commands, verification, and recovery considerations rather than filling in a hypothetical ending.
