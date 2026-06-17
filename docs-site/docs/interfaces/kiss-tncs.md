# KISS TNCs

This page connects a hardware **KISS TNC**, including a **NinoTNC** or a BPQ-style KISS
EPROM, to XRouter. It is for a sysop who has a TNC on a serial or USB port and wants a
working radio port. If you are using a *software* modem (Direwolf, UZ7HO, QtSoundModem),
read [Soundcard modems](soundcard-modems.md) instead; the interface is similar but the
device is different.

KISS is the simplest, most universal way to attach a TNC: the TNC does the modulation and
HDLC framing, and XRouter does everything above the link layer. Almost every modern TNC
speaks KISS, so this is the path most sysops take.

## The interface

A wired KISS TNC is an **`ASYNC`** (serial) interface running the **`KISS`** protocol. The
keywords you will use:

| Keyword | What it sets | Notes |
| --- | --- | --- |
| `TYPE=ASYNC` | Serial interface | Mandatory. |
| `PROTOCOL=KISS` | Speak KISS to the TNC | Mandatory, without a protocol you get a *bad or missing protocol* error. |
| `COM=<device>` | The serial device | e.g. `/dev/ttyACM0` (USB, typical for a NinoTNC) or `/dev/ttyUSB0` (USB-serial). On Windows, a `COMn` name. |
| `SPEED=<baud>` | Host↔TNC serial speed | The wire speed between the computer and the TNC, **not** the RF baud rate. No comma (e.g. `57600`). |
| `FLOW=<n>` | Serial flow control | `0` none, `1` hardware RTS/CTS, `2` software XON/XOFF, `3` both. Use `0` for KISS, never XON/XOFF, which corrupts binary KISS frames. |
| `MTU=<bytes>` | Max transmission unit | Up to 1500, but **256 or less is recommended on AX.25 ports**. *"If in doubt, use 256."* |
| `KISSOPTIONS=<list>` | KISS dialect tweaks | See below. Default is plain KISS. |

!!! tip "SPEED is the serial speed, not the over-the-air speed"
    `SPEED` is how fast the computer talks to the TNC down the cable (commonly `9600` or
    `57600`). The RF data rate is a property of the TNC and the channel; set that on the TNC
    itself. For a NinoTNC the USB serial speed is fixed by the device, so match what its
    documentation specifies.

### KISSOPTIONS

Most TNCs need nothing here, plain KISS is the default. Set `KISSOPTIONS` only when your TNC
requires a particular dialect. The recognised values:

| Option | Meaning |
| --- | --- |
| `NONE` | Plain KISS. The default; what most TNCs use. |
| `POLLED` | For TNCs that transmit to the host only when polled. |
| `CHECKSUM` | KISS frames are protected by a checksum. |
| `ACKMODE` | The TNC tells XRouter when a frame has actually been sent on air. |
| `SLAVE` | XRouter behaves like a polled KISS TNC (transmits only when commanded). |
| `NOPARMS` | Don't send KISS parameter-setting commands to the TNC. |

`POLLED` and `SLAVE` are mutually exclusive. **BPQ-style KISS EPROMs require `POLLED` and
`CHECKSUM`** (`ACKMODE` optional). Combine options with commas, for example
`KISSOPTIONS=POLLED,CHECKSUM`.

!!! note "ACKMODE and the NinoTNC"
    `ACKMODE` lets the TNC report when each frame is keyed up, which improves XRouter's
    timing. It is commonly used with the NinoTNC. It is optional, start without it if you
    are unsure, and add it once the basics work.

## A clean example

A single 1200/9600-baud TNC (such as a NinoTNC on USB) as port 1:

```ini
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/ttyACM0      ; your TNC device
        SPEED=57600           ; host-to-TNC serial speed
        FLOW=0                ; no flow control with KISS
        MTU=256
        KISSOPTIONS=ACKMODE   ; omit if your TNC doesn't support it
ENDINTERFACE

PORT=1
        ID=2m 1200 baud users
        INTERFACENUM=1
ENDPORT
```

The `PORT` block needs only `ID` (the text shown on the node's `PORTS` display) and
`INTERFACENUM` (the interface it binds to). Everything else, `PACLEN`, `TXDELAY`,
`QUALITY`, digipeating and so on, is a per-port setting with sensible defaults; see
[Ports](../configuration/ports.md) for the full list and the AX.25 timing parameters.

## KISS over TCP

Some KISS sources are reached over a TCP socket rather than a serial port, most often a
soundcard modem that offers "KISS over TCP". XRouter reaches those with a **`TCP`** interface
carrying `PROTOCOL=KISS`, rather than `ASYNC`. The mechanics (which keywords carry the host
and port) and a worked example live on the [soundcard modems](soundcard-modems.md) page,
since that is where KISS-over-TCP is normally used.

---

**Sources:** [In-program manual, OARC wiki: §6 Installation & Configuration](https://wiki.oarc.uk/packet:xrouter:docs:man6)
(ASYNC interface, `KISSOPTIONS`, MTU guidance) ·
[ohiopacket.org XRPi mirror: interfaces](https://ohiopacket.org/xrpi/docs/ifaces.htm) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
