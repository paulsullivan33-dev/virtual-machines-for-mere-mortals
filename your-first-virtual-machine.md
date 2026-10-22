# Your First Virtual Machine

Every first VM, in every hypervisor, is the
same five decisions wearing different clothes:

1. **How much memory and how many processors**
   (chapter 4 teaches the sizing; this chapter
   just needs numbers).
2. **A virtual disk** to install onto
   (chapter 5 teaches the formats; this
   chapter just needs a file).
3. **An installer** — almost always an ISO
   file, attached as a pretend CD.
4. **A network** (chapter 7; the default is
   fine for a first machine).
5. **Boot order** — disc first, so the
   installer starts, then disk, so the
   installed system starts.

A wizard in VirtualBox, VMware, or
virt-manager asks exactly these, in roughly
this order, and the safe answer to everything
else it asks is the default it offers. Those
wizards are described from their
documentation in this guide — the build
machine has no display — but the lab performs
the same five decisions on the command line,
where each one is visible, and then boots the
result. Nothing about the ideas changes
between the two shapes.

## The lab's first machine, decision by decision

**The disk.** Chapter 5 makes this one and
keeps its receipts; the one-line version:

```
$ qemu-img create -f qcow2 specimen.qcow2 8G
Formatting 'specimen.qcow2', fmt=qcow2 cluster_size=65536 extended_l2=off compression_type=zlib size=8589934592 lazy_refcounts=off refcount_bits=16
```

An 8 GiB pretend disk that occupies 196 KiB
of the real one — thin provisioning, proven
in chapter 5.

**The installer.** Instead of an ISO, the lab
boots the smallest honest guest available:
Tiny Core Linux 16.x, whose kernel and root
filesystem the project publishes as separate
files, with checksums:

```
$ md5sum -c vmlinuz.md5.txt
vmlinuz: OK
$ md5sum -c core.gz.md5.txt
core.gz: OK
```

(Transcript `t08` — including the first
mirror answering with empty replies and the
files coming from the project's own site
instead. Verify downloads against the
publisher's checksums whenever they offer
them; an installer is a whole operating
system, and it deserves the ten seconds.)

Booting a kernel directly is a QEMU shortcut
(`-kernel`/`-initrd` below). The ISO route —
download the installer image, attach it as a
CD, boot from it, click through the guest
OS's own installer onto the virtual disk — is
the route your first real VM will take, and
it differs from the lab's only in which
bootloader starts first. The graphical
install click-through itself is the guest
vendor's to document, and is marked
documented-only in `criteria.md`.

**The machine.** One command assembles the
whole computer:

```
$ timeout 420 qemu-system-x86_64 -m 256 -smp 1 \
    -kernel vmlinuz -initrd core.gz \
    -append "console=ttyS0" \
    -display none -serial file:boot.log -monitor none \
    -netdev user,id=n0 -device e1000,netdev=n0
```

Read it as a parts list, because that is what
it is:

- `-m 256` — 256 MiB of pretend memory.
  Deliberately small: the lab guest is tiny
  and the build host was busy. Chapter 4 is
  about choosing this number honestly.
- `-smp 1` — one pretend processor.
- `-kernel` / `-initrd` / `-append` — the
  shortcut above: hand the pretend machine a
  kernel, its starter filesystem, and the
  instruction to write its console to the
  serial port.
- `-display none` — no window. The lab has no
  screen; the serial port is the console.
- `-serial file:boot.log` — everything the
  guest writes to its serial console lands in
  a file. That file is the boot's evidence.
- `-netdev user … -device e1000 …` — the
  default NAT network of chapter 7, attached
  to a pretend Intel network card.

Conspicuous by its absence: any accelerator.
Chapter 1's Check applies — the build
machine offers none, so this boot ran under
TCG emulation. On a loaded 2-core host it
reached the login line within the first few
minutes of the 420 seconds it was observed
for — a fact about emulation on a busy
sandbox, quoted as a shape, not a speed
(chapter 11).

## What a boot looks like when it works

The serial log (`t09`, kept whole) opens with
the guest kernel introducing itself — proof
the pretend machine executed a real operating
system's first instructions:

```
Linux version 6.12.11-tinycore (tc@boxdell) (gcc (GCC) 14.2.0, GNU ld (GNU Binutils) 2.43.1) #1 SMP
```

…continues through Tiny Core's own startup,
in its own words:

```
Booting Core 16.2
Loading extensions... Done.
Setting hostname to box Done.
```

…and arrives at the two lines the whole
chapter exists for — a login, and a network
card reporting its link up:

```
login[397]: root login on 'tty1'
e1000: eth0 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
```

That is a complete boot: firmware, kernel,
init system, login. Under emulation, on a
machine with no virtualization hardware at
all. On your desk, with the accelerator on,
the same sequence is the installer's first
screen — and after installing to the virtual
disk and removing the ISO, it is your new
machine's login prompt.

Check: your first VM has worked when the
*guest's own words* say so — its installer
finishing, or its login prompt — not when
the hypervisor's window opens. A window with
a firmware error in it is a machine that
failed politely (chapter 14 keeps the common
ones).

## Starting, stopping, and the third state

Three verbs, and beginners conflate two of
them:

- **Shut down** — the guest OS powers off,
  the tidy way, from inside (or by asking the
  guest agent to, chapter 8). The next start
  is a fresh boot.
- **Power off** — the hypervisor stops the
  machine the way a pulled plug stops a real
  one. For a stuck guest only; a real OS
  dislikes it exactly as much.
- **Suspend / save state** — the running
  machine is frozen to disk and can resume
  mid-thought, open windows and all. Not a
  shutdown (nothing was closed), not a
  snapshot (it holds the one live state, not
  a labelled point you can revisit twice) —
  and the saved state belongs to the
  hypervisor version that made it, which
  matters in chapter 10.

## Check

Decisions made, disk created, installer
booted, guest's own login seen, and you can
name which of the three stopping verbs you
would use before closing the laptop lid
(suspend) and which before lending the
machine's disk to chapter 6 (shut down).
