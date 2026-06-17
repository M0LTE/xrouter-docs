# Ports

A **port** is where XRouter's core meets an [interface](interfaces.md). In AX.25 terms a port
usually corresponds to a single radio channel: all AX.25 operations are port-based. Ports are
defined in `PORT=n … ENDPORT` blocks in the GLOBAL section, and every port must attach to an
interface that has already been defined. This page covers the block, its mandatory keywords,
and the AX.25 timing and NET/ROM behaviour you tune per port.

## The port block

Each block opens with `PORT=n` and closes with `ENDPORT`. **Two keywords are mandatory:**

- **`ID`**, the text shown for this port on the `PORTS` display. Make it descriptive.
- **`INTERFACENUM`**, the number of the [interface](interfaces.md) this port binds to.

On a multi-channel interface, add `CHANNEL` (A–P) to pick the channel; it defaults to `A` and
is not needed on AXIP/AXUDP/AXTCP. A minimal port is therefore:

```ini
PORT=1
        ID=144.950 MHz 1200 baud
        INTERFACENUM=1
ENDPORT
```

XRouter will not run unless there is at least one port. Remember the ordering rule from the
[configuration model](index.md): **the interface named in `INTERFACENUM` must appear earlier
in the file than this port.**

## AX.25 timing and behaviour

These keywords shape how AX.25 behaves on the channel, how it shares the air, how long it
waits, and how many frames it sends before expecting an acknowledgement. Several also exist as
global defaults; setting them in the port block overrides the default for that channel.

| Keyword | Controls | Default |
| --- | --- | --- |
| `PACLEN` | Maximum AX.25 packet (payload) length. | 120 |
| `MAXFRAME` | Maximum outstanding (unacknowledged) AX.25 frames. | 3 |
| `FRACK` | Frame-acknowledgement timeout, milliseconds (AX.25 T1). | 7000 |
| `RESPTIME` | Delayed-acknowledgement timer, milliseconds. | see note |
| `PERSIST` | CSMA probability to transmit (0–255). | 64 |
| `SLOTTIME` | CSMA slot interval, milliseconds. | 100 |
| `TXDELAY` | Delay between PTT and the start of data, milliseconds. | 300 |
| `TXTAIL` | Delay between the end of data and PTT drop, milliseconds. | 100 |
| `RETRIES` | Maximum connect / disconnect / resend attempts. | 10 |

!!! note "Defaults vary by version, `RESPTIME` especially"
    Documented defaults differ between the in-program manual and older mirrors. `RESPTIME`
    is the clearest case: it is documented as **1500 ms** in one place and **2000 ms** in
    another. On Windows builds, timing jitter makes a low value unreliable and the author
    suggests `RESPTIME=200` rather than something tiny. Treat the numbers in this table as
    starting points, set the ones that matter to you explicitly, and confirm on your own
    build with the matching sysop command.

!!! tip "MAXFRAME above 7 enables modulo-128"
    `MAXFRAME` defaults to 3 and may be raised on good channels. Setting `MAXFRAME` greater
    than 7 makes XRouter attempt **extended (modulo-128) AX.25**, which allows up to 63
    outstanding frames, useful on a fast, clean link but pointless on a marginal one. On a
    busy 1200-baud channel keep `FRACK` high (the default 7000 ms is sensible); lower values
    such as ~2000 ms suit fast or wired links only. A rough rule for `PERSIST` is
    255 ÷ (number of users sharing the channel).

## NET/ROM on the port

`QUALITY` sets the default NET/ROM neighbour quality advertised over this port, how good a
path *through* this port is considered to be. A few related keywords go alongside it:

| Keyword | Controls | Default |
| --- | --- | --- |
| `QUALITY` | Default NET/ROM neighbour quality for the port. | 10 |
| `MINQUAL` | Minimum quality for a node to be added to the table (port override). | 10 |
| `MINTXQUAL` | Minimum quality for a node to be broadcast from this port. | 0 |
| `NODESINTERVAL` | Minutes between NODES broadcasts (port override). | 60 |

