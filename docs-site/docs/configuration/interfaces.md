# Interfaces

An **interface** is a point of connection between XRouter and somewhere else — a serial port
driving a TNC, a soundcard link, an Ethernet adaptor, or a socket carrying AX.25 across the
internet. Interfaces are defined in `INTERFACE=n … ENDINTERFACE` blocks in the GLOBAL section
of `XROUTER.CFG`, and every [port](ports.md) attaches to one. This page covers the block,
its two mandatory keywords, and the `TYPE` / `PROTOCOL` values that select the driver.

For step-by-step setup of particular hardware, see the [Interfaces &
modems](../interfaces/index.md) section; this page is the configuration-file reference that
underpins it.

## The interface block

Each block opens with `INTERFACE=n`, where `n` is a unique number greater than zero, and
closes with `ENDINTERFACE`. Inside, **two keywords are mandatory** — XRouter refuses to start
without them:

- **`TYPE`** — the kind of interface (the hardware or transport). See the table below.
- **`MTU`** — the Maximum Transmission Unit in bytes.

Most interface types also need a **`PROTOCOL`**, and you will usually see an `ID` for the
ports display too. A bare minimum looks like this:

```ini
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/ttyACM0
        SPEED=57600
        MTU=256
ENDINTERFACE
```

!!! tip "MTU: if in doubt, use 256"
    `MTU` may be set up to 1500, but values above 256 are not recommended on AX.25 ports —
    hardware TNC buffers and on-air efficiency both favour smaller frames. The standard
    advice is simply: **if in doubt, use 256.** Larger MTUs make sense only on wired or IP
    interfaces (SLIP, PPP, Ethernet), not on a radio channel.

One interface can carry several ports if the hardware is multi-channel (each port picks a
`CHANNEL`, A–P). Single-channel types take one port; emulator types (such as TNC2 host-mode)
take none.

## TYPE values

`TYPE` selects the driver. The accepted values are:

| `TYPE` | Connects XRouter to… |
| --- | --- |
| `ASYNC` | A character-by-character serial device: RS-232, a USB-serial adaptor, or a pseudo-TTY. Drives KISS TNCs, wired links, PSTN modems and serial NET/ROM links. |
| `AGW` | An AGW Packet Engine or Direwolf acting as a packet engine (XRouter is the *client*). Up to 16 ports. |
| `AXIP` | AX.25 carried in raw IP (IP protocol 93). For inter-node internet links. |
| `AXUDP` | AX.25 carried over UDP. More versatile than AXIP and able to share one public IP. |
| `AXTCP` | AX.25 carried over a TCP stream — traverses NAT and firewalls. One port only. |
| `TCP` | A general TCP pseudo-interface that tunnels another protocol inside a TCP stream (e.g. KISS-over-TCP to Direwolf). |
| `UDP` | A general UDP tunnel for frame-delimited protocols (e.g. KISS-over-UDP). |
| `TUN` | A Linux `tun` device — an IP-layer link between XRouter's stack and the Linux kernel. |
| `EXTERNAL` | Ethernet / WiFi. The name is historical; on XrPi/XrLin no external driver is needed. |
| `LOOPBACK` | An internal loopback inside XRouter's own stack. Test and development only — and the shipped dummy config. |
| `YAM` | A YAM ("Yet Another Modem") HDLC modem (1200 / 2400 / 9600). |

!!! note "LOOPBACK is for getting started, not for the air"
    The shipped "dummy" configuration uses a `LOOPBACK` interface and port so a new install
    will start. It has no on-air use; comment it out once you have a real interface and port.

## PROTOCOL values

`PROTOCOL` tells the interface what to speak over the chosen `TYPE`. The accepted values are:

| `PROTOCOL` | Used for |
| --- | --- |
| `KISS` | Driving KISS TNCs and wired KISS links. The usual choice for TNCs and soundcard modems. |
| `AX25` | Native AX.25. |
| `NETROM` | A NET/ROM backend serial link to another node. |
| `ETHER` | Ethernet (used with `TYPE=EXTERNAL`). |
| `PPP` | Point-to-Point Protocol — TCP/IP over a serial line. |
| `SLIP` | Serial Line Internet Protocol — TCP/IP over a serial line. |
| `MODEM` | A Hayes-compatible PSTN modem. |
| `DEDHOST` | WA8DED host-mode emulation, for hosting applications such as FBB. |
| `TNC2` | TNC2 emulation. |
| `HDLC` | For the YAM modem and some `EXTERNAL` drivers. |
| `IP` | Used with `TYPE=TUN`. |
| `ASCII` | A dumb-terminal (TTY) sysop console on an `ASYNC` port. |
| `NONE` | Used with `TYPE=LOOPBACK`. |

