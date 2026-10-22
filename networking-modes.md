# Networking Modes

A VM's network card is pretend, but the mode
you choose for it is a real decision about
who can reach the guest and whom the guest
can reach. Every desktop hypervisor offers
the same three shapes under near-identical
names. Mistake 2 from chapter 1 lives here:
the default, NAT, is a one-way door, and
beginners lose an afternoon to it weekly —
the web server that works beautifully from
inside the guest and is invisible to the
phone on the same desk.

## The three modes

| Mode | Guest reaches the internet | Host reaches guest | Other LAN devices reach guest | Guest sees the LAN |
|---|---|---|---|---|
| **NAT** | Yes | Via port forward only | No | No — a private mini-network |
| **Bridged** | Yes | Yes, at its own LAN address | Yes | Yes — it *is* a LAN device |
| **Host-only / internal** | No | Yes (host-only) | No | No |

**NAT** (VirtualBox "NAT", VMware "NAT",
Hyper-V's default switch, QEMU's `user`
backend — the mode the lab's boot used) puts
the guest behind a tiny private router the
hypervisor runs for it. Outbound works like
a home network does: the guest can browse,
update, and download. Inbound stops at the
door unless you forward a port. The guest's
address is a private one the outside world
never sees — in QEMU user networking's
documented defaults, the guest is
10.0.2.15, its gateway 10.0.2.2, its DNS
10.0.2.3 — and the lab's evidence for the
mode is its boot log (`t09`): the pretend
Intel card reporting its link up and a guest
that came fully up behind that door. (The
guest-side lease conversation itself happens
on the guest's console, which the lab read
only as a log; the addresses here are QEMU's
documented defaults, quoted as such.)

**Bridged** networking hands the guest its
own identity on the real network: it asks
the real router for an address by DHCP, like
a second computer plugged into the same
switch, because as far as the network knows
it is one. Choose it when the guest *is* a
server for the household — the thing other
devices should find by address. Accept its
terms: the guest is now a full citizen of
your LAN, with a citizen's exposure, and on
some networks (hotel and campus Wi-Fi,
802.1X-authenticated Ethernet) a second
identity is not permitted to exist.

**Host-only** gives the guest a private wire
to the host and other guests, and nothing
else — no internet, no LAN. (VirtualBox's
"internal network" goes one step further and
excludes even the host.) It is the mode for
labs that must not leak: malware study,
simulated office networks, anything whose
guest you would not introduce to your router.

## Port forwarding: the deliberate exception

NAT's door opens individual ports, on
purpose, one rule at a time: "connections to
the host's port 2222 go to the guest's port
22." In QEMU the rule rides on the network
option itself:

```
-netdev user,id=n0,hostfwd=tcp::2222-:22
```

The graphical hypervisors keep the same rules
in a Port Forwarding table in the network
settings. Two honest notes: a forwarded port
is a *published* port — whatever listens in
the guest is now reachable by anything that
can reach the host — and forwarding is the
fix for "my guest's server is invisible,"
not switching to bridged and hoping, which
publishes *every* port the guest opens.

## Multiple cards, mixed modes

A VM can have several pretend network cards,
each in its own mode — NAT for updates,
host-only for talking to its lab partner,
bridged for the service it exists to provide.
Real lab setups are usually this shape. The
modes are per-card answers to one question:
*who should be able to start a conversation
with this machine?* Ask it per card.

## Check

For your VM, complete the table's row from
memory: which mode, what address the guest
holds, and who — host, phone on the Wi-Fi,
the internet — can open a connection to it.
If the middle answer is "some 10.x address,"
you are behind NAT, and chapter 14's most
common networking symptom is already
explained.
