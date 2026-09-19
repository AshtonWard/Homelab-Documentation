# Installing Kali Linux on an HP Notebook 2000

> [!NOTE]
> Historical build log. This documents one of my physical Kali installations rather than a current universal Kali deployment standard.

I had an older HP Notebook 2000 available and decided to turn it into a dedicated Kali Linux machine. It was a good use for hardware that otherwise was not doing much, and it gave me a physical system for lab/testing work instead of making every experiment a VM.

## What I Used

- HP Notebook 2000
- USB flash drive
- Kali Linux installer image
- Balena Etcher to write the image

## Installation

I wrote the Kali image to the USB drive, inserted it into the notebook, and rebooted.

On this HP system:

1. Press **Esc** during startup.
2. Press **F9** for Boot Device Options.
3. Select the USB drive.
4. Choose **Graphical Install**.

From there I worked through the normal installer:

- language
- location
- keyboard
- network interface
- hostname/user creation
- timezone
- disk partitioning
- software selection

For this machine I used the entire target disk with a simple single-partition layout.

The important part of that step is verifying the selected target disk before writing changes. Accidentally selecting the installer USB would be inconvenient; selecting the wrong data disk would be considerably less funny.

After installation, I rebooted into Kali and performed the normal package update.

```bash
sudo apt update
sudo apt full-upgrade -y
```

## Network Placement

The original installation was performed before the current network design was finalized.

A security-testing system should be placed deliberately in an isolated lab/testing segment rather than casually connected to management or service infrastructure.

## What I Learned

The actual Kali installation was straightforward. The more important design decision was **where a security-testing machine belongs**.

A penetration-testing distribution is useful because it contains tools capable of doing things I explicitly do not want happening accidentally against normal infrastructure.

The OS is the easy part. Containment is the architecture problem.
