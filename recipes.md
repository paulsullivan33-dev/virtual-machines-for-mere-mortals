# Recipes

Set pieces, start to finish. Each names its
mode of verification honestly: the lab ran the
disk work; the wizard-shaped recipes follow
the hypervisors' documented flows, as
`criteria.md` records.

## 1. The throwaway test machine

*The job: meet software you don't trust on a
machine you can delete.*

1. Create a VM (chapter 3): 2 processors,
   4 GiB memory, a 25 GiB thin disk, NAT
   networking — and no shared folders, no
   shared clipboard (chapter 8).
2. Install the guest from its official ISO;
   verify the ISO's checksum first (chapter
   3's habit).
3. Install the guest tools only if the test
   doesn't argue against them.
4. Snapshot it, named `clean` (chapter 6).
5. Test. Between tests, rewind to `clean`.
   When the machine is done being useful,
   delete its folder and be free of the whole
   affair.

## 2. A snapshot before the scary update

*The job: make an irreversible guest change
reversible.*

```
qemu-img snapshot -c before-update machine.qcow2
qemu-img snapshot -l machine.qcow2
```

Or the same two clicks in your hypervisor's
snapshot panel, named with the date and the
reason — `before-update` will not explain
itself in six months; `2026-10-before-driver-update`
will. Run the update. If the guest misbehaves,
apply the snapshot; if it behaves, keep the
snapshot a week, then delete it. A snapshot
kept forever is disk spent on a moment you
no longer need — and it is still not a
backup.

## 3. A second copy of a machine to experiment on

*The job: clone the working VM, break the
clone freely.*

Shut the guest down. Copy the machine's whole
folder (full clone, chapter 6) — or, in the
graphical tools, use Clone and choose *full*.
Start the clone and fix its identity before
it meets the network: accept the new MAC
address, change the guest's hostname, and
let it take a fresh DHCP lease. Two machines
with one identity on one network is chapter
14's riddle; the clone is where it is born.

## 4. Move a machine to a new computer

*The job: same VM, different desk.*

Chapter 10's checklist, compressed: shut
down; same hypervisor there → copy the
folder; different → export an OVA and import
it; verify by boot; refresh the guest tools;
keep the original until the copy has earned
trust. The disk-conversion bench, if formats
are the obstacle:

```
qemu-img convert -f qcow2 -O vmdk machine.qcow2 machine.vmdk
qemu-img compare -f qcow2 -F vmdk machine.qcow2 machine.vmdk
```

## 5. Free the host: retire a VM properly

*The job: a machine's work is done; reclaim
its resources without orphaning its data.*

1. Inside the guest, copy out anything that
   exists nowhere else. Then ask the chapter
   6 question: if this vanished in an hour,
   what would I lose? Act on the answer, not
   on the hope.
2. Export an OVA **as its backup** and store
   it somewhere that is not this host's disk,
   if there is any chance the machine is
   wanted again.
3. Delete the machine in the hypervisor (so
   its registrations and snapshot trees go
   too), then check the folder is really
   gone.
4. Verify the reclaim: the host's free disk
   space should rise by roughly the disk
   size `qemu-img info` reported — the real
   number, not the virtual one (chapter 5).

## 6. The legacy application machine

*The job: one old program that needs one old
operating system.*

Give the VM exactly what the old system
wants — often less than the defaults: 1–2
processors, memory at the old OS's comfort,
a modest disk — and then **subtract**, by
judgement: host-only networking (chapter 7)
if the program needs no internet, which old
unpatched systems emphatically should not
have; no shared folders beyond the one
folder the program's files live in; a
snapshot named `working` taken the day it
first runs properly, because recreating that
day is the whole value of the machine. If
the program checks for a dongle or a serial
device, chapter 9 is the map.
