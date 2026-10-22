# Performance and Security Limits

Two subjects where VM writing usually turns
into advertising. This chapter takes them at
the speed of the evidence: what is structurally
true, what depends on your setup, and what
this guide measured (one emulated boot, on a
loaded sandbox — a fact about the sandbox,
not about your desk).

## Performance: the shape of it

**The accelerator is the whole story at
first.** With VT-x/AMD-V working, the guest's
ordinary code runs on the real processor, and
a VM does ordinary work — compiling, serving,
calculating — at close to the speed the same
code would run directly on the host. Without
it, under emulation, the same work runs many
times slower; this guide's lab booted a
minimal Linux that way and it took minutes to
do what an accelerated machine does in
seconds. No other choice you make matters a
tenth as much as this one bit. Check it
(chapter 1) before you tune anything else.

**Then the pretend parts tax you.** The guest
is quickest at pure computation and slowest
where it must ask the hypervisor for
something: disk writes through a virtual
controller, packets through a virtual network
card, pixels through a virtual display. The
mitigations are a known list, in rough order
of effect:

- **Virtio devices**, where your guest
  supports them: disk and network controllers
  designed for VMs, which skip most of the
  pretending-to-be-hardware ceremony. The
  guest needs drivers for them (in the Linux
  kernel already; on Windows, installed from
  the virtio-win drivers).
- **Enough memory, not maximal memory** —
  chapter 4. A guest swapping to its virtual
  disk is slow in two directions at once.
- **The disk image on your fastest real
  disk**, in a format your hypervisor is
  happiest with, and *not* on a nearly-full
  filesystem (chapter 5).
- **Display modesty.** A VM's pretend graphics
  card is a framebuffer with good intentions.
  Lower the guest's resolution and turn off
  its visual effects before blaming the
  hypervisor; and for real graphics work, see
  chapter 9's honest passthrough discussion.

**What this guide will not give you** is a
percentage. Speed depends on the host CPU,
the disk under the image, the guest, and the
workload, in proportions nobody can state for
a machine they haven't met. Anyone who
quotes you a single overhead number for
"virtualization" is selling something.

## Security: a strong wall, not a force field

A VM is a genuinely strong boundary. The
guest's code runs against pretend hardware;
malware that wrecks the guest has wrecked a
*pretend computer*, and you can delete the
files and be done. For testing untrusted
software and opening suspicious attachments,
that is a real and large improvement over
doing it on the host. Hold on to the limits
with equal firmness:

- **The boundary has been crossed before.**
  Hypervisor escapes — bugs that let guest
  code reach the host — are rare, serious,
  and patchable, which is a long way of
  saying: keep the hypervisor updated like
  you keep the browser updated. A VM reduces
  risk; it does not repeal it.
- **The conveniences are doors.** Shared
  folders, shared clipboard, drag-and-drop,
  and USB passthrough (chapters 8 and 9) each
  punch a deliberate hole in the wall. For a
  test VM you are keeping *because* you don't
  trust what's in it, leave them off.
- **NAT is not a firewall promise.** NAT
  mode stops unsolicited inbound connections
  as a side effect of how it works (chapter
  7). The guest can still call out, download
  things, and talk to anything it can reach —
  including, on bridged networking, the rest
  of your home network.
- **A snapshot is not a clean bill of
  health.** Rewinding a guest to "before the
  bad download" also rewinds its security
  updates and its clock. And snapshots are
  not backups (chapter 6) — say it every
  time.
- **The host is the foundation.** If the
  host is compromised, every guest on it is
  a file the attacker can read. VMs protect
  the host from the guest far better than
  they protect the guest from the host.

The defensible claim, in one sentence: a
patched hypervisor, a guest with the
convenience doors shut, and backups kept
somewhere else is a solid setup for meeting
untrusted software — and none of those three
clauses is optional.
