# Moving Machines

A VM is files, and files travel. Moving a
machine to another computer — or from one
hypervisor to another — is a copy, a possible
format conversion, and a short list of
things that don't survive the trip. The lab
performed the conversions on its specimen
(`t05`); the journeys around them are
described from the hypervisors' export
documentation, and `criteria.md` keeps that
ledger.

## The conversion bench

`qemu-img convert` reads and writes every
format in chapter 5's table. The lab's
specimen — the 8 GiB qcow2 holding 2 MiB of
patterns — became all of them:

```
$ qemu-img convert -f qcow2 -O raw specimen.qcow2 converted.raw
$ qemu-img convert -f raw -O qcow2 converted.raw roundtrip.qcow2
$ qemu-img convert -f qcow2 -O vmdk specimen.qcow2 specimen.vmdk
$ qemu-img convert -f qcow2 -O vdi specimen.qcow2 specimen.vdi
$ qemu-img convert -f qcow2 -O vhdx specimen.qcow2 specimen.vhdx
$ ls -lh specimen.qcow2 converted.raw roundtrip.qcow2 specimen.vmdk specimen.vdi specimen.vhdx
-rw-r----- 1 root nogroup 8.0G Oct 22 00:16 converted.raw
-rw-r----- 1 root nogroup 2.4M Oct 22 00:16 roundtrip.qcow2
-rw-r----- 1 root nogroup 2.4M Oct 22 00:16 specimen.qcow2
-rw-r----- 1 root nogroup 2.1M Oct 22 00:16 specimen.vdi
-rw-r----- 1 root nogroup  40M Oct 22 00:16 specimen.vhdx
-rw-r----- 1 root nogroup 3.1M Oct 22 00:16 specimen.vmdk
```

Read the table the way chapter 5 taught:
`converted.raw` has the full 8.0G *length*
(the guest's bytes, laid out flat) while
occupying little on a sparse-aware
filesystem; the others keep their thin
character, each with its own overhead —
VHDX's 40M on a 2 MiB payload is its block
allocation granularity, not your data
multiplying. And the round trip is proven,
not assumed:

```
$ qemu-img compare -f qcow2 -F raw specimen.qcow2 converted.raw
Images are identical.
```

`compare` checks the guest-visible bytes, so
"identical" means the machine you moved is
the machine you had — every conversion in
this chapter should end with either that
word or the target hypervisor booting the
result, and preferably both.

## The disk is not the machine

A converted disk still needs a machine
around it: memory, processors, network,
firmware settings. Two ways to carry those:

- **Copy the whole folder** when the
  destination runs the same hypervisor — the
  configuration file travels with the disks,
  and the machine arrives whole. Between two
  VirtualBox or two VMware installs, this is
  the whole job, plus the identity courtesy
  of chapter 6 (a fresh MAC address; the
  hypervisor will offer).
- **Export and import** when the hypervisor
  changes. The interchange packaging is
  **OVF** (a folder: a descriptor plus the
  disks) and **OVA** (the same, as one
  archive file), offered by VirtualBox,
  VMware, and others under Export Appliance /
  Import Appliance. The export converts the
  disks into the package, the import converts
  them back into the new home's native
  format. It is the designed bridge — and
  like all designed bridges, it carries the
  standard load and creaks at the exotic
  ones: snapshots do not travel in an export,
  suspended states never do, and unusual
  devices (passed-through USB, exotic
  controllers) are left behind with a note.

## What doesn't survive the trip

- **The guest tools' other half.** The
  drivers installed in the guest (chapter 8)
  belong to the old hypervisor's pretend
  hardware. The moved machine usually boots
  anyway — remove the old tools and install
  the new home's, in that order's
  documentation, before judging performance.
- **Licences with opinions.** Some guest
  operating systems and applications tie
  activation to the machine's pretend
  hardware identity, which a move can change.
  This is their rule, not the hypervisor's
  malfunction; the fix is reactivation, and
  occasionally a phone call.
- **Suspended state and snapshots in place.**
  Chapter 3's third verb doesn't travel at
  all; snapshots travel only as history
  inside the same hypervisor's folder copy,
  not through an export.
- **Absolute paths.** Configurations that
  named the old host's directories — shared
  folders, attached ISOs — point nowhere on
  arrival until you re-point them.

## The moving checklist

1. Shut the guest down. (Suspended and
   running states do not move.)
2. Decide: same hypervisor → copy the folder;
   different → export to OVA, or convert the
   disks and rebuild the machine settings at
   the far end.
3. Convert with `qemu-img` if formats are the
   obstacle, and `qemu-img compare` or a boot
   to prove the bytes.
4. At the far end: fresh MAC address, check
   the guest's hostname and identity, replace
   the guest tools, re-point shared folders
   and ISO attachments.
5. Keep the original until the moved machine
   has done a day of real work. A move you
   can undo is a move you can be brave about —
   and that kept original, stored elsewhere,
   has quietly been a backup all along.
