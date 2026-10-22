# Sharing and Guest Tools

Out of the box, a VM is an island: files on
the host are invisible to the guest, the
clipboard stops at the window's edge, and
shutting the machine down from outside is
like pulling its plug. This chapter covers
the bridges — each of which is also a door in
the wall chapter 11 asks you to think about —
and the software that builds them.

The bridges are installed *inside* the guest,
so on the build machine — no display, no
accelerator, a guest that booted to a text
console — none of them could be exercised.
This chapter is documented from the
hypervisors' own manuals, and `criteria.md`
marks it so. The one bridge the lab did use
was the pretend *serial* console, which is
how chapter 3's boot was read.

## Guest tools: the installation that changes everything

Every hypervisor ships a set of **guest
tools**: drivers and small services you
install in the guest.

- **VirtualBox Guest Additions** — an ISO
  that ships with VirtualBox and is mounted
  in the guest from the Devices menu.
- **VMware Tools** — for most guests, the
  open-source `open-vm-tools` package from
  the guest's own repositories is the
  recommended route; VMware's installer ISO
  remains for others.
- **QEMU guest agent** (`qemu-guest-agent`) —
  a small daemon from the guest's package
  manager; spice-vdagent adds the desktop
  comforts on Linux guests using SPICE.
- **Hyper-V integration services** — built
  into modern Windows and mainstream Linux
  kernels already; there is usually nothing
  to install, only services to check.

What they buy, roughly in the order a
beginner notices:

1. **A pointer that behaves.** No more
   "capture" and a key combination to escape
   the window; the mouse crosses the border
   like any other window's.
2. **A display that fits.** The guest's
   resolution follows the window size instead
   of being a fixed pretend monitor.
3. **Shared clipboard and drag-and-drop** —
   in both directions, each switchable, each
   a door (see below).
4. **Time sync** with the host, so a resumed
   or rewound guest isn't living in yesterday.
5. **Graceful shutdown and information** —
   the host can ask the guest to shut down
   properly, read its IP address, and (with
   the QEMU agent) freeze filesystems briefly
   so a snapshot is consistent.

If a VM feels crude, the guest tools are
almost always the missing piece. Install them
before you judge the hypervisor.

## Shared folders

The deliberate bridge for files: you name a
folder on the host, and it appears inside the
guest as a network-like share. VirtualBox,
VMware, and Parallels each have their own,
arriving with the guest tools. QEMU's answers
are **virtio-fs** (a shared directory that
looks like a local filesystem to the guest,
the modern choice on Linux hosts and guests)
and the older **9p** filesystem share.

Honest notes:

- A shared folder is **not fast storage** and
  not a place to run a database or a build
  tree from if you can avoid it; move work
  into the guest's own disk for real work.
- It is a standing open door between host and
  guest. Ransomware in the guest can see
  everything that share can see. Share the
  project folder, not your home directory.
- Permissions and case-sensitivity differ
  across the bridge; a file tree that is fine
  on one side can be quietly wrong on the
  other. Version control inside the guest was
  invented for this.

## The other bridges, briefly

- **Networking as a file channel.** The
  unglamorous classic: the guest talks to the
  host over its virtual network (`scp`, a
  shared drive elsewhere, a git remote).
  No tools needed, works in every mode that
  lets the guest see the host, and leaves
  nothing standing open. Chapter 7's modes
  decide what's reachable.
- **Clipboard and drag-and-drop** arrive with
  the guest tools and default differently per
  hypervisor. Both can be set one-way or off;
  for a guest you don't trust, off is the
  setting, and chapter 11 says why.
- **Shared nothing.** For the throwaway test
  VM — the one meeting software you don't
  trust — the right configuration is often no
  bridges at all: install from ISO, test,
  delete. The island was the point.

## Check

On a VM you own, you should be able to say
which bridges are open — tools installed?
clipboard which way? which folder shared? —
without opening the settings to look. If you
can't, the settings are the chapter: open
them, and close what you don't use.
