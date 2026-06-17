# Chat server

XRouter includes a built-in multi-channel chat server, **XRChat** — a keyboard-to-keyboard
conferencing system that lets users on your node, and on linked nodes, talk in shared
rooms. This page covers what it is, how to switch it on, how its rooms and links work, and
how callers reach it.

## Heritage

XRChat is not new. The chat subsystem has been in use **for roughly a quarter of a
century**, evolving alongside the rest of XRouter since the late 1990s. That longevity
matters in two ways: it is well proven in the field, and it speaks several long-established
chat dialects, so it can join conversations hosted by other software rather than walling
your users off in their own system.

## Enabling it

The chat server is configured with a small set of global directives in `XROUTER.CFG`.
As with the mailbox, giving it a callsign is what makes it visible on the network.

| Directive | Purpose |
| --- | --- |
| `CHATCALL` | The chat server's own AX.25 / NET-ROM callsign. An SSID of `-8` is the conventional choice. |
| `CHATALIAS` | The chat server alias; the convention is to end it in `CHT`. |
| `CHATQUAL` | The NET-ROM quality at which the chat server is advertised. `0` makes it invisible in the nodes table (callers can still reach it via the service number). |
| `CHATPORT` | The TCP port the chat server listens on for Telnet/TCP callers (default **3600**). |
| `CHATLINKS` | The callsigns of other chat servers this one links to (see below). |

A typical minimal configuration:

```ini
CHATCALL=M0XXX-8
CHATALIAS=XXXCHT
CHATQUAL=0          ; reachable, but not broadcast as its own node
```

!!! note "The chat server is effectively always on"
    Unlike the mailbox, the chat server is part of XRouter's core and cannot be fully
    switched off — at most it can be hidden from NET-ROM by leaving `CHATQUAL` at `0`.
    There is no harm in this; an unused chat server simply sits idle.

## Rooms and channels

XRChat supports a very large number of channels — far more than any node will use in
practice — so you never run short of rooms. Users join, leave and create rooms with the
`/`-prefixed chat commands once they are connected. A few rooms have special significance:

- There is a **default room** that users land in, so a caller who just connects has
  somewhere to be without issuing any commands.
- One room is set up to **bridge the BPQ "RoundTable"** chat network, so XRChat users and
  BPQ chat users share a conversation rather than running in parallel.
- XRChat also **interworks with the JNOS "Tampa Ping-Pong"** Converse network, the
  long-running cross-system chat that JNOS nodes participate in.

This cross-system reach is one of XRChat's defining traits: the manual describes it as
compatible with **XRChat, Ping-Pong and RoundTable** systems at once, so a message typed
on your node can be seen by users on quite different software elsewhere.

!!! tip "The sysop instant-message channel"
    XRouter's own console carries a sysop chat window. Community sources place the sysop
    instant-messaging channel at **channel 1234** — handy for sysop-to-sysop chat across
    linked nodes. Treat the exact number as community knowledge; confirm it against your
    own build's chat help if you intend to rely on it.

## Linking chat servers between nodes

Two XRouter chat servers can be **linked** so that their rooms are shared, building a
wider conferencing mesh out of individual nodes. Each side lists the other's `CHATCALL`
in its `CHATLINKS` directive. The link can run over NET-ROM or TCP.

A few practical rules the author stresses:

- **Links must be bilateral.** Each partner must list the other; a one-sided `CHATLINKS`
  entry will not form a working link.
- **Only link to nodes already in your nodes table**, and prefer nearby, well-connected
  servers. Linking distant or slow servers loads the network and degrades the experience
  for everyone in the room.

```ini
CHATCALL=M0XXX-8
CHATALIAS=XXXCHT
CHATLINKS=M0YYY-8 M0ZZZ-8   ; each of these must list M0XXX-8 in return
```

## Reaching the chat server

As with the mailbox, the chat server has its own callsign (`CONNECT M0XXX-8`) and a TCP
port (`CHATPORT`, default 3600). The standard route, though, is **NetRom-X service 8**:
on any XRouter node, connecting to service 8 reaches the chat server.

```text
CONNECT M0XXX 8
```

See [NetRom-X service numbers](../networking/netromx-services.md) for the full scheme.
The user-facing chat commands — joining rooms, listing users, sending and leaving — are
documented with the other node commands in the
[Command reference](../commands/command-reference.md).

---

**Sources:** [In-program manual, OARC wiki — Chat server](https://wiki.oarc.uk/packet:xrouter:chatserver) ·
[Chat server help](https://wiki.oarc.uk/packet:xrouter:docs:chatserverhelp) ·
[In-program manual — Section 7 (directives)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
