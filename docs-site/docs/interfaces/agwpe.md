# AGWPE

This page connects XRouter to an **AGW Packet Engine** — SV2AGW's AGWPE, or any program that
speaks the AGW protocol, including **Direwolf**, **UZ7HO SoundModem** and **QtSoundModem** in
AGW mode. It is for a sysop who already runs an AGW engine to drive their radios and wants
XRouter to use it. The reverse case — XRouter pretending to *be* an AGW engine for other
apps — is covered at the end.

The AGW protocol lets one program present multiple radio ports over a single TCP connection.
Where KISS gives you one TNC per interface, an AGW interface can carry **up to 16 ports**
(one per AGW "radio port"), each mapped to an XRouter port via a channel letter.

## XRouter as an AGW client

Use a **`TYPE=AGW`** interface. The keywords that matter:

| Keyword | What it sets | Default |
| --- | --- | --- |
| `TYPE=AGW` | AGW client interface | — (mandatory) |
| `MTU=<bytes>` | Max transmission unit | mandatory; use `256` on AX.25 |
| `IOADDR=<ip>` | IP address of the AGW engine | `127.0.0.1` (localhost) |
| `INTNUM=<port>` | The engine's TCP port | `8000` |
| `CONFIG=<password>` | AGW login password, if the engine requires one | — |

When `CONFIG` supplies a password, the username presented to the engine is your `NODECALL`.
For a local engine with no password, you can omit `CONFIG` and `IOADDR` and accept the
localhost default.

### Example

XRouter talking to a local AGW engine (AGWPE or Direwolf in AGW mode) on the default port,
with one radio port:

```ini
INTERFACE=1
        TYPE=AGW
        IOADDR=127.0.0.1     ; AGW engine on this machine (the default)
        INTNUM=8000          ; AGW engine's TCP port (the default)
        MTU=256
ENDINTERFACE

PORT=1
        ID=AGW radio port 1
        INTERFACENUM=1
        CHANNEL=A            ; first AGW radio port
ENDPORT
```

To use a second radio port on the same engine, add another `PORT` block pointing at the same
`INTERFACENUM` but with `CHANNEL=B`, and so on (up to 16 channels, `A`–`P`).

!!! tip "AGW vs KISS for a soundcard modem"
    Direwolf, UZ7HO SoundModem and QtSoundModem can present *either* a KISS interface or an
    AGW interface. If you would rather connect via KISS — for instance over a serial device
    or Direwolf's KISS-over-TCP — see [Soundcard modems](soundcard-modems.md). Either path
    works; choose whichever is simpler on your system.

!!! warning "An AGW engine on a remote host needs IP enabled"
    If the AGW engine is on another machine (`IOADDR` is not localhost), the connection is an
    ordinary TCP link and is subject to the `IPADDRESS` rule: with `IPADDRESS=0.0.0.0` all IP
    activity is disabled. Set a non-zero address such as `10.1.1.1`. See
    [Global settings](../configuration/global-settings.md).

## XRouter as an AGW host (the other direction)

XRouter can also **emulate an AGW Packet Engine**, so that other applications — a logger, a
terminal, an APRS client — can use XRouter as *their* packet engine. This is the opposite of
the interface above: here other programs connect *in* to XRouter.

The emulator is enabled with the global directive **`AGWPORT`**, which sets the TCP port
XRouter listens on for AGW clients; the default is **`8000`**.

```ini
AGWPORT=8000        ; XRouter offers an AGWPE host on port 8000
```

A client application then points its AGWPE connection at XRouter's IP address and that port.
Because the emulator listens on a TCP port, it is — like all IP services — subject to the
`IPADDRESS` rule above.

!!! note "Don't run a client and host on the same port by accident"
    If XRouter is *using* an AGW engine (client, `INTNUM=8000`) **and** *emulating* one
    (`AGWPORT=8000`) on the same machine, make sure the two don't collide on port 8000. Give
    the emulator a different `AGWPORT`, or point the client at the real engine's port.

---

**Sources:** [In-program manual, OARC wiki — §6 Installation & Configuration](https://wiki.oarc.uk/packet:xrouter:docs:man6)
(AGW client interface keywords; AGW host emulation via `AGWPORT`) ·
[ohiopacket.org XRPi mirror — interfaces](https://ohiopacket.org/xrpi/docs/ifaces.htm) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
