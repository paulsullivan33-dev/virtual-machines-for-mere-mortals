# The Neighbours

A virtual machine is one way to get "another
computer" out of the one you have. It is not
the only way, and for some jobs it is the wrong
way. This chapter places the neighbours
honestly: what each trades away, and what each
gives back.

## Proxmox VE — the type-1 neighbour

Everything in this guide used a hypervisor
that runs as a program on a desktop operating
system. **Proxmox VE** is the other shape: a
hypervisor that *is* the operating system,
installed on bare hardware, managed from a web
page, built on the same KVM and QEMU this
guide's lab used.

Choose it when a machine's whole job is
running VMs — a home lab server, a retired
desktop pressed into service as an always-on
host. Its costs are real: the host computer
stops being your desktop, the learning curve
is a server's learning curve, and backups,
storage, and networking are configured as a
server's are.

The sibling guide *Proxmox for Mere Mortals*
owns that subject, including its own first-VM
walkthrough and its containers-versus-VMs
chapter. This guide stays on the desktop side
of the fence and treats Proxmox as what it
is: the same ideas with the training wheels
off and a machine dedicated to them.

## Containers — isolation without the pretend hardware

A container (Docker is the household name)
packages a program and its libraries and runs
them in an isolated space — but on the host's
own kernel. There is no second operating
system, no virtual disk, no pretend hardware.

| | Virtual machine | Container |
|---|---|---|
| Contains | A whole OS, kernel included | A program and its libraries |
| Kernel | Its own | The host's, shared |
| Starts in | Seconds to minutes | Fractions of a second to seconds |
| Size on disk | Gigabytes | Megabytes to a few gigabytes |
| Isolation | Hardware boundary | OS namespaces and limits |
| Runs a different OS? | Yes — any the hypervisor can present | No — same kernel family only |

Choose containers when the job is "run this
service, reproducibly, on a machine like
mine." Choose a VM when the job is "run a
different operating system," "test something
that might wreck the OS," or "pretend to be a
whole separate computer." The sibling guide
*Docker for Mere Mortals* owns containers;
the Proxmox guide's containers chapter covers
LXC, Proxmox's built-in flavour.

## Dual boot — no pretence at all

Dual booting installs two real operating
systems on one machine and chooses between
them at power-on. The running OS gets the
whole machine — full speed, full hardware,
real graphics card — because there is no
pretence to maintain.

What it gives up is everything this guide is
for: no snapshots, no running both systems at
once, no trying an installer without risking
the disk layout, and a restart every time you
switch. It also cannot protect the other
system: from inside one OS, the other's files
are just files on a disk.

Choose dual boot when one workload needs the
bare hardware — typically games or graphics
work — and you can live with one OS at a
time. Choose a VM when you need safety,
reversibility, or both systems at once.

## WSL — the neighbour that moved in

Windows Subsystem for Linux (WSL2) runs a real
Linux kernel inside a lightweight utility VM
that Windows manages for you. It answers a
narrower question than this guide's: not "how
do I run another computer," but "how do I get
a Linux command line and toolchain on this
Windows machine, in seconds, without thinking
about virtual hardware at all."

It is a fine answer to that question. It is
not a general VM: you don't choose its disks,
networks, or snapshots, and it runs Linux
only. When you need any of the knobs this
guide teaches, you need a real hypervisor —
and Hyper-V, WSL's own foundation, is sitting
right there in Windows Pro and above.

## Cloud instances — someone else's hypervisor

A cloud instance (an EC2 instance, a Droplet,
an Azure VM) is a virtual machine exactly as
this guide describes — qcow2's cousins,
snapshots, virtual networks and all — running
on a provider's hardware, rented by the hour,
and managed through a web page or an API
instead of a program on your desk.

Everything you learned here transfers: sizing
is the same arithmetic with a price attached,
snapshots are still not backups, and a
security group is a NAT-and-firewall decision
wearing a different hat. What's gone is the
host — you can't watch its disks fill or pass
it a USB device, and the meter is always
running. Providers' free tiers and trials are
a reasonable way to feel a type-1-style
workflow without dedicating a machine to it.

## Emulators of other machines entirely

QEMU, this guide's lab tool, can also pretend
to be a *different kind* of computer — an ARM
board on an x86 PC, say — which is how
developers test phone and Raspberry Pi
software without the hardware. That is
emulation as a subject of its own: slower,
stranger, and genuinely useful. It is named
here so the word doesn't surprise you, and
left there; this guide's subject is VMs that
run at (nearly) native speed on hardware like
the host's own.
