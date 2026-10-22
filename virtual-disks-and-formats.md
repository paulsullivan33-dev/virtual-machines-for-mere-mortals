# Virtual Disks and Formats

Inside the guest, the disk is a disk: a
capacity, partitions, a filesystem, files. On
the host, the whole thing is **one file** —
the disk image — and everything interesting
about virtual disks is the relationship
between those two views. This chapter lives
in that relationship. All of it was run, on
the specimen made in chapter 3; the
transcripts are `t03`–`t06`.

## Two sizes, both real

Create a disk and ask about it:

```
$ qemu-img create -f qcow2 specimen.qcow2 8G
Formatting 'specimen.qcow2', fmt=qcow2 cluster_size=65536 extended_l2=off compression_type=zlib size=8589934592 lazy_refcounts=off refcount_bits=16
$ qemu-img info specimen.qcow2
image: specimen.qcow2
file format: qcow2
virtual size: 8 GiB (8589934592 bytes)
disk size: 196 KiB
...
    file length: 192 KiB (197120 bytes)
```

- **Virtual size** — 8 GiB — is what the
  guest sees: the capacity of the pretend
  disk.
- **Disk size** — 196 KiB — is what the file
  actually occupies on the host.

That gap is **thin provisioning** (also
called dynamic allocation or sparse storage):
the image stores only what the guest has
written, plus its own bookkeeping, and grows
as the guest writes more. The opposite,
**thick provisioning**, takes the whole
capacity on the host at creation. QEMU's
`preallocation=full` option does it; the
desktop hypervisors expose it as a checkbox
("allocate all disk space now" in VMware's
spelling, the inverse of VirtualBox's
"dynamically allocated").

Watch thin provisioning work. The lab wrote
two 1 MiB patterns into the image with
`qemu-io` — a tool that pokes an image the
way a guest would, without booting anything:

```
$ qemu-io -c 'write -P 0x41 0 1M' specimen.qcow2
wrote 1048576/1048576 bytes at offset 0
$ qemu-io -c 'write -P 0x42 512M 1M' specimen.qcow2
wrote 1048576/1048576 bytes at offset 536870912
$ qemu-img info specimen.qcow2
virtual size: 8 GiB (8589934592 bytes)
disk size: 2.32 MiB
```

2 MiB written, 2.32 MiB occupied — the extra
is qcow2's own structure. And the image stays
healthy under its own checker:

```
$ qemu-img check specimen.qcow2
No errors were found on the image.
32/131072 = 0.02% allocated, 0.00% fragmented, 0.00% compressed clusters
```

Raw images behave differently. Same 8 GiB
request, other format:

```
$ qemu-img create -f raw specimen.raw 8G
Formatting 'specimen.raw', fmt=raw size=8589934592
$ qemu-img info specimen.raw
file format: raw
virtual size: 8 GiB (8589934592 bytes)
disk size: 4 KiB
    file length: 8 GiB (8589934592 bytes)
```

A raw image is nothing but the disk's bytes,
in order — no header, no bookkeeping, no
features (no snapshots of its own, chapter
6). Its *length* is the full 8 GiB from
birth; its *disk size* is tiny only because
the host filesystem is keeping it sparse. `ls`
will tell you the length and frighten you;
`du` tells the truth about occupancy. Two
sizes, both real, on every disk you own —
VMs just make you learn it.

## The formats, placed

| Format | Home | Character |
|---|---|---|
| **qcow2** | QEMU/KVM | Thin by nature, internal snapshots (chapter 6), optional compression and encryption, backing files |
| **raw** | everywhere | The bytes and nothing else; every tool can read it; no features; the interchange floor |
| **VMDK** | VMware | Workstation/ESXi's native; also readable by QEMU and VirtualBox |
| **VDI** | VirtualBox | VirtualBox's native; converts cleanly to and from the others |
| **VHDX** | Hyper-V | Windows' native; large-disk support, its own resilience features |

No format is a trap: all of them convert
(chapter 10 does it with this very specimen),
and `qemu-img` reads and writes the whole
table. Choose your hypervisor's native format
for machines that stay home, and convert
deliberately when one travels.

## Growing a disk — and the step after the step

Making the pretend disk bigger is one
command, run on a disposable copy in the lab
(`t06`):

```
$ qemu-img resize resize-me.qcow2 +2G
$ qemu-img info resize-me.qcow2
virtual size: 10 GiB (10737418240 bytes)
```

Then the part the command doesn't do: **the
guest still sees its old partitions.** A
bigger pretend disk is a bigger empty room
around the guest's existing furniture —
extending the partition and filesystem is a
separate job *inside* the guest (its own
disk tools, or an installer/rescue boot),
with the guest shut down or the filesystem
unmounted as its documentation instructs.
Shrinking is the dangerous mirror: `qemu-img
resize` refuses to truncate an image smaller
than its virtual size without `--shrink` and
a clear warning, because bytes beyond the
cut are simply gone. Shrink the guest's
filesystem first, the image second, and a
backup before either (chapter 6 — a real one,
somewhere else).

## Backing files, in one paragraph

A qcow2 image can be created *on top of*
another image: the child stores only its own
changes and reads everything else from its
**backing file**. That is how linked clones
(chapter 6) and instant template machines
work — and the rule that keeps them alive is
absolute: never modify a backing file while
children exist. It is named here because the
neighbours' template chapters build on it;
the lab did not need one.

## Check

For each VM disk you own, you can answer:
which format, what virtual size, what the
file actually occupies on the host, and how
close the host's filesystem is to full. The
last answer is the one that decides whether
the first three stay true.
