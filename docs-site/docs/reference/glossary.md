# Glossary

Short definitions of the terms you meet across this site and in XRouter's own manual.
Where a concept has a fuller page, the term links to it. This is a quick reference, not
a tutorial, start with [Packet radio basics](../basics/what-is-packet-radio.md) if a
whole area is new to you.

### AGWPE
The **AGW Packet Engine** (SV2AGW), a Windows program that presents radios/TNCs to
applications over a TCP network interface. XRouter can act as a *client* of AGWPE (or
Direwolf's AGW interface) and can also *emulate* AGWPE so other apps use XRouter as
their packet engine. See [AGWPE](../interfaces/agwpe.md).

### Alias
A short, human-friendly name for a [node](#node) (for example `KIDDER`) carried alongside
its [callsign](#ssid). Users can connect or route by alias instead of memorising a callsign.

### AMPRNet / 44Net
The amateur TCP/IP internetwork built on the `44.0.0.0/8` address block obtained for
amateur use in 1981. IP runs *on top of* AX.25 (or over internet tunnels). XRouter has a
full IP stack for it. See [IP over AX.25 and AMPRNet](../basics/ip-over-ax25.md).

### APRS
The **Automatic Packet Reporting System** (WB4APR): a connectionless, broadcast use of
AX.25 [UI frames](#ui-frame) for positions, weather, objects and short messages, usually
plotted on a live map. XRouter can digipeat APRS and act as an [IGate](#igate). See
[APRS in brief](../basics/aprs.md).

### AX.25
**Amateur X.25**, the data-link (layer-2) protocol underneath almost all packet radio.
It carries [callsigns](#ssid) in every frame, supports both connected and connectionless
operation, and defines [digipeating](#digipeater). See [AX.25: the data link](../basics/ax25.md).

### AXIP / AXUDP / AXTCP
Three ways to carry AX.25 frames over the internet to link nodes: **AXIP** wraps them in
raw IP (protocol 93), **AXUDP** in UDP datagrams, **AXTCP** in a TCP stream (which traverses
NAT and firewalls well). See [AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md).

### BBS
A **Bulletin Board System**, full store-and-forward mail and message handling. XRouter
provides a smaller [PMS](#pms) rather than a full BBS; for a full BBS the community usually
points to BPQ.

### DEDHOST / WA8DED host mode
A TNC "host mode" (originated by WA8DED, popularised by the TF/TheFirmware family) that lets
a host program drive several logical channels over one serial link. XRouter can *emulate* a
WA8DED host-mode TNC so applications such as FBB can use it as their packet engine.

### Digipeater
A single-frequency, store-and-forward repeater at layer 2: if its callsign appears next in a
frame's repeater list, it retransmits the frame. It is simple source routing, the sender
lists the path, and is the building block beneath APRS paths. See [AX.25](../basics/ax25.md).

### FRACK
**Frame ACK** time: the AX.25 T1 timer, the time a station waits for an acknowledgement before
resending a frame. Set per port (`FRACK`, milliseconds); higher on slow RF links, lower on fast
or wired links.

### IGate
An **Internet Gateway** that bridges on-air [APRS](#aprs) to the worldwide APRS-IS backbone,
making local traffic visible on maps such as aprs.fi. XRouter includes one. See
[APRS digipeater and IGate](../subsystems/aprs-igate.md).

### INP3
The **Inter-Node Protocol 3**, a link-state routing extension to [NET/ROM](#netrom). Instead of
broadcasting [quality](#quality), it unicasts routing updates to neighbours and uses measured
**trip time** (plus hop count) as the metric, so the network can prefer genuinely faster paths.
See [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).

### KISS
"**Keep It Simple, Stupid**", a minimal framing protocol on the link between a computer and a
[TNC](#tnc). It is not part of AX.25 and never goes on the air; it just delimits frames over the
wire. Runs over serial or over TCP. See [KISS, TNCs and soundcard modems](../basics/kiss-tncs-and-modems.md).

### KISS-over-TCP
The same KISS byte stream carried over a TCP socket instead of a serial port, so a node program
can reach a software TNC (such as Direwolf) or a TNC on another machine. The usual way XRouter
talks to a [soundcard modem](#soundcard-modem).

### L3RTT
XRouter's **Layer-3 Round-Trip-Time** measurement: the node times traffic to each neighbour to
keep a running average, feeding the time-based routing metrics ([INP3](#inp3)) and keeping
inter-node links alive.

### MAXFRAME
The maximum number of un-acknowledged AX.25 information frames a station may have outstanding
(the window). Set per port; values of 8 or more switch the link to extended (modulo-128) operation.

### MTU
**Maximum Transmission Unit**, the largest frame an [interface](../configuration/interfaces.md)
will pass. Mandatory in every interface block; the manual advises keeping it to 256 (or less) on
AX.25 ports because of TNC buffer limits.

### Neighbour
A [node](#node) reachable directly, in one AX.25 hop, over a radio port or link. NET/ROM routing
is built up from what each node learns about its neighbours.

### NET/ROM
A layer-3/4 networking scheme over connected-mode AX.25 that lets traffic be routed automatically
across many nodes (with friendly [aliases](#alias) and per-hop acknowledgement) instead of relying
on manual [digipeater](#digipeater) paths. XRouter implements it and extends it. See
[NET/ROM: networking and routing](../basics/netrom.md).

### NetRom-X
XRouter's **service-numbering scheme**: one node callsign exposing many numbered services
(mailbox, chat, info and so on), like well-known TCP ports, avoiding a separate [SSID](#ssid)
per service. See [NetRom-X service numbers](../networking/netromx-services.md) and the
[service-number table](ports-and-services.md#netrom-x-standard-service-numbers).

### Node
A station, usually unattended, at a good RF site, that provides onward connectivity between
users and other nodes, identified by a [callsign](#ssid) and an [alias](#alias). XRouter turns a
computer plus radios/links into such a node. See [Nodes, switches and the sysop role](../basics/nodes-and-sysops.md).

### PACLEN
**Packet length**: the maximum payload XRouter puts in a packet (`PACLEN`). Set globally and
per port; the global value also sets the NET/ROM layer-3 default.

### PMS
The **Personal Message Server**, XRouter's built-in mailbox. It does FBB-style forwarding but is
deliberately smaller than a full [BBS](#bbs). See [PMS mailbox](../subsystems/pms-mailbox.md).

### PZTDOS
A small DOS-style file-maintenance shell built into XRouter (with a line editor) for managing
files remotely over slow packet links. Entered with the `DOS` command.

### Quality
NET/ROM's routing metric: a configurable 0–255 figure for how good a route to a node is. It is
de-rated as it propagates, so each node can pick the best next hop. Sensible quality settings keep
node tables sane; everyone using 255 causes route "hijacking" and bloat.

### Soundcard modem
Software that turns a computer sound card (or USB audio interface) into an AX.25 modem, replacing
a hardware TNC, for example **Direwolf**, **UZ7HO SoundModem** or **QtSoundModem**. XRouter
connects to these over [KISS](#kiss) (often [KISS-over-TCP](#kiss-over-tcp)) or [AGWPE](#agwpe).
See [Soundcard modems](../interfaces/soundcard-modems.md).

### SSID
**Secondary Station Identifier**, the 0–15 suffix on a callsign (for example `CALL-7`) that lets
one operator run several distinct stations or services. AX.25 callsigns are limited to six
characters plus the SSID.

### Sysop
The **system operator** who owns and runs a [node](#node): licensing/authorisation, siting,
configuring routing and services, and keeping the node well-behaved on shared channels. See
[Nodes, switches and the sysop role](../basics/nodes-and-sysops.md) and
[Sysop access and commands](../commands/sysop.md).

### TNC
A **Terminal Node Controller**, the device between radio and computer. Traditionally it ran an
AX.25 implementation in firmware; in [KISS](#kiss) mode it becomes a "dumb" modem driven by host
software like XRouter. See [KISS TNCs](../interfaces/kiss-tncs.md).

### UI frame
An **Unnumbered Information** frame: a connectionless AX.25 frame broadcast with no connection,
acknowledgement or retransmission. NET/ROM [NODES broadcasts](#netrom) and [APRS](#aprs) both use
UI frames.
