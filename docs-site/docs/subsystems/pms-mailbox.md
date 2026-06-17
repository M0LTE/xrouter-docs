# PMS mailbox

XRouter has a small mailbox of its own, the **Personal Message Store**, or PMS.
This page explains what it is (and what it deliberately isn't), how to switch it on,
how to tune it, and how callers reach it. It assumes you already have a node running;
if not, start with [Getting started](../getting-started/index.md).

## What the PMS is and isn't

The PMS is an **integral personal mailbox**: a place for users to leave you messages,
for you to leave messages for them, and for simple store-and-forward mail between
neighbouring nodes. It is built into the node, needs no separate program, and shares
the node's interfaces and routing. For a single sysop who wants a "leave me a message"
facility and a little inter-node mail, it is all you need.

It is **not a full BBS.** It has no message-board hierarchy, no bulletin distribution
on the scale of a proper bulletin board, and no file libraries. Paula G8PZT is explicit
about this: *"If you want to run a BBS, then BPQ is the one for you. XRouter only has a
PMS."* That is a design choice, not a gap to apologise for, the PMS does mailbox-shaped
jobs well and stays out of the way.

!!! tip "Need a full BBS? Pair XRouter with FBB"
    If you want bulletins, areas and file serving, the established approach is to run a
    real BBS, most commonly **FBB**, *alongside* XRouter, and let XRouter act as the
    packet engine for it. XRouter emulates a **WA8DED host-mode TNC** (`DEDHOST`) for
    exactly this purpose, so FBB connects to XRouter rather than to the radio directly.
    See [Linking nodes and interoperating](../networking/linking-and-interop.md) for how
    host-mode application links are wired up.

## Enabling the mailbox

The mailbox is governed by a handful of global directives in `XROUTER.CFG`. The presence
of `PMSCALL` is what brings the PMS to life: omit it and the mailbox is disabled.

| Directive | Purpose |
| --- | --- |
| `PMSCALL` | The mailbox's own AX.25 / NET-ROM callsign. Setting it enables the PMS; omitting it disables it. A spare SSID (conventionally `-2`) is usual. |
| `PMSALIAS` | The mailbox alias. |
| `PMSQUAL` | The NET-ROM quality at which the PMS is advertised in the nodes table. `0` (the default) keeps it from being broadcast as a separate node, callers still reach it, but it doesn't bloat everyone's tables. |
| `PMSTYPE` | Selects the mailbox mode. **Mode `1` is the standard PMS** and the right choice for almost everyone. |

A minimal, working mailbox looks like this:

```ini
PMSCALL=M0XXX-2
PMSALIAS=XXXPMS
PMSQUAL=0          ; reachable, but not broadcast as its own node
PMSTYPE=1          ; standard personal message store
```

There is also a `PMSHADDR` directive for the mailbox's **hierarchical address** (the
`@ BBS.region.country` style routing address used by forwarding mail systems); set it if
you intend to exchange forwarded mail with other mailboxes.

!!! note "Callsigns and aliases on Linux are case-sensitive in files, not in keywords"
    As everywhere in `XROUTER.CFG`, the keyword names are not case-sensitive, but on
    Linux the data files the mailbox reads and writes live in the UPPER-CASE `/PMS`
    directory. See [System and data files](../configuration/system-files.md).

## Tuning with PMS.CFG

Finer behaviour lives in a separate optional file, **`PMS.CFG`**, kept in the node's
`/PMS` subdirectory. If it is absent, the mailbox runs on sensible defaults; you only
need it to change them. It uses the familiar `keyword=value` form, and its settings
cover housekeeping and forwarding rather than identity. Useful keys include:

| `PMS.CFG` keyword | What it controls |
| --- | --- |
| `SysopCallsign` | The callsign treated as the mailbox owner / sysop. |
| `HousekeepInterval` | How often expiry and tidy-up runs (hours). |
| `ForwardInterval` | How often queued mail is offered to neighbours (seconds). |
| `FwdKickOnRcv` | Whether receiving mail triggers an immediate forward attempt. |
| `AutoRenumber` | Whether message numbers are automatically renumbered. |
| `MaxAgeB`, `MaxAgePR` | Maximum age (days) for bulletin and private/personal mail before expiry. |

Mail acceptance, holding, forwarding and distribution are steered by the related `*.SYS`
control files, `FWD.SYS`, `DISTRIB.SYS`, `HOLD.SYS`, `REJECT.SYS`, `EXPORT.SYS` and the
`BADWORDS.SYS` filter, all of which also live in `/PMS`. Those are covered in
[System and data files](../configuration/system-files.md); reach for them only when you
actually run inter-node mail.

## Reaching the mailbox

Because the PMS has its own callsign you can always connect to it directly
(`CONNECT M0XXX-2`). The tidier route, though, is **NetRom-X service 2**: XRouter assigns
each subsystem a standard service number under the *node's* callsign, and **service 2 is
always the PMS**. From any XRouter node a caller can do:

```text
CONNECT M0XXX 2
```

and land in your mailbox without needing to know its separate SSID. This is the same
scheme that puts the node-info server on service 1 and the chat server on service 8,
see [NetRom-X service numbers](../networking/netromx-services.md) for the full picture
and why it exists (it spares the network a forest of SSIDs).

Once connected, users drive the mailbox with its own command set, listing, reading,
sending and killing messages. Those commands are documented with the rest of the node
commands in the [Command reference](../commands/command-reference.md).

---

**Sources:** [In-program manual, OARC wiki, Section 9 (subsystems/servers) and Section 7
(directives)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[Mailbox help](https://wiki.oarc.uk/packet:xrouter:docs:mailboxhelp) ·
[ohiopacket.org XRPi mirror: PMS](https://ohiopacket.org/xrpi/docs/index.htm) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
