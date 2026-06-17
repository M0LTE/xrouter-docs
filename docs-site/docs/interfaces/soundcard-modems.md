# Soundcard modems (Direwolf, UZ7HO, QtSoundModem)

This page uses a **software modem** as XRouter's TNC: the modem turns audio from a soundcard
into packets, and presents itself to XRouter as a KISS TNC. It is for a sysop with a radio
wired to a soundcard (or a sound-fob) who wants packet without buying a hardware TNC. If you
have a real KISS TNC, see [KISS TNCs](kiss-tncs.md), the XRouter side is almost identical.

A soundcard modem replaces the hardware TNC with a program. XRouter doesn't care that the
"TNC" is software; it just speaks KISS to it. The modems the community runs successfully with
XRouter are:

- **Direwolf** (cross-platform; KISS over a pseudo-terminal or over TCP),
- **UZ7HO SoundModem** (Windows),
- **QtSoundModem** (cross-platform), and
- Thomas Sailer's **soundmodem** (Linux), which can be configured to act as a KISS TNC.

Any of these can drive XRouter. You connect to them in one of two ways: **KISS over a serial
device** (often a pseudo-terminal), or **KISS over TCP**.

## Option A: KISS over a serial / pseudo-terminal

If the modem creates a serial-style device (a real serial port, or a Linux pseudo-terminal
such as `/dev/pts/N`), treat it exactly like a hardware KISS TNC: an **`ASYNC`** interface
with `PROTOCOL=KISS`. This is how Thomas Sailer's `soundmodem` is wired, set it up to expose
a KISS device, then point XRouter's `COM=` at that device.

```ini
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/pts/N       ; the pseudo-terminal your modem creates (exact node varies)
        SPEED=57600
        FLOW=0               ; never XON/XOFF with KISS
        MTU=256
ENDINTERFACE

PORT=1
        ID=Soundcard 1200 baud
        INTERFACENUM=1
ENDPORT
```

The exact device the soundmodem creates varies, often a `/dev/pts/N` node or a symlink you
create yourself, so consult the modem's own documentation for the device it presents, and
substitute it for `COM=`.

## Option B: KISS over TCP (Direwolf)

Direwolf can offer **KISS over a TCP socket** instead of a serial device. XRouter connects to
that socket with a **`TCP`** interface (not `ASYNC`) carrying `PROTOCOL=KISS`. The interface
needs to know the modem's address and TCP port:

- `IOADDR`, the IP address of the modem. Use `127.0.0.1` when Direwolf runs on the same
  machine as XRouter (the default is localhost).
- `INTNUM`, the TCP port the modem is listening on.

Direwolf's KISS-over-TCP service listens on **port 8001 by default**. (That is Direwolf's
default, set in its own configuration with `KISSPORT`; it is not an XRouter setting. If you
changed it in Direwolf, use the value you chose.)

```ini
INTERFACE=1
        TYPE=TCP
        PROTOCOL=KISS
        IOADDR=127.0.0.1     ; Direwolf on the same machine
        INTNUM=8001          ; Direwolf's KISS-TCP port (its default)
        MTU=256
ENDINTERFACE

PORT=1
        ID=Direwolf 1200 baud
        INTERFACENUM=1
ENDPORT
```

!!! warning "Use a current XRouter build with Direwolf"
    Very old XRouter builds had a problem with Direwolf KISS-over-TCP, where the link would
    repeatedly drop and reconnect. As Paula G8PZT put it, *"I don't think Direwolf was much
    of a thing when that version of xrpi was written."* The behaviour was fixed in later
    builds, so
    if you see XRouter constantly reconnecting to Direwolf, **update to a current build**
    before chasing anything else. See [Upgrading and versions](../admin/upgrading.md).

## Rig control

A soundcard modem handles audio, but something still has to key the transmitter and (if you
want it) set the radio's frequency and mode. PTT is commonly handled by the modem itself
(VOX, a serial/GPIO line, or CAT). XRouter can also control the radio directly, or via
**Hamlib**, so frequency and mode can be managed from the node rather than the modem. Rig
control is configured separately from the interface, see the `RADIO` block in
[Interfaces](../configuration/interfaces.md), and is independent of whether you chose the
serial or the TCP path above.

!!! note "AGW is an alternative to KISS here"
    Direwolf, UZ7HO SoundModem and QtSoundModem can each present an **AGW** interface instead
    of KISS. If you would rather connect that way, or you are already running an AGW Packet
    Engine, see [AGWPE](agwpe.md). Neither approach is "more correct"; use whichever is
    easier to wire up on your system.

---

**Sources:** [XRouter support group (groups.io): usable modems & Hamlib](https://groups.io/g/xrouter/message/4118) ·
[groups.io: soundmodem as a KISS TNC](https://groups.io/g/xrouter/message/273) ·
[In-program manual, OARC wiki: §6 Installation & Configuration](https://wiki.oarc.uk/packet:xrouter:docs:man6)
(ASYNC and TCP interfaces, `KISSOPTIONS`). The Direwolf reconnect history and `KISSPORT`
default are community knowledge (Direwolf's own documentation).
