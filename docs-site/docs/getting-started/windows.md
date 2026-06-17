# Getting started on Windows (XrWin)

This page gets an XRouter node running on Microsoft Windows. The current Windows build is
**XrWin**; the configuration is the same `XROUTER.CFG` as on Linux, so this page concentrates
on the few things that are genuinely Windows-specific and sends you to
[Your first XROUTER.CFG](first-config.md) for the rest.

XrWin is the actively-maintained Windows member of the XRouter family. It shares the same
codebase and the same feature set as the Linux builds; only the operating-system glue
differs.

!!! note "XrWin is current — XR32 is the legacy Windows build"
    There are two Windows programs in XRouter's history, and they are easy to confuse:

    - **XrWin** — the **current** Windows build, kept in step with the Linux versions.
    - **XR32** — the **legacy** 32-bit Windows build, frozen around 2013 and no longer
      developed. The well-known `XR32.doc` Word manual documents *this older* program.

    If you are setting up a node today, use **XrWin**. Treat `XR32.doc` as useful background
    only, and prefer the in-program manual (mirrored on the OARC wiki) for current behaviour.
    The companion file/BBS server **Xs32** is separate from XRouter and is still in use.

!!! tip "Which page do I need?"
    This is the Windows path. For a Raspberry Pi use [Raspberry Pi (XrPi)](raspberry-pi.md);
    for an x86 / x86-64 Linux PC use [Linux PC (XrLin)](linux-pc.md). The config file and
    almost everything else is identical across them.

## 1. What you need

- A Windows PC.
- A way onto the air: a hardware **KISS** TNC, a **NinoTNC**, or a soundcard modem such as
  **[Direwolf](../interfaces/soundcard-modems.md)**, **UZ7HO SoundModem** or
  **QtSoundModem** — or just an internet
  [AXUDP/AXIP link](../interfaces/axip-axudp-axtcp.md) to another node if you're starting
  without radio.
- Your callsign, and a spare SSID for the node (for example `-1`).

## 2. Download XrWin

Current XrWin builds are published in the **Files** area of the
[XRouter support group on groups.io](https://groups.io/g/xrouter) (you must join the group
to download), and are listed on the
[XRouter downloads page on the OARC wiki](https://wiki.oarc.uk/packet:xrouter:downloads).
As with every XRouter build, the working directory holds the program and `XROUTER.CFG`
together; there is no system-wide installation path.

!!! note "Older mirrors lag behind"
    Historic XRouter mirrors and the legacy `XR32.doc` describe earlier programs and
    versions. For a current Windows build, use the groups.io Files area or the OARC
    downloads page. See [Upgrading and versions](../admin/upgrading.md).

## 3. Configure it — same XROUTER.CFG as everywhere else

The whole of XRouter's configuration model is identical on Windows: the same
`XROUTER.CFG`, the same `KEYWORD=value` syntax, the same `INTERFACE`/`PORT` blocks, the same
ordering rule (interfaces before ports), and the same mandatory keywords. Rather than repeat
it here, work through **[Your first XROUTER.CFG](first-config.md)** — it applies directly,
with only the Windows points below to keep in mind.

!!! danger "Set IPADDRESS, or you lose *all* IP services"
    As on every platform, if the global `IPADDRESS` is left at `0.0.0.0` or undefined,
    XRouter **deliberately disables all IP activity** — AXUDP, AXTCP, AXIP, HTTP, FTP, the
    lot. If you don't have an AMPRNet (44Net) address, set a private dummy such as `10.1.1.1`
    so the IP-based features work. — *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

### Serial ports are COMn

Where a Linux config names a serial device as `/dev/ttyACM0` or `/dev/ttyUSB0`, on Windows
you use the familiar `COM` naming. So a KISS TNC interface that reads `COM=/dev/ttyACM0` on
Linux becomes, for example:

```ini
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=COM3          ; the COM port your TNC presents
        SPEED=57600
        MTU=256
ENDINTERFACE
```

Use Windows' Device Manager to find which `COMn` your TNC or USB-serial adaptor has been
assigned.

### Set RESPTIME=200 on Windows

!!! tip "Allow for Windows timing jitter"
    Windows does not schedule with the same fine timing granularity as Linux, so the
    per-port AX.25 delayed-acknowledgement timer should be relaxed a little. Set
    **`RESPTIME=200`** (milliseconds) on your ports rather than a very small value. This
    avoids spurious timing behaviour caused by the host's scheduling jitter. The directive
    is set per `PORT` block — see [Ports](../configuration/ports.md).

## 4. Run it

Start XrWin from its working directory. Like the other builds it presents an interactive
colour text interface with status screens, reads `XROUTER.CFG` once at start-up, and reports
configuration errors **with the line number** — so the edit-restart-check loop on
[Your first XROUTER.CFG](first-config.md) works exactly the same way here. Once it is up,
connect to the node prompt over Telnet to try it out and explore the
[node command prompt](../commands/node-prompt.md).

!!! note "Windows-specific details: ask the group"
    XRouter is one program with one feature set across platforms, but the specifics of
    installing and launching XrWin on a given Windows version, and any Windows-only quirks,
    are best confirmed against the people running it. The
    [XRouter support group on groups.io](https://groups.io/g/xrouter) is the place to ask,
    and its Files area carries the current build.

## 5. Keep it running

XrWin can be run permanently, including **as a Windows service** so the node starts with the
machine and keeps running in the background without an interactive logon. The general
principles of running XRouter unattended — daemon-style headless operation versus keeping an
attachable interactive interface, and surviving restarts — are covered on
**[Running as a service](running-as-a-service.md)**. For the exact mechanism of registering
XrWin as a Windows service on your version of Windows, ask on the groups.io group.

## Next steps

- Build your configuration: [Your first XROUTER.CFG](first-config.md) →
  [Global settings](../configuration/global-settings.md) →
  [Ports](../configuration/ports.md).
- Connect a radio: [KISS TNCs](../interfaces/kiss-tncs.md) or
  [soundcard modems](../interfaces/soundcard-modems.md).
- Link to the wider network: [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) and
  [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).

---

**Sources:** [XRouter downloads, OARC wiki](https://wiki.oarc.uk/packet:xrouter:downloads)
· [in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[XRouter support group, groups.io](https://groups.io/g/xrouter) (current Windows build and
Windows-specific support) · the `RESPTIME` and `COMn` notes reflect XRouter's documented
Windows behaviour.
