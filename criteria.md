# Writing Criteria

Every guide in this repo is written to the same
standard. If you're adding or editing a guide —
human or AI — hold it to these.

1. **Plain words, real ideas.** Simple language,
   never dumbed-down concepts. Explain jargon on
   first use; collect terms in the glossary.
2. **No hype.** No marketing adjectives, no
   "revolutionary," no promises about what
   something will do for the reader.
3. **Honest about limits.** Say what the thing
   *can't* do. State uncertainty instead of
   smoothing it over. Validation notes include
   what remains unverified.
4. **No invented numbers.** Benchmarks, defaults,
   and specs are either measured or qualified with
   "depends on your setup." If it wasn't tested,
   say so.
5. **Checkable.** Commands are meant to be run,
   with "Check:" blocks showing what success looks
   like. Sources are linked, not asserted.
6. **Respect the reader's setup.** Call out
   Windows/Mac/Linux differences. Don't assume a
   GPU, a budget, or prior experience.
7. **Safety isn't skipped.** Real risks get
   stated plainly, not buried.
8. **Scoped.** Each guide says what it covers and
   points elsewhere for the rest. No guide
   pretends to be the whole subject.
9. **Structure over prose.** Tables, before/after
   examples, symptom → fix. Scannable beats essay.

## Verification notes for this guide

Standard 3 and 4 applied to this guide,
specifically. The build machine ran Linux
x86_64 (2 cores, 7.7 GiB of memory shared
with other work) with **QEMU 8.2.2 (Debian
1:8.2.2+ds-0ubuntu1.18)** — `qemu-system-x86_64`,
`qemu-img`, and `qemu-io`, installed for this
build. Working transcripts are kept with the
guide (`virtual-machines-lab/transcripts/`,
t00–t09); the lab's facts are written up in
`examples/lab-notes.txt`.

**The build machine's defining limit, stated
before everything else:** it has **no usable
hardware accelerator**. `/dev/kvm` does not
exist and `/proc/cpuinfo` shows no `vmx` or
`svm` flag (t00). Asked for KVM anyway, QEMU
refused in words the guide quotes:

```
Could not access KVM kernel module: No such file or directory
qemu-system-x86_64: -accel kvm: failed to initialize kvm: No such file or directory
```

So: **hardware acceleration is
documented-only in this build**, and every
performance-shaped statement in the chapters
is either structural (emulation translates
instructions; virtualization runs them) or
explicitly unmeasured. No timings of the
guest's work are quoted anywhere. The one
boot ran under **TCG software emulation**,
QEMU's default in KVM's absence, and chapter
3 says so where it reports it.

**Verified by running:**

- *Environment and versions (t00, t01):*
  `uname`, core count, the absent `/dev/kvm`
  (exit 2), the zero count of `vmx|svm`
  flags, `--version` for all three QEMU
  tools, `-accel help` (tcg, kvm), and the
  KVM refusal above (exit 1).
- *Capability listings (t02):*
  `-machine help`, `-cpu help`, `-display
  help` (none, gtk, sdl, egl-headless,
  curses, spice-app, dbus), and `-netdev
  help` (user, tap, bridge, socket, and
  friends) — the command surface the
  chapters describe, quoted from the tool.
- *Disk creation and inspection (t03):*
  `qemu-img create` for an 8 GiB qcow2
  (196 KiB disk size, 192 KiB file length)
  and an 8 GiB raw image (8 GiB file length,
  4 KiB disk size); `qemu-img info` on both;
  `ls -lh` and `du -h` agreeing with info.
- *Writing and checking (t04):* two 1 MiB
  `qemu-io` pattern writes (0x41 at offset 0,
  0x42 at offset 512 MiB) growing the qcow2's
  disk size to 2.32 MiB (file length
  2.38 MiB); `qemu-img check` reporting "No
  errors were found on the image" with its
  allocation counts, as quoted.
- *Conversions (t05):* qcow2 → raw, raw →
  qcow2 (round trip), and qcow2 → VMDK
  (3.1M), VDI (2.1M), VHDX (40M) with `ls -lh`
  sizes as printed in chapter 10;
  `qemu-img compare` of the source qcow2
  against its raw conversion: "Images are
  identical."
- *Resize (t06):* on a disposable copy only:
  `qemu-img resize +2G` taking virtual size
  from 8 GiB to 10 GiB (10737418240 bytes),
  confirmed by `qemu-img info` after. The
  copy was deleted afterwards.
