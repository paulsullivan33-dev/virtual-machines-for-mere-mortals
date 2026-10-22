# What a Virtual Machine Is

A virtual machine is a computer made of
software. It has a processor, memory, disks, a
network card — all pretend, all described by
files and settings — and it is convincing
enough that an ordinary operating system can
be installed on it and runs without ever
learning the difference. The real computer it
runs on is the **host**; the operating system
inside is the **guest**; the program that
builds and runs the pretend computer is the
**hypervisor**.

That is the whole idea. Everything else in
this guide is consequence: because the
machine is software, it can be paused,
copied, rewound, and deleted like a file.
Because it is a *computer*, it can run a
completely different operating system from
the host's, crash without taking the host
down, and be given as much or as little of
the real hardware as you choose.

## Two ways to pretend

A hypervisor can build the pretend computer
two ways, and the difference decides whether
a VM feels like a real machine or a museum
piece.

**Virtualization** uses hardware help. Modern
processors (Intel's VT-x, AMD's AMD-V — nearly
every desktop and server CPU of the last
fifteen years) have a mode that lets guest
code run *directly on the real processor*,
with the hypervisor stepping in only when the
guest touches something sensitive. The guest
runs at close to native speed. The piece of
software that talks to this hardware mode is
the **accelerator**: KVM on Linux, Hyper-V on
Windows, the Hypervisor framework on macOS.

**Emulation** is the fallback: software
translates every guest instruction into host
instructions, one at a time. It works anywhere
— it can even pretend to be a different kind
of processor entirely — but it is typically
many times slower. QEMU, the tool this
guide's lab uses, calls its emulator TCG.

> **The lab's honest position.** The machine
> this guide was built on had no usable
> accelerator: no `/dev/kvm`, no VT-x/AMD-V
> flags exposed. So the lab's one boot ran
> under emulation — slowly, and far enough to
> prove the machine works. On your own
> hardware, with the accelerator on, the same
> steps are the same; only the speed is
> different. Chapter 11 talks about what that
> speed difference does and doesn't mean.

Check what your own machine offers before you
believe any performance claim, including this
guide's silences:

- Linux: `ls /dev/kvm` (a character device,
  present and writable means KVM is ready)
  and `grep -o -m1 -E 'vmx|svm' /proc/cpuinfo`
  (either word means the CPU has the
  extensions).
- Windows: Task Manager → Performance → CPU
  shows "Virtualization: Enabled" when VT-x is
  on and available.
- macOS: every Apple-silicon Mac and every
  Intel Mac of the last decade and a half has
  the extensions; the question there is which
  hypervisor you choose, not whether you can.

## Type 1 and type 2 hypervisors

Hypervisors come in two shapes.

A **type 1** (bare-metal) hypervisor *is* the
operating system. It installs on the empty
machine, owns the hardware, and every
"computer" on that machine — including the
management console — is a guest. Proxmox VE,
VMware ESXi, and Hyper-V in its server role
are type 1. This shape owns a machine; the
sibling guide *Proxmox for Mere Mortals*
covers it.

A **type 2** (hosted) hypervisor is an
ordinary program. You install VirtualBox,
VMware Workstation or Fusion, or QEMU on the
laptop you already use, and VMs run alongside
your other windows. That is this guide's
shape: your first VM on the computer you have.

Neither shape is the "real" one. They are the
same idea wearing different amounts of the
host.

## The three mistakes this guide refuses to let you make

Three confusions cause most beginner damage.
They are stated here, early, and again where
they belong:

1. **A snapshot is not a backup.** A snapshot
   lives inside the VM's own disk files. If
   the host's disk dies, the snapshot dies
   with the machine it was protecting. A
   backup is a copy *somewhere else*.
   (Chapter 6.)
2. **NAT networking is a one-way door.** With
   the default NAT mode, your guest can reach
   the internet, and the internet — and the
   other computers on your network — cannot
   reach your guest, until you deliberately
   forward a port or switch modes.
   (Chapter 7.)
3. **Resources given to a VM are taken from
   the host.** A guest with 8 GB of memory
   and 4 cores leaves the host 8 GB and those
   cores poorer while it runs. A VM is not
   free capacity; it is a partition of what
   you have. (Chapter 4.)

## When a VM is the right tool — and when it isn't

| The job | VM? | Why |
|---|---|---|
| Try a different operating system | Yes | Reversible, deletable, no disk surgery |
| Test software you don't trust yet | Yes | Breaks the guest, not the host; rewind with a snapshot |
| Run one old program that needs an old OS | Yes | The legacy-application VM is a classic |
| Run a service like a home server | Often | A container may be lighter for the same job (chapter 13) |
| Run many copies of the same small service | Usually not | Containers do this at a fraction of the weight |
| Play demanding games, edit video at full speed | Usually not | The pretend graphics card is the bottleneck; dual boot or passthrough territory (chapters 9, 13) |
| Browse the web a little more safely | Partly | A VM contains damage better than a tab does; it is not invisibility (chapter 11) |

## The lab

This guide's claims are practised on one
specimen: an invented general-purpose test
machine, created with QEMU — the hypervisor
whose parts are all on the command line,
where nothing hides behind a wizard. Its disk
operations, format conversions, snapshots,
and one emulated boot were run and captured
on the build machine; the transcripts are
kept in `virtual-machines-lab/transcripts/`
and the lab's facts are written up in
`examples/lab-notes.txt`. The graphical
walkthroughs of the friendlier hypervisors —
VirtualBox's wizard, VMware's, virt-manager's
— are described from their documentation and
marked as such in `criteria.md`, because the
build machine has no display and no
accelerator to run them honestly.

The specimen guest is Tiny Core Linux, chosen
because it is tiny and publishes checksums —
small enough for an emulator to boot in a
sandbox, honest enough to verify on the way
in. Your first real VM should be whatever
system you actually want to meet.

## Check

You understand the shape of the subject if
you can say, without looking: which program
on your machine would be the hypervisor, what
its accelerator is called, and which of the
three mistakes above is the one your current
backup plan is making.
