# Sysop access and commands

Most of the node prompt is open to any user, but the commands that *change* the
node — editing routes, monitoring traffic, restarting daemons, managing the IP
stack and security — are reserved for the **sysop**. This page explains how a
session becomes a sysop session, then surveys the categories of sysop power and
the representative commands in each. The commands themselves are catalogued in
the [command reference](command-reference.md).

## Becoming sysop

There are two routes to sysop status, one safe everywhere and one safe only on
trusted links.

### Authenticate with the sysop password

The normal, link-agnostic way is to **prove who you are** against the node's
sysop password file. XRouter keeps sysop passwords in `PASSWORD.SYS` — a list of
`callsign password` pairs (no SSID, no spaces, not case-sensitive). When a
recognised sysop connects, the node can issue a **password challenge**; you
answer it, and the session is promoted to sysop for its duration. This is what
guards remote AX.25 (`@`), RLOGIN and FTP sysop access. With no `PASSWORD.SYS`
present, that access simply does not exist — which is itself a safe default.

!!! note "Password file is sysop-only and case-matters on disk"
    `PASSWORD.SYS` holds the keys to the node; it must be readable only by the
    node's own user. Remember that on Linux the filename is **upper case** and
    case-sensitive — see [System and data files](../configuration/system-files.md).

### The `SYSOP=1`-on-a-port shortcut

A port can instead be declared *inherently* trusted, so that **everyone** who
connects on it is a sysop without any challenge. This is the `SYSOP` port
directive:

```ini
PORT=1
        ID=Console / LAN link
        INTERFACENUM=1
        SYSOP=1        ; every caller on this port is sysop — secure links ONLY
ENDPORT
```

!!! danger "`SYSOP=1` gives every caller full control — secure links only"
    The manual is explicit: *"If you set `SYSOP=1`, all users who connect on this
    port get full sysop status without needing to answer a password challenge.
    This is intended ONLY FOR USE ON SECURE LINKS, such as RS232 or Ethernet, and
    the default is zero."* Never set `SYSOP=1` on a port that is reachable over
    the air or from the wider internet — doing so hands node administration to
    anyone who can connect. The default is `0`, and on any RF port it must stay
    that way.

## The categories of sysop power

Once your session is sysop, a large additional command set unlocks. The
[command reference](command-reference.md) marks each command's scope; the groups
below show the shape of what is available, with representative commands. (Exact
syntax for a given command is on the node itself via `HELP <command>`.)

### Node and route management

XRouter learns its NET/ROM nodes table from broadcasts, which can take hours to
rebuild — so the sysop can save and restore it, and lock in routes by hand.

- **`SAVENODES`** writes the current nodes table out to the `XRNODES` recovery
  file (it is also saved periodically and at shutdown).
- **`LOADNODES`** reads that file back, repopulating the table — the mechanism
  that lets the node restart without losing everything it knew.
- `ROUTES` (add / drop / list) manages the neighbour routes directly, and
  `NODES` can modify the nodes table; broadcasts can be triggered or requested
  with `BCAST` and `BCPOLL`.

See [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md) for what these tables
mean and how routing uses them.

### Monitoring and traffic capture

XRouter can trace traffic at every protocol layer — invaluable for diagnosing a
link.

- **`MON`** / **`MONITOR`** turns live tracing on and off.
- **`CAP`** / **`CAPTURE`** writes the traced traffic to a disk file.
- **`MMASK`** selects *which* protocols are traced, as a hex bitmask. The
  default mask is **`0x3ff`**; setting **`0x43ff`** additionally hex-dumps each
  packet. As Paula G8PZT puts it, to log all traffic to a file: turn `MON` on,
  turn `CAP` on, and for a hex dump of every packet set `MMASK 43ff` instead of
  the default `3ff`.
- `MPORTS` / `MPORT` and `MTO` narrow monitoring to particular ports or
  destinations.

More on log files and their layout is on
[Logging and monitoring](../admin/logging-and-monitoring.md).

### Process and daemon control

The node and its services can be steered without stopping the program.

- **`RESTART`** re-starts XRouter (re-reading its configuration). Note that after
  replacing the executable you must mark the new file executable first, or the
  restart is refused.
- **`START`** and **`STOP`** start and stop the node's daemons (such as the
  IGate); `STOP` with no argument lists the running daemons.
- **`KILL`** terminates a session; it accepts multiple sessions, and `ALL`.

### IP, NAT and RIP

Because XRouter carries its own multi-homed TCP/IP stack, the sysop manages IP
from the prompt as well as from `IPROUTE.SYS`:

- **`IP`** exposes the IP routing-table sub-commands (ordinary users get the
  read-only `IPROUTE` / `IPROUTES` view).
- **`NAT`** controls Network Address Translation.
- **`RIP`** controls the RIP routing-information exchange.
- `ARP`, `DNS`, `IPADDRESS`, `IPLINK` and others tune the surrounding IP
  machinery.

The stack itself is described in [The TCP/IP stack](../networking/tcpip.md).

### Security

XRouter includes several layers a sysop can drive live:

- **`ACL`** edits the IP Access Control List — the live packet-filter rules.
- **IP ban** — the Intrusion Detection System maintains a banned-IP list
  (`IPBAN.SYS`); `IDS` controls it, including honeypot ports and unbanning.
- **`IDS`** is the front end to that Intrusion Detection System.
- Packet capture (**PCAP**) and the tracing commands above let you see exactly
  what is hitting the node.

General hardening guidance — sysop passwords, ACLs, not running as root, the
`IPADDRESS` security behaviour — is collected on
[Security and hardening](../admin/security-hardening.md).

### File maintenance (PZTDOS)

XRouter has a small built-in DOS-style file shell, **PZTDOS**, entered with the
`DOS` command. Its file commands (`DIR`, `COPY`, `DEL`, `TYPE`, `EDIT` and the
rest) let a sysop manage the node's files remotely over a slow link. **These
commands are sysop-only**; they are listed in the
[command reference](command-reference.md).

## Where to go next

- The complete command catalogue with scope and abbreviations:
  [Command reference](command-reference.md).
- The everyday user view of the same prompt:
  [The node command prompt](node-prompt.md).
- Hardening a public node: [Security and hardening](../admin/security-hardening.md).

---

**Sources:** [In-program manual, Section 1 — General Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man1)
· [Section 3 — PZTDOS Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man3)
· [Configuration directives, `SYSOP` (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man7)
· [XRouter support group (groups.io)](https://groups.io/g/xrouter)
