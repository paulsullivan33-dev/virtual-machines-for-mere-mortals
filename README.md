# Virtual Machines for Mere Mortals

A virtual machine is a computer made of
software — pretend processor, memory, disks,
and network card, convincing enough that a
real operating system installs on it and
never learns the difference. This guide is
the beginner-first tour of that idea: what a
VM is, which program should build yours, how
to make a first one, how to size it without
lying to yourself, and how its disks,
snapshots, and networks actually work.

It practises on the command line, with QEMU
— the hypervisor whose parts are all visible,
because nothing hides behind a wizard there.
Every disk command quoted was run on the
build machine, and the lab's specimen guest,
a Tiny Core Linux downloaded with its
checksums verified, was booted for real. One
honesty note belongs up front: the build
machine had no virtualization hardware
available, so that boot ran under software
emulation — slowly, and completely. The
graphical hypervisors (VirtualBox, VMware,
virt-manager, Hyper-V, Parallels, UTM) are
placed and compared in chapter 2 and taught
by their documented flows; the sibling guide
*Proxmox for Mere Mortals* owns the type-1
platform end of the subject, and this guide
treats it as a neighbour, not a topic.

Three mistakes get named in chapter 1 and
refused throughout: a **snapshot is not a
backup**, **NAT networking is a one-way
door**, and **resources given to a VM are
taken from the host**. Most beginner damage
is one of those three, arriving on schedule.

## What this guide covers

- [**What a virtual machine is**](what-a-virtual-machine-is.md) —
  host, guest, hypervisor; virtualization
  versus emulation; type 1 and type 2; the
  three mistakes; when a VM is the right tool
- [**Choosing a hypervisor**](choosing-a-hypervisor.md) —
  QEMU/KVM and virt-manager, VirtualBox,
  VMware, Hyper-V, Parallels, UTM — and what
  not to decide by
- [**Your first virtual machine**](your-first-virtual-machine.md) —
  the five decisions every first VM is made
  of, performed on the command line, and a
  real boot read from its serial log
- [**Sizing honestly**](sizing-honestly.md) —
  processors (forgiving), memory (not), disk
  (patient), and the arithmetic that keeps a
  host healthy
- [**Virtual disks and formats**](virtual-disks-and-formats.md) —
  qcow2, raw, VMDK, VDI, VHDX; virtual size
  versus disk size, both measured; growing a
  disk and the step after the step
- [**Snapshots, clones, and backups**](snapshots-clones-and-backups.md) —
  the rewind button proven at byte level, the
  second machine, and the copy that survives
- [**Networking modes**](networking-modes.md) —
  NAT, bridged, host-only; port forwarding as
  the deliberate exception
- [**Sharing and guest tools**](sharing-and-guest-tools.md) —
  guest additions and agents, shared folders
  and clipboards, and why each bridge is also
  a door
- [**Devices and passthrough**](devices-and-passthrough.md) —
  USB (the friendly case), graphics cards
  (the deep end), and the rest of the drawer
- [**Moving machines**](moving-machines.md) —
  conversions measured on the lab specimen,
  OVF/OVA export, and what doesn't survive
  the trip
- [**Performance and security limits**](performance-and-security-limits.md) —
  the accelerator is the story; a strong wall,
  not a force field
- [**Recipes**](recipes.md) — the throwaway
  test machine, the pre-update snapshot, the
  clone, the move, the retirement, the legacy
  application machine
- [**The neighbours**](the-neighbours.md) —
  Proxmox, containers, dual boot, WSL, cloud
  instances, and emulation of foreign hardware
- [**Troubleshooting**](troubleshooting.md) —
  symptom → fix: won't start, slow, networking
  riddles, disk troubles, guest complaints
- [**Glossary**](glossary.md) — the terms,
  collected in one place

## What it doesn't cover

Running a hypervisor *platform* — Proxmox VE
installation, clustering, and its web UI are
the sibling guide's. Containers as a craft
belong to *Docker for Mere Mortals*; this
guide only draws the border (chapter 13).
GPU passthrough is mapped honestly and left
as the project it is. Cloud consoles, nested
virtualization, and enterprise migration
tooling are named where relevant and not
taught.

## How to use this guide

Read chapter 1, then build along: chapters 3,
5, and 6 need nothing but `qemu-img` and
`qemu-io` (in your distribution's QEMU
packages) and the specimen commands in
`examples/commands.txt`. If you'd rather
click than type, chapter 2's table points at
your hypervisor and chapter 3 names the five
decisions its wizard will ask you — the
chapters after that are written so the ideas
transfer. Keep the transcripts
(`virtual-machines-lab/transcripts/`) as the
receipts: every quoted output in the guide
is there in full.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Proxmox for Mere Mortals](https://paulsullivan33-dev.github.io/proxmox-for-mere-mortals/)
- [Docker for Mere Mortals](https://paulsullivan33-dev.github.io/docker-for-mere-mortals/)
- [Ventoy for Mere Mortals](https://paulsullivan33-dev.github.io/ventoy-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/virtual-machines-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/virtual-machines-for-mere-mortals/blob/main/LICENSE-CODE).
