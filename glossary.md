# Glossary

The terms, collected in one place. Each one is
explained where it is first used; this page is
the quick way back.

**Accelerator** — the part of a hypervisor that
uses the CPU's virtualization hardware instead
of translating every instruction in software.
KVM on Linux, Hyper-V's hypervisor on Windows,
Apple's Hypervisor framework on macOS. Without
one, a VM runs by *emulation* and is far
slower. (Chapter 1.)

**Backing file** — a disk image that another
image builds on. The top image stores only its
changes; the backing file holds the rest and
must not be modified while children exist.
(Chapter 5.)

**Bridged networking** — the guest gets its own
address on the real local network, like a second
computer plugged into the same switch. Other
devices can reach it directly. (Chapter 7.)

**Clone** — a copy of a VM: its disk, and
usually its settings, duplicated so the copy can
run as a separate machine. A full clone is
independent; a linked clone shares a backing
file with the original. (Chapter 6.)

**Container** — a program (with its libraries)
running in an isolated space on the *host's*
kernel. Not a virtual machine: there is no
second kernel and no pretend hardware.
(Chapter 1.)

**Emulation** — pretending to be hardware in
software, instruction by instruction. Works
anywhere, runs slowly. QEMU's software mode is
called TCG. (Chapter 1.)

**Guest** — the operating system running inside
a virtual machine. The machine it runs on is
the *host*. (Chapter 1.)

**Guest tools / guest additions** — software
installed *inside* the guest that makes it a
better citizen: smoother display, shared
clipboard, time sync, graceful shutdown, a
channel for the host to ask the guest questions.
VirtualBox calls its set Guest Additions;
VMware calls it VMware Tools (open-vm-tools);
QEMU's is the QEMU guest agent. (Chapter 8.)

**Host-only networking** — a private network
between the host and its guests. Guests can
reach the host and each other, and nothing
outside. QEMU's equivalent is an isolated
internal network; VirtualBox also offers a
generic *internal network* that excludes even
the host. (Chapter 7.)

**Hypervisor** — the program that creates and
runs virtual machines and divides the real
hardware among them. *Type 1* runs directly on
the hardware (Proxmox, ESXi, Hyper-V in its
server role); *type 2* runs as an application
on a host operating system (VirtualBox,
VMware Workstation, QEMU on a desktop).
(Chapter 1.)

**Image / disk image** — a file that contains a
virtual disk. Formats include **qcow2** (QEMU),
**VDI** (VirtualBox), **VMDK** (VMware), and
**VHDX** (Hyper-V), plus **raw**, the plain
bytes with no wrapper. (Chapter 5.)

**ISO** — a file containing the image of an
optical disc, nowadays almost always an
operating system's installer, attached to a VM
as if a disc were in its drive. (Chapter 3.)

**NAT (network address translation)** — the
default networking in most desktop hypervisors.
The guest can reach out to the network and the
internet; the network cannot reach in, unless a
*port forward* is configured. Like a home
router, in miniature. (Chapter 7.)

**OVA / OVF** — packaging formats for handing a
whole VM (disks plus a description of its
hardware) to someone else. An OVF is a folder
of files; an OVA is the same thing in one
archive file. (Chapter 10.)

**Overcommit** — promising guests more CPU or
memory than the host physically has, betting
they won't all demand it at once. CPU
overcommit is normal and usually safe; memory
overcommit is the risky kind. (Chapter 4.)

**Passthrough** — handing a real physical
device (a USB dongle, or a whole graphics card
via VFIO) to one guest for its exclusive use.
(Chapter 9.)

**Port forward** — a rule that says: connections
to this port on the host are handed to that
port on the guest. The deliberate exception to
NAT's one-way door. (Chapter 7.)

**Provisioning, thin and thick** — a *thin* (or
sparse, or dynamically allocated) disk file
starts small and grows as the guest writes. A
*thick* (preallocated) file takes its full size
on the host immediately. (Chapter 5.)

**Snapshot** — a saved state of a VM: the disk
(and, if taken while running, the memory and
device state) at a moment, so you can return to
it. A snapshot lives inside the VM's own files.
It is a rewind button, **not a backup**.
(Chapter 6.)

**Suspend / save state** — freezing a running VM
to disk so it can resume exactly where it left
off, the way a laptop hibernates. Distinct from
shutting the guest down and from a snapshot.
(Chapter 3.)

**TCG** — QEMU's Tiny Code Generator, its
software emulation engine. The fallback when no
hardware accelerator is available — and the
engine this guide's lab ran on. (Chapter 1.)

**Virtual disk** — the pretend hard drive inside
a VM, stored on the host as one file (the disk
image). The guest sees a disk of the size you
declared; the file on the host may be much
smaller. (Chapter 5.)

**Virtual machine (VM)** — a computer simulated
by software: pretend CPU, memory, disks, and
network card, real enough that an ordinary
operating system installs and runs on it
without knowing the difference. (Chapter 1.)

**Virtualization extensions** — CPU features
(Intel VT-x, AMD-V) that let a hypervisor run
guest code directly on the processor safely.
Nearly every desktop and server CPU of the last
fifteen years has them; the lab machine for
this guide did not expose them, which is why
its boot ran under emulation. (Chapter 1.)