!!! warning "An interface needs a protocol"
    Most interface types fail to start with a missing or unrecognised protocol —
    `ERROR … Bad or missing protocol`. If an interface won't come up, check `PROTOCOL` first,
    then `TYPE` and `MTU`.

## Common per-interface keywords

Beyond `TYPE`, `MTU` and `PROTOCOL`, the keywords you will most often use are:

| Keyword | Meaning |
| --- | --- |
| `COM` | The serial device for `ASYNC`/`YAM` (e.g. `/dev/ttyUSB0`, `/dev/ttyACM0`); the tunnel name for `TUN` (e.g. `tun99`); a remote UDP port for `UDP`. `COM=0` parks an interface (a handy way to disable it without deleting it). |
| `SPEED` | The serial line speed for `ASYNC` (e.g. `57600`), or the radio baud rate for `YAM`. No comma or thousands separator. |
| `FLOW` | Serial flow control on `ASYNC`: `0` none, `1` hardware (RTS/CTS), `2` software (XON/XOFF), `3` both. Do **not** use XON/XOFF with KISS. |
| `KISSOPTIONS` | KISS variations: `NONE` (plain KISS, the default), `POLLED`, `CHECKSUM`, `ACKMODE`, `SLAVE`, `NOPARMS`. `POLLED` and `SLAVE` are mutually exclusive; BPQ KISS EPROMs need `POLLED`,`CHECKSUM`. |
| `CONFIG` | Type-specific configuration — e.g. the AGWPE password for `AGW`, or `<alias> <host> <port>` client lines for `AXTCP`/`TCP`. |
| `INTNUM` | Recycled per type: the AGWPE TCP port for `AGW`; the listen port for `AXTCP`/`TCP`/`UDP`. |
| `IOADDR` | Recycled per type: the target IP address for `AGW`/`TCP`/`UDP` (defaults to `127.0.0.1`). |
| `UDPLOCAL` / `UDPREMOTE` | The local and remote UDP ports for AXUDP links (these are usually set on the *port*, default 93). |

The `INTNUM` and `IOADDR` names are legacy: on the old DOS builds they were a hardware IRQ
and I/O address. On XrPi/XrLin they have been repurposed as the TCP/UDP parameters above.

## Common hardware → TYPE / PROTOCOL

This table maps the usual ways onto the air to the interface settings that drive them. Follow
the link for the full setup, including the matching radio/modem configuration.

| You have… | `TYPE` | `PROTOCOL` | Setup page |
| --- | --- | --- | --- |
| A hardware KISS TNC (incl. NinoTNC) on serial/USB | `ASYNC` | `KISS` | [KISS TNCs](../interfaces/kiss-tncs.md) |
| A soundcard modem (Direwolf, UZ7HO, QtSoundModem) presenting a KISS serial/pty | `ASYNC` | `KISS` | [Soundcard modems](../interfaces/soundcard-modems.md) |
| A soundcard modem presenting KISS-over-TCP (e.g. Direwolf) | `TCP` | `KISS` | [Soundcard modems](../interfaces/soundcard-modems.md) |
| An AGW Packet Engine / Direwolf as a packet engine | `AGW` | — | [AGWPE](../interfaces/agwpe.md) |
| An internet link to a partner node (raw IP) | `AXIP` | — | [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) |
| An internet link to a partner node (over UDP) | `AXUDP` | — | [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) |
| An internet link through NAT/firewalls (over TCP) | `AXTCP` | — | [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) |
| Ethernet or WiFi (a LAN port) | `EXTERNAL` | `ETHER` | [The TCP/IP stack](../networking/tcpip.md) |
| A YAM modem | `YAM` | `HDLC` | [Interfaces & modems](../interfaces/index.md) |

Once an interface is defined, give it a [port](ports.md) so XRouter's core can use it — and
remember the ordering rule: **the interface must appear above the port that references it**.

---

**Sources:**
[Installation and configuration topics, OARC wiki (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[Configuration directives, OARC wiki (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[ohiopacket.org XRPi mirror — interfaces](https://ohiopacket.org/xrpi/docs/ifaces.htm) ·
[XRouter support group, groups.io](https://groups.io/g/xrouter)