!!! note "QUALITY=0 disables Layer 3/4 on the port"
    Setting `QUALITY=0` turns off NET/ROM Layer-3/Layer-4 on that port, the port carries
    plain AX.25 only, with no NET/ROM routing. This is exactly what you want on a busy
    user-access channel where you don't want node broadcasts. Separately, a `QUALITY` in the
    range **256–511 enables AUTOQUAL**, XRouter's automatic route-quality measurement, rather
    than a fixed quality. Use the per-port `NODESINTERVAL`, never a low *global* one, if a
    single neighbour needs more frequent broadcasts.

## Behaviour flags

Two bitmask keywords control connection handling and digipeating. Add the bit values you want:

`CFLAGS` (default **3**) governs AX.25 up/downlinking:

- `1`, allow uplinks (users may connect *to* the node).
- `2`, allow downlinks (the node may connect *out* on behalf of users).
- `4`, applications may downlink unconditionally.
- `8`, suppress L3RTT on this port.
- `16`, allow Layer-2 fragmentation.

`DIGIFLAG` (default **7**) governs digipeating, with bits for UI and non-UI digipeating,
the APRS New-N paradigm (`UITRACE`/`WIDEn-n` and `UIFLOOD`), and IGate to/from the internet.
The legacy `RELAY`/`TRACE`/`WIDE` bits are deprecated in favour of the New-N scheme. See
[APRS digipeater and IGate](../subsystems/aprs-igate.md) for the full bit list and recommended
values.

## MHeard and broadcasting

| Keyword | Controls |
| --- | --- |
| `MHEARD` | Enables the "stations heard" list on the port and sets its size (0–50). |
| `MHFLAGS` | Which kinds of station the MHeard list records (direct, via digipeaters, digipeated). Default 255. |
| `BCAST` | Destination call(s) for UI "broadcasting" on the port. |
| `BCFROM` | Which callsigns are permitted to use UI broadcast. |
| `UNPROTO` | Destination and digipeater path for unproto (UI) broadcasts. |

## The SYSOP keyword: handle with care

`SYSOP` marks a whole port as sysop-privileged. It defaults to `0` (off), and it should
almost always stay there.

!!! danger "SYSOP=1 grants full sysop with no password"
    If you set `SYSOP=1`, **every** user who connects on that port is given full sysop
    status, with no password challenge at all. This is intended **only for genuinely secure
    links**, such as a direct RS-232 connection or a trusted Ethernet segment that nobody else
    can reach. Never set `SYSOP=1` on an RF port or any port reachable from the internet:
    doing so hands node control to anyone who can connect. For remote sysop access over RF or
    the internet, use a password instead (`PASSWORD.SYS` and the `@` challenge). See [Sysop
    access and commands](../commands/sysop.md) and [Security and
    hardening](../admin/security-hardening.md).

## Defaults at a glance

The values below are collected for reference. They are the documented defaults, but **defaults
can vary by version**, `RESPTIME` is the notable example (1500 vs 2000). Set the ones you
care about explicitly.

| Keyword | Default | | Keyword | Default |
| --- | --- | --- | --- | --- |
| `PACLEN` | 120 | | `QUALITY` | 10 |
| `MAXFRAME` | 3 | | `MINQUAL` | 10 |
| `FRACK` | 7000 | | `MINTXQUAL` | 0 |
| `RESPTIME` | 1500 / 2000 | | `NODESINTERVAL` | 60 |
| `PERSIST` | 64 | | `CFLAGS` | 3 |
| `SLOTTIME` | 100 | | `DIGIFLAG` | 7 |
| `TXDELAY` | 300 | | `MHFLAGS` | 255 |
| `TXTAIL` | 100 | | `SYSOP` | 0 |
| `RETRIES` | 10 | | `CHANNEL` | A |

A common AX.25 user-access port, with NET/ROM suppressed and a sensible MTU-friendly `PACLEN`:

```ini
PORT=1
        ID=144.950 MHz 1200 baud users
        INTERFACENUM=1
        PACLEN=200
        MAXFRAME=4
        QUALITY=0          ; AX.25 only on this channel, no NET/ROM broadcasts
ENDPORT
```

Worked, end-to-end configurations are on [Worked example configs](examples.md).

---

**Sources:**
[Configuration directives, OARC wiki (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[Installation and configuration topics, OARC wiki (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[XROUTER.CFG, OARC wiki (man8)](https://wiki.oarc.uk/packet:xrouter:docs:man8) ·
[ohiopacket.org XRPi mirror: ports](https://ohiopacket.org/xrpi/docs/ports.htm)
