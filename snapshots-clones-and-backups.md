# Snapshots, Clones, and Backups

Three words beginners use interchangeably and
computers do not. A **snapshot** rewinds one
machine. A **clone** duplicates it. A
**backup** survives it. This chapter proves
the first on the lab's specimen, explains the
second, and refuses to blur the third.

## Snapshots: the rewind button

A snapshot records the state of a VM's disk —
and, taken while the machine runs, its memory
too — at a moment, inside the VM's own files,
so you can return the machine to that moment
later. Before an experiment, before an update
you don't trust, before opening the suspicious
attachment: snapshot, proceed, and the cost of
being wrong is a rewind instead of a rebuild.

QEMU keeps a qcow2 image's snapshots inside
the image itself. The lab proved the whole
lifecycle at byte level (`t07`). The specimen
begins with the pattern from chapter 5:

```
$ qemu-io -c 'read -v 0 16' snaptest.qcow2
00000000:  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
```

Take the snapshot, then overwrite the same
bytes with a different pattern:

```
$ qemu-img snapshot -c before-changes snaptest.qcow2
$ qemu-io -c 'write -P 0x5a 0 1M' snaptest.qcow2
$ qemu-io -c 'read -v 0 16' snaptest.qcow2
00000000:  5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a 5a  ZZZZZZZZZZZZZZZZ
```

The snapshot is on the image's own list:

```
$ qemu-img snapshot -l snaptest.qcow2
Snapshot list:
ID        TAG               VM SIZE                DATE     VM CLOCK     ICOUNT
1         before-changes        0 B 2026-10-22 00:16:57 00:00:00.000          0
```

(`VM SIZE 0 B` because the machine was off
when the snapshot was taken — there is no
memory state to store. A snapshot of a
*running* VM carries its memory and costs
that much more room.)

Apply the snapshot, and the bytes say the
experiment never happened:

```
$ qemu-img snapshot -a before-changes snaptest.qcow2
$ qemu-io -c 'read -v 0 16' snaptest.qcow2
00000000:  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
```

Delete it (`-d`) and the list is empty again.
VirtualBox, VMware, and Hyper-V (whose word
is *checkpoint*) wrap the same mechanism in
menus and trees of named moments; the idea,
and the warning below, transfer unchanged.

Care with the rewind: it rewinds **everything**
— the guest's clock, its security updates,
and anything you did inside the guest that
you meant to keep. A snapshot tree you no
longer understand is a liability, not a
library; name snapshots like commits and
delete them like branches.

## Clones: the second machine

A clone is a copy that becomes its own
machine: its own disk file, its own settings,
free to diverge. Two kinds:

- A **full clone** copies the whole disk —
  independent, portable, and as large on the
  host as the original's used space.
- A **linked clone** gives the copy a thin
  child image on a backing file (chapter 5)
  and copies nothing: instant and tiny, and
  chained forever to the original file.

At the file level — which is where QEMU
lives — a full clone of the lab's specimen is
nothing grander than copying the image while
the machine is off, and chapter 10's
conversions are the same act with a format
change. The graphical hypervisors add the
important courtesy: a fresh machine identity
for the clone (new MAC address; and you
should give the guest OS inside a new
hostname and identity of its own, because two
machines with one name confuse networks and
licence servers alike).

## Backups: the copy that survives

Now the sentence this guide promised in
chapter 1 to keep repeating: **a snapshot is
not a backup.** The snapshot list above lives
*inside `snaptest.qcow2`*. Delete that file,
or lose the disk under it, and the machine
and every one of its snapshots go together —
the rewind button is stored in the same room
as the fire. The same is true, word for word,
of VirtualBox snapshots, VMware snapshots,
and Hyper-V checkpoints: all of them are
inside the VM's own folder.

A backup of a VM is unglamorous:

1. Shut the guest down (or use the
   hypervisor's backup mode/agent, which
   arranges a consistent moment — copying a
   running machine's disk file is copying a
   book while someone writes in it).
2. Copy the machine's whole folder — disk
   images *and* its configuration — to
   somewhere else: another disk, another
   machine, storage you rent.
3. Restore once, somewhere safe, to prove
   the copy is a machine and not a souvenir.

Export formats (chapter 10) make tidy
backup-shaped packages, and every hypervisor
vendor sells or documents something more
automatic than step 2. The principle doesn't
move: *elsewhere, complete, and tested*.

## Check

Pick any VM you care about and answer: if the
host's disk died this hour, what exactly
would you lose? Snapshots named in a list
inside the same folder are part of the loss.
The copy that isn't — that's the backup.
