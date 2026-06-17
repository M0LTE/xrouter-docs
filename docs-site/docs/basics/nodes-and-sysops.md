# Nodes, switches and the sysop role

A packet network is built from **nodes**, and nodes are run by **sysops**. This primer
explains what a node (or switch) is and does, why someone runs one, and what responsibilities
the sysop takes on, which sets up the idea that XRouter is exactly such a node.

## What a node (or switch) is

In a packet network a **node**, also loosely called a **switch**, is a station, usually
unattended and at a good radio site, that provides **onward connectivity** between users and
between other nodes. It typically runs [NET/ROM](netrom.md) routing (and may add INP3, IP and
other layer-3 schemes). A node has a **callsign-SSID and an alias**, it sends and receives
NODES broadcasts, and it lets a user connect in and then be switched onward towards a distant
station or service.

A node may also host services, a chat server, a mailbox or bulletin board, a DX cluster, or
it may do nothing but forward traffic. UK callsign conventions hint at the distinction:
`GB7xxx` traditionally denotes a node *with* a mailbox, and `MB7Nxx` a node *without* one.
XRouter is software that turns a computer plus radios and links into precisely this kind of
node, handling AX.25, NET/ROM and TCP/IP in one program.

## Why someone runs one

People run nodes to provide a useful local service and to strengthen the wider network:
giving local users access, relaying traffic between areas, bridging RF and internet-tunnel
links, and experimenting. A node in isolation works but quickly loses its point, the value
comes from **links to neighbours**, by RF where possible and internet tunnels otherwise. For
that reason, network guidance encourages neighbouring nodes to coordinate and even share a
frequency, which is the opposite of the "everyone on their own channel" hotspot habit.

## The sysop's responsibilities

The **sysop** (system operator) owns and runs the node. The role covers, broadly:

- **Authorisation and siting**, meeting any local licensing requirement for an unattended
  station (region-dependent), and choosing a sensible location and antenna.
- **Bands and frequencies**, picking and coordinating channels with nearby sysops.
- **Routing configuration**, setting NET/ROM qualities sensibly, tuning INP3 limits, and
  arranging links to neighbours.
- **A credible node table**, keeping the node's view of the network *connectable* rather
  than bloated with stale or unreachable entries (see [NET/ROM](netrom.md) on quality).
- **Good channel citizenship**, keeping the node well-behaved on shared frequencies, and
  cooperating with other sysops.

Much of a packet network's health comes down to sysops configuring things thoughtfully.
Regulatory specifics, licensing, permitted operation of unattended stations, vary by
country and change over time, so always check the current rules where you are.

## Getting started

If you would like to run such a node with XRouter, the practical path begins at
[Getting started](../getting-started/index.md).

---

**Sources:**
[UK Packet Radio Network](https://ukpacketradio.network/) ·
[NET/ROM qualities (N1URO)](https://packet-radio.net/netrom-qualities/) ·
[XRouter/XRPi INP3 documentation](https://ohiopacket.org/xrpi/docs/inp3.htm) ·
[OARC packet history (G4KLX)](https://wiki.oarc.uk/packet:history)