- *Internal snapshots (t07):* on a second
  disposable copy: reading the 0x41 pattern
  back, `snapshot -c before-changes`,
  overwriting the first megabyte with 0x5a,
  reading the change, `snapshot -l` (the
  listing quoted in chapter 6, VM SIZE 0 B —
  the machine was off), `snapshot -a`
  restoring the original 0x41 bytes exactly
  as read back, and `snapshot -d` leaving an
  empty list. The copy was deleted
  afterwards.
- *The guest image (t08):* Tiny Core Linux
  16.x (x86) `vmlinuz` (5.6 MB) and `core.gz`
  (13 MB) fetched from the project's
  distribution directory, each verified with
  the project's own published `.md5.txt` —
  both "OK". The first mirror attempted
  (dist.ibiblio.org) returned empty replies
  from curl; the files came from
  tinycorelinux.net. Both facts are in the
  transcript.
- *The boot (t09):* QEMU ran the guest
  (`-m 256 -smp 1 -kernel vmlinuz -initrd
  core.gz -append "console=ttyS0"`, no
  display, serial console to a file, a
  pretend Intel e1000 card on `-netdev
  user`). The 386-line serial log shows the
  guest kernel banner (Linux
  6.12.11-tinycore), Tiny Core 16.2's
  startup completing ("Loading
  extensions... Done.", hostname set to
  `box`), a root login on tty1, and the NIC
  link coming up at 1000 Mbps full duplex.
  The run was ended by the 420-second
  timeout set on the command (QEMU exit
  124); a booted guest does not exit on its
  own, and the log's completeness before the
  stop is the result. The guest reached its
  login within the first few minutes of
  that window, on a loaded host, under
  emulation — stated as a shape in chapter
  3, not a benchmark.

**Capture hygiene (declared trims):**
transcript command echoes lose the shell's
quoting around `qemu-io -c '…'` arguments
(the commands as typed are preserved in
`examples/commands.txt`); the quotes in the
chapters restore it. Chapter 5 trims the
`qemu-img info` blocks to the lines it
discusses, with the elisions marked.
Chapter 10's `ls -lh` quote omits one line —
`specimen.raw`, a leftover of chapter 5's
work, not a conversion product. The boot log
is kept whole in t09, ANSI colour escapes,
spinner frames and all; chapter 3 quotes the
milestone lines only and says the log is
kept whole. Nothing was cut silently.

**Documented-only (not run), and why:**

- *Hardware acceleration and all performance
  behaviour under it:* no `/dev/kvm` exists
  on the build machine (above). Chapters 1
  and 11 describe the mechanism and refuse
  numbers.
- *The graphical hypervisors and their
  wizards* — VirtualBox, VMware Workstation/
  Fusion, virt-manager, Hyper-V Manager,
  Parallels, UTM: none was installed; the
  build machine has no display. Their flows,
  menu names, and feature placements in
  chapters 2, 3, 6, 8, 9, and 10 are from
  the vendors' documentation and the
  products' known behaviour, never quoted
  as captures. Licence and price statements
  in chapter 2 are the terms in force at
  writing and are labelled perishable there.
- *The ISO install walkthrough* of any real
  guest OS: the lab booted a kernel and
  rootfs directly (QEMU's `-kernel` route);
  clicking through an installer onto a
  virtual disk is the guest vendor's
  documentation, and chapter 3 marks the
  boundary.
- *Guest tools, shared folders, clipboard,
  drag-and-drop, display resizing* (chapter
  8): all require an installed graphical
  guest. None was run. The lab's only
  host–guest channel was the serial console.
- *Device passthrough, USB and GPU* (chapter
  9): no devices, no IOMMU, no accelerator.
  The chapter is a map from the hypervisors'
  documentation and says so at its head.
- *Bridged and tap networking, and the guest
  side of NAT:* only QEMU's `user` backend
  was run (in the boot command), and the
  evidence is the link-up line in the boot
  log; the guest's DHCP conversation
  happened on its tty1 console, which the
  serial log does not record, so no lease is
  quoted. QEMU user networking's default
  addresses in chapter 7 are quoted from
  QEMU's documentation as such. Bridged,
  host-only, and internal modes are from the
  vendors' documentation.
- *OVF/OVA export and import mechanics*
  (chapter 10): described from the
  hypervisors' documentation; the format
  conversions that carry the disks were run
  (t05), the packaging was not.
- *Proxmox, containers, WSL, cloud
  instances* (chapter 13): placement only,
  from their documentation and the sibling
  guides; nothing installed or run.

Specimen discipline, for the record: the
guest and every example machine in the
chapters — the test VM, the legacy-
application VM, the throwaway VM — are
invented and general; nothing is modelled
on any real person's machines,
organisations, or life.
All scratch lived under
`~/workspace/virtual-machines-build/`; the
resize and snapshot runs worked on copies
made to be deleted, and were.
