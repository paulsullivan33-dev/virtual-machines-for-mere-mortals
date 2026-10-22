# Choosing a Hypervisor

Every hypervisor in this chapter does the
same job — pretend computers, honestly
divided hardware — and they differ in the
things around the job: what host they run on,
what they cost, how much they hide, and which
knobs they expose. There is no best one.
There is a best one *for a given desk*, and
the table below is most of the decision.

Everything in this chapter except QEMU's own
behaviour is described from the vendors'
documentation — the build machine could only
run QEMU, and only under emulation. The
ledger in `criteria.md` says so plainly.
Prices and licence terms are the ones in
force as this guide was written; they are
the most perishable facts in the book, so
check them where you download.

## The candidates

**QEMU with KVM** (Linux hosts). The engine
room. QEMU builds the pretend hardware; KVM
is the accelerator that makes it fast. On its
own, QEMU is driven entirely from the command
line — every device, disk, and network is an
option you type. That is why this guide's lab
uses it: nothing is hidden, so everything can
be explained. Most people who stay on Linux
put a face on it:

**virt-manager** (Linux hosts). A graphical
front end for QEMU/KVM through libvirt. The
wizard asks the same questions chapter 3 asks
— how much memory, how many cores, where is
the install disc — and writes the same kind
of configuration QEMU would take by hand.
Free and open source; the default choice on a
Linux desktop.

**VirtualBox** (Windows, macOS, Linux hosts).
Oracle's free type-2 hypervisor and the most
common first one, because it is the same
program on every desk and its wizard is
genuinely beginner-shaped. Two licence notes
that matter: the base program is free and
open source, while the **Extension Pack**
(USB 2/3 devices, disk encryption, a few
extras) is a separate download under a
licence that is free for personal and
evaluation use and paid for organisations.
Its pretend graphics and guest additions are
covered in chapters 8 and 9.

**VMware Workstation and Fusion** (Windows/
Linux, and macOS respectively). Broadcom's
desktop hypervisors, made free for personal
*and* commercial use in 2024 — a change worth
double-checking at download time, because it
reversed decades of paid licensing. Polished
guests, strong snapshot tooling, and the
VMDK disk format (chapter 5) are its home
ground. Support arrangements changed with
the free release; a business that needs a
support contract should read the current
terms, not this paragraph.

**Hyper-V** (Windows hosts). Microsoft's
hypervisor, built into Windows Pro,
Enterprise, and Education — notably **not**
Home. In its desktop role it behaves like a
type-2 tool (a manager program, wizards,
checkpoints — its word for snapshots), though
underneath, Windows itself becomes a guest of
the hypervisor once the feature is on, which
is why enabling Hyper-V can change how other
hypervisors on the same machine perform.
Uses VHDX disks (chapter 5). If your Windows
is Pro and you want nothing to download, it
is already there.

**Parallels Desktop** (macOS hosts,
especially Apple silicon). The commercial
default on a Mac: the smoothest Windows-on-
Mac experience, sold by subscription or
licence, with the guest tools and display
integration to match. On Apple silicon it —
like every Mac hypervisor — can only run
guests built for ARM processors, which in
practice means Windows for ARM and ARM
Linux distributions. No Mac hypervisor can
make an Apple-silicon Mac run an Intel
operating system at virtualization speed;
that is emulation, and it is slow (chapter 1).

**UTM** (macOS and iOS hosts). A friendly
face on QEMU for Apple hardware — free from
its website, paid on the App Store as a way
to support it. On Apple silicon it uses the
Hypervisor framework for real VMs and falls
back to QEMU emulation for foreign
processors. A reasonable free way to meet
chapter 3's concepts on a Mac.

## The decision, in one table

| Your desk | Sensible first choice | Because |
|---|---|---|
| Linux desktop | virt-manager (QEMU/KVM under it) | Free, native, every knob visible when you want it |
| Windows Pro or above | Hyper-V, already installed | Nothing to buy or download; or VirtualBox for one tool across desks |
| Windows Home | VirtualBox or VMware Workstation | Hyper-V isn't in the edition |
| Mac, Intel | VirtualBox, VMware Fusion, or Parallels | All run Intel guests at full speed |
| Mac, Apple silicon | Parallels (paid) or UTM (free) | ARM guests only, whichever you pick |
| No desk — a spare machine to dedicate | Proxmox (chapter 13) | That's the type-1 shape, a different guide |
| You want to see the machinery | QEMU by hand | This guide's lab; every option is a sentence you can read |

## What not to decide by

**Benchmarks of strangers' machines.** VM
speed is dominated by whether the accelerator
is on — a binary difference — and then by
your disks and memory. Between the mainstream
hypervisors on the same host with the
accelerator working, everyday differences
are small. Nobody's "37% faster" chart
survives contact with your desk.

**The disk format.** qcow2, VDI, VMDK, and
VHDX convert into each other (chapter 5 does
it live). A VM is not a marriage.

**Feature checklists for features you won't
use.** GPU passthrough, live migration
between hosts, and clustering are real and
wonderful and irrelevant to a first VM. The
knobs a beginner lives with are snapshots,
shared folders, and networking modes —
chapters 6, 8, and 7 — and every candidate
above has them.
