# Devices and Passthrough

A VM starts life with pretend devices: a
pretend disk, network card, display, keyboard.
Sooner or later you want it to touch a real
one — the printer, a USB security dongle, a
serial adapter, a graphics card. That act is
**passthrough**: the hypervisor steps aside
and lets the guest drive the hardware
directly. This chapter is mostly a map of
where the easy ground ends, because the
honest answer changes by device class.

Everything here is described from the
hypervisors' documentation — the build
machine had no devices to pass and no
accelerator to pass them with. The ledger in
`criteria.md` marks the whole chapter
documented-only.

## USB: the friendly case

USB passthrough is the one that usually just
works. You plug the device in, tell the
hypervisor "give this to the guest" (a menu
pick in VirtualBox, VMware, and virt-manager;
a device option in QEMU), and the guest sees
it as if it were plugged into a real machine
— because from the device's point of view, it
is. Details that bite beginners:

- **One owner at a time.** A passed device
  vanishes from the host for as long as the
  guest holds it. Pass your only keyboard and
  you will have an educational minute.
- **Speed tiers are features.** VirtualBox's
  USB 2.0/3.0 speeds live in its Extension
  Pack (chapter 2's licence note); the base
  product passes USB 1.1 speeds.
- **State doesn't travel.** Unplug mid-session
  and the guest sees a yanked cable, with
  whatever that does to the software using it.
- **Some devices are two devices.** Phones,
  dongles that first appear as a storage drive
  and then switch modes, and similar shapeshifters
  can pass the wrong personality to the guest.
  The vendor's software is the guide there,
  not this one.

## Graphics cards: the deep end

Passing a whole GPU to a guest — so it can
play games or compute at full speed — is
called VFIO passthrough on Linux, and it is
a project, not a setting. The shape of it:

- The host needs **two** graphics devices in
  practice (one to keep a display, one to
  give away), or a willingness to run the host
  headless.
- The motherboard and CPU must support an
  **IOMMU** (Intel VT-d, AMD-Vi), and the card
  must sit in its own IOMMU group so it can be
  isolated safely — the feature that makes it
  safe is the feature that makes it fussy.
- The host must be told to never load the
  card's drivers for itself.

People do this and it works well; entire
communities document it card by card. It is
placed here so you know the gap between this
and chapter 3 is a canyon with a bridge, not
a step. On Windows hosts, Hyper-V's GPU
options are narrower still, and VMware/
VirtualBox instead offer improved *pretend*
graphics, which is a different thing entirely
(chapter 8).

## The rest of the drawer

- **Serial and parallel ports** — the legacy
  application's companions. Hypervisors can
  pass a real port through, or better, a
  pretend port connected to a file or a
  network socket, which is how old industrial
  and point-of-sale software usually ends up
  living in a VM. (This guide's lab used a
  pretend serial port as the guest's console —
  the one device it could prove.)
- **Optical drives and floppies** — a real
  drive can be passed, but an ISO *file*
  attached as a pretend drive (chapter 3) is
  almost always the better tool, and the only
  one most modern hosts can offer.
- **Sound** — pretend sound cards are standard
  equipment and adequate; passing a real audio
  interface to a guest is a latency project
  nobody starts for fun.
- **Smart cards and security keys** — pass as
  USB when the key is a USB device; the
  hypervisors also offer virtual smart-card
  readers backed by the host's. Either way the
  one-owner rule applies, and a key the host
  can't see is a key the host can't use to
  unlock anything while the guest runs.

## The rule of the chapter

Passthrough spends the VM's portability and
part of its isolation (chapter 11) to buy
real hardware. Spend it where the hardware is
the point — the dongle the legacy program
checks for, the adapter nothing else speaks
to — and keep the pretend devices, which are
free and travel with the files, for
everything else.
