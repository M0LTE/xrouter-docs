# APRS digipeater and IGate

XRouter is, among other things, an APRS station. It can digipeat APRS packets on RF using
the modern New-N paradigm, gateway between RF and the APRS-IS internet backbone, and serve
APRS clients such as UI-View. This page covers those features and how to turn them on; for
a primer on APRS itself, read [APRS in brief](../basics/aprs.md) first.

## The APRS identity

APRS traffic from the node — beacons it originates, and packets it gates to the internet —
carries the callsign set by the global `APRSCALL` directive. If you leave it unset, the
node's `NODECALL` is used. Set it when you want the APRS side of the station to appear
under its own callsign:

```ini
APRSCALL=M0XXX-1
```

## UI digipeating (WIDEn-N)

APRS digipeating is configured per port, on the ports that carry APRS. XRouter supports the
**New-N paradigm** — the `WIDEn-N` / `SSn-N` scheme that replaced the old RELAY/WIDE/TRACE
aliases — through two global directives that name the digipeating aliases:

| Directive | Role |
| --- | --- |
| `UITRACE` | The traceable alias (the `WIDE`/`TRACE`-style alias that records the path). |
| `UIFLOOD` | The flood alias, typically set to a regional/state code (for example a national or county prefix). |

Whether a given port digipeats at all, and how, is controlled by that port's `DIGIFLAG`
bitmask. The bits relevant to APRS UI digipeating include digipeating of UI frames and the
`UITRACE` (256) and `UIFLOOD` (512) New-N behaviours. `DIGIFLAG` can also be read and
changed live from the node prompt with the `DIGIFLAG` command. See
[Ports](../configuration/ports.md) for the full bit list.

## The IGate

An **IGate** (Internet Gateway) bridges APRS between RF and the APRS-IS internet servers.
XRouter's IGate is a daemon that you enable in two steps:

1. Set the global directive `IGATE=1` so the IGate starts at boot (you can also start it
   at runtime with `START IGATE`).
2. Provide an **`IGATE.CFG`** file describing the upstream server and filters.

```ini
IGATE=1            ; start the APRS IGate at boot
```

`IGATE.CFG` is a separate configuration file (kept in the node's working directory) holding
keywords such as `SERVER` (the APRS-IS host and port to connect to), `IFILTER` and
`PFILTER` (input and path filters), `RADIUS`, and timing controls like `PAUSE`, `WAIT` and
`MAXTRIES`. Its full format is documented in
[System and data files](../configuration/system-files.md).

### Controlling gating direction

Which way packets flow across the gateway is **not** set in `IGATE.CFG` — it is governed by
the same per-port `DIGIFLAG` bitmask, using two dedicated bits:

| `DIGIFLAG` bit | Meaning |
| --- | --- |
| `64` | Gate this port's traffic **to the internet** (RF → APRS-IS). |
| `128` | Gate traffic **from the internet** onto this port (APRS-IS → RF). |

Setting these per port lets you, for example, gate a busy port to the internet for
visibility while declining to inject internet traffic back onto a congested RF channel.

!!! warning "Gate from the internet to RF deliberately"
    The `128` (from-internet) bit injects internet-sourced APRS onto your radio channel.
    On a busy frequency this can add significant load and is widely discouraged unless you
    have a specific reason and the channel can carry it. Enable to-internet gating freely;
    enable from-internet gating with care, and only on ports where it is appropriate.

## The APRS message server

Beyond digipeating and gating, XRouter runs an **APRS message server** — a shell that lets
a connected user send and read APRS messages interactively, rather than from a dedicated
APRS terminal. Users enter it from the node prompt with the `AMSG` command, and there is a
companion `DX` command for distant stations heard and `WX` for APRS weather. The server
also listens on a TCP port for APRS client software (the APRS server port, default 1448),
so programs like UI-View can use XRouter as their APRS back-end.

The APRS messaging commands are documented in the
[Command reference](../commands/command-reference.md), and the related data and
configuration files in [System and data files](../configuration/system-files.md).

---

**Sources:** [In-program manual, OARC wiki — Section 9 (APRS, IGate, APRS server)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[APRS message server help](https://wiki.oarc.uk/packet:xrouter:docs:aprsmessageserverhelp) ·
[In-program manual — Section 7 (directives)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[ohiopacket.org XRPi mirror — APRS/IGate](https://ohiopacket.org/xrpi/docs/index.htm)
