# The node command prompt

When you connect to an XRouter node — over the air, or in over the internet, or
at the node's own console — you arrive at a **command prompt**. From there you
drive the node: list what it can reach, connect onward, check who is around, and
reach the mailbox and chat server. This page is for anyone using a node as an
ordinary user; the [sysop commands](sysop.md) build on the same prompt with
extra privileges.

## Getting to the prompt

You can reach the node prompt several ways:

- **Over RF (AX.25 / NET/ROM)** — connect to the node's callsign or alias from
  your TNC or terminal, exactly as you would connect to any packet station. If
  the node has a [connect text](../configuration/global-settings.md) it greets
  you, then drops you at the prompt.
- **By Telnet** — XRouter runs a built-in Telnet server (port 23 by default), so
  you can reach the prompt from any Telnet client on the same network or, where
  the sysop allows it, across the internet. This is the easiest way to try a node
  you are running yourself.
- **At the console** — on the machine running XRouter, the text-mode console
  windows include a command line that talks to the same node core.

However you arrive, the prompt behaves the same way: type a command, press Enter,
read the reply.

## The abbreviation convention

The in-program manual writes every command with its shortest acceptable form in
square brackets. So `C[onnect]` means **you can type `C`, or `CONNECT`, or
anything in between** (`CO`, `CON`, …) — the part outside the brackets is the
minimum, and the bracketed tail is optional. A few more examples you will meet
below:

| Written as | Shortest form | Also accepted |
| --- | --- | --- |
| `C[onnect]` | `C` | `CO`, `CON`, `CONNECT` |
| `N[odes]` | `N` | `NO`, `NODES` |
| `I[nfo]` | `I` | `INFO` |
| `B[ye]` | `B` | `BYE` |
| `MH[eard]` | `MH` | `MHEARD` |

Commands are **not case-sensitive** — `connect`, `Connect` and `CONNECT` are the
same. (Anything that names a *file*, on the other hand, is case-sensitive on
Linux; that only matters for sysop file commands.) Throughout this site we print
commands in capitals for clarity, but you never have to.

## Getting help

Three commands open the node's own documentation, which is always the final word:

- **`HELP`** (or **`?`**) — `?` on its own lists the available commands; `HELP`
  gives help, and `HELP <command>` explains one command and its syntax. Because
  this help is generated from the same manual the node was built from, it
  reflects exactly what *your* build supports.
- **`MAN`** — displays the sysop manual pages (the man-style reference sections)
  held on the node.
- **`INFO`** or **`I`** — shows the node's own information text and can look up a
  topic or another node.

!!! tip "Trust the node over this site"
    If `HELP` on your node and a table on this site ever disagree, the node is
    right — it knows its own version. Use `?` to see the exact command set your
    build offers.

## The everyday commands

What follows is a tour of the commands an ordinary user reaches for. Every one of
them is listed in the [command reference](command-reference.md); here we show how
they fit together.

### Finding your way around

**`NODES`** displays the node's NET/ROM nodes table — every distant node it knows
how to reach, by alias and callsign. With no argument you get the whole list;
name a node to see the routes to it:

```text
NODES            list every known node
NODES GB7XXX     show the route(s) to one node
```

**`ROUTES`** lists the node's immediate **neighbours** — the directly reachable
links, with their quality — so you can see what this node is actually wired to,
as opposed to everything it has *heard about* (which is `NODES`).

**`PORTS`** lists the node's ports (its radio channels and links) with the
identifying text the sysop gave each one. Knowing the port numbers matters when
you want to connect out on a specific port.

### Connecting onward

**`CONNECT`** (`C`) is the workhorse: it makes an outgoing connection from this
node to another station, on RF or onward through the network.

```text
C GB7XXX             connect to a node or station by call/alias
C 2 GB7XXX           connect out on port 2 specifically
C 2 GB7XXX V GB7DIG  connect via a digipeater
```

XRouter also uses CONNECT to reach **numbered services** on a node (its
NetRom-X service numbers — see
[NetRom-X service numbers](../networking/netromx-services.md)). The same idea
underpins the two subsystem gateways below.

### Seeing who and what is about

**`USERS`** shows who is currently using the node and what they are doing.

**`MHEARD`** (`MH`) lists the callsigns the node has recently heard on the air,
newest first — a quick way to see who is active on a frequency and whether your
own signal is getting in.

### Testing reachability

**`PING`** sends an ICMP echo to a TCP/IP host and reports whether it answered —
the familiar reachability test, for the node's IP side. (For NET/ROM links there
are sibling commands such as `NPING`; see the
[command reference](command-reference.md).)

### Leaving

**`BYE`** (`B`) — or its synonym **`QUIT`** — disconnects you cleanly from the
node. On an RF or Telnet session this drops the link and returns you to wherever
you came from.

## The two subsystem gateways

Two subsystems have their own command worlds, reached from the node prompt:

- **`CHAT`** (`CH`) connects you to the node's **chat server** — a multi-channel
  conversation system that can link to peer chat servers. Once inside, chat has
  its own set of `/`-prefixed commands. See [Chat server](../subsystems/chat.md)
  for the full walkthrough.
- **`PMS`** connects you to the node's **personal mailbox** (the Personal Message
  System), where you read and send mail. The mailbox has its own command set in
  turn. See [PMS mailbox](../subsystems/pms-mailbox.md).

Both behave like connecting to a service: you enter the subsystem, work there,
and leave it to return to the node prompt — or `BYE` to leave the node entirely.

## Where to go next

- The full list, including sysop-only commands:
  [Command reference](command-reference.md).
- Gaining sysop rights and the administrative commands:
  [Sysop access and commands](sysop.md).
- The subsystems reached from the prompt:
  [PMS mailbox](../subsystems/pms-mailbox.md) ·
  [Chat server](../subsystems/chat.md).

---

**Sources:** [In-program manual, Section 1 — General Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man1)
· [General Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:generalhelp)
· [XRouter support group (groups.io)](https://groups.io/g/xrouter)
