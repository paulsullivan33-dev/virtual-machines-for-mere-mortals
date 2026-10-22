# Sizing Honestly

Every VM begins as three numbers: processors,
memory, disk. The defaults a wizard offers
are sane, and this chapter is about the
arithmetic behind them — because the failure
mode of sizing is not picking a slightly
wrong number, it is believing the numbers are
free. Mistake 3 from chapter 1 runs this
chapter: **resources given to a VM are taken
from the host.** A guest's memory is carved
out of the host's while it runs. Its disk —
chapter 5 — is a file growing in the host's
filesystem. Its processors are time on the
host's.

## Processors: the forgiving number

A pretend processor is mostly a schedule, not
a possession: when the guest is idle, its
virtual CPUs cost the host almost nothing, so
giving a guest 2 or 4 cores on an 8-core host
is reasonable, and giving it all 8 is merely
greedy, not broken. The guidance that
survives contact:

- Start with **2** for a desktop guest, **1**
  for a small server guest. Add when the
  guest itself shows you it is CPU-starved,
  not before.
- Never give one guest more cores than the
  host has threads; the hypervisor will warn
  or quietly cap you, and either way the
  guest's own expectations break.
- **Overcommit** — the sum of all guests'
  cores exceeding the host's — is normal and
  usually fine for CPU, because guests are
  rarely all busy at once. It is a bet, and
  the bet's terms should be known, not
  discovered.

## Memory: the unforgiving number

Memory is not scheduled the same way. A guest
running with 8 GiB is, to a first
approximation, 8 GiB the host no longer has —
and when host memory runs out, something
swaps to disk or dies, and it is as likely to
be the host's own work as the guest. (Some
hypervisors can reclaim gently — balloon
drivers, dynamic memory — which softens the
rule without repealing it; the guest tools of
chapter 8 carry the balloon.)

The arithmetic that keeps a desk healthy:

1. Know the host's total. (The lab's build
   machine had 7.7 GiB — and was busy with
   other work, which is why the lab guest got
   256 MiB and the guest was chosen to be
   happy there.)
2. Keep the host's own share untouchable:
   the host OS and your open work need theirs
   *first*.
3. Give the guest what its OS and job need —
   the guest vendor's stated minimum is a
   floor for installing, not a size for
   working. A modern desktop Linux is content
   at 4 GiB for light work; a Windows desktop
   wants more; the honest source is the guest
   documentation, and the honest test is
   watching the guest's own memory pressure
   after a day of real use.
4. The sum of *running* guests' memory must
   fit in what step 2 left. Memory overcommit
   is the risky kind of overcommit, and the
   risk lands on the host.

## Disk: the patient number

Disk lies by politeness: a thin disk
(chapter 5) lets you *promise* the guest
100 GiB on a host that has 40 GiB free, and
everything works — until the day the guest
has actually written 40 GiB and the host's
filesystem is full, at which point the guest
fails mid-write in ways operating systems
find undignified. Size the pretend disk for
the job (an OS plus its room to grow: 25–40
GiB is the common desktop range), and watch
the *file*, not the promise: the host-side
size in chapter 5's `qemu-img info` is the
one that fills your real disk.

## The fourth resource nobody sizes: the host's disk speed

A guest's disk is a file, and it inherits the
real disk underneath it. Four VMs on one
spinning drive are four computers sharing one
very patient disk. Nothing in the wizards
says this; `criteria.md` repeats it because
the lab — one guest, 2 MiB of writes — has
nothing to measure it with.

## Check

Before starting a new VM, say out loud: host
total memory, host's own share, this guest's
share, running total. If any of those is "I
don't know," that is the finding — sizes
chosen without them are wishes, and chapter
14 is where wishes come back as symptoms ("the
host froze when the VM was running" is this
chapter, arriving late).
