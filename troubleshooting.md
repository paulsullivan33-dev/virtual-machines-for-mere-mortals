# Troubleshooting

Symptom → likely cause → fix. The causes are
ordered by how often they are the answer,
not by how interesting they are.

## The machine won't start

**"VT-x is not available" / the hypervisor
says the accelerator couldn't be used.**
Another program owns the virtualization
hardware, or the firmware has it off. On
Windows, an enabled Hyper-V (or the
memory-integrity feature built on it) takes
the hardware first — other hypervisors then
run through Microsoft's platform layer or
complain; the fix is choosing one owner, not
fighting for two. On any platform, check the
firmware setup for the extensions (chapter
1) actually being enabled.

**QEMU says it plainly.** The lab captured
the exact words on a machine with no KVM:

```
Could not access KVM kernel module: No such file or directory
qemu-system-x86_64: -accel kvm: failed to initialize kvm: No such file or directory
```

Two cures: give the machine an accelerator
(`/dev/kvm` present, your user allowed to
use it) — or accept emulation by asking for
`-accel tcg`, knowingly, with chapter 11's
expectations set.

**"No bootable device" in the window.** The
machine is fine; it has nothing to boot.
The ISO isn't attached, the boot order puts
the empty disk first, or the install never
completed. Chapter 3's five decisions, in
order, find it.

**Black window, no error.** Often a display
setting the guest can't use, or a machine
that *did* boot to a text console your
graphical view isn't showing. Give it a
minute, try the display options (chapter 8),
and remember the guest's own words — a
serial console or the guest's logs — are the
evidence, not the window's mood.

## The machine is slow

**Everything is slow, from the first boot.**
Suspect the accelerator first (above) — the
difference is not a percentage, it is a
kind. Then chapter 4: the guest swapping
inside itself, the host swapping underneath
it, or four guests on one spinning disk.

**Fast yesterday, slow today.** What grew?
A snapshot tree being written through, a host
disk that filled into its last percent, a
guest update that replaced a virtio driver
with a pretend-hardware one (chapter 10's
guest-tools warning), or the host's own
antivirus meeting a 40 GiB disk image and
deciding to read all of it, repeatedly. Disk
images are the files to exclude from a host
scanner's real-time path, on the scanner's
own documented advice — after you've weighed
that a disk image can hold a whole machine's
worth of things to scan.

**Graphics are slow, everything else fine.**
Normal, and chapter 11: the pretend graphics
card is modest. Lower the guest's resolution
and effects; install the guest tools; if the
work is truly graphics, it's passthrough
(chapter 9) or dual boot (chapter 13)
territory.

## Networking riddles

**The guest browses fine; nothing can reach
its server.** NAT, chapter 7 — the one-way
door working as designed. Forward the port,
or bridge the card, deliberately.

**The guest has an address starting 169.254
or no address at all.** It asked for DHCP and
nobody answered: the virtual network's DHCP
service is off (VirtualBox host-only networks
want it enabled explicitly), the card is set
to a mode with no service, or the cable is —
in every hypervisor's settings, literally —
"disconnected." Check the cable checkbox
before the kernel.

**Two machines, one name, weird behaviour.**
The clone that kept its identity (chapter 6,
recipe 3). New MAC, new hostname, fresh
lease.

## Disk troubles

**The host's disk is full and the VM
paused.** A thin disk grew into a wall
(chapter 4's patient number arriving). Free
host space — never by deleting pieces of the
VM's folder, which its snapshot tree counts
as corruption — then resume. Prevention is
watching `disk size`, the real one (chapter
5).

**"The image is corrupted."** First, read
chapter 5's own tool: `qemu-img check` on a
copy of the image reports what the format
itself finds wrong, before any heroics.
Then restore the backup. Then — and only
then — the repair flags, on the copy, with
the manual open.

**The guest still shows the old disk size
after a resize.** Expected: chapter 5's step
after the step. The disk grew; the guest's
partitions haven't. Extend them from inside
the guest.

## The guest's own complaints

**The clock is wrong after every resume or
rewind.** The guest tools' time sync
(chapter 8) is the fix; a snapshot restore
also returns the clock to the snapshot's
moment, which the tools then correct.

**"This machine has been moved or copied?"**
VMware asks this of arrivals. "I copied it"
gives the machine a fresh identity — the
right answer for a clone or a move that will
coexist with its original. "I moved it"
keeps the identity, for a machine whose
original will never run again. Answering the
wrong one is chapter 6's riddle with a dialog
box.
