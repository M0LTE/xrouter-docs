# Your first XROUTER.CFG

This page walks through a minimal, working `XROUTER.CFG` line by line, enough to get
XRouter to start, identify itself on the air and accept a connection, and then shows how
to verify it and grow it safely. It is for anyone who has just unpacked a build (see
[Raspberry Pi](raspberry-pi.md), [Linux PC](linux-pc.md) or [Windows](windows.md)) and is
staring at an empty config wondering what the bare minimum is.

`XROUTER.CFG` is the one file XRouter cannot run without. Nearly all of the node's
configuration lives here, and getting the first dozen lines right is most of the battle.
The good news is that the bare minimum is genuinely short.

## Where the file lives

XRouter reads its files from **the directory the binary lives in**, there is no
system-wide config path, no `/etc` entry, nothing hidden. Put `XROUTER.CFG` next to the
executable and that is where it will be found.

!!! warning "The name is UPPER CASE, and on Linux that matters"
    The file must be named `XROUTER.CFG` in **upper case**. On Linux and the Raspberry Pi
    the filesystem is case-sensitive, so `xrouter.cfg` is a *different file* and XRouter
    will behave as though it has no config at all. Every XRouter configuration and data
    file uses upper case (`XROUTER.CFG`, `IPROUTE.SYS`, `PASSWORD.SYS`, …). This is the
    single most common "it won't start" mistake on a fresh install. On Windows the
    filesystem is not case-sensitive, but using upper case keeps your config portable.

The file is **read once, at start-up**. XRouter parses it top to bottom, and if it finds a
problem it reports the **line number** of the offending line. Many, though not all,
parameters can also be changed live from the [node command prompt](../commands/node-prompt.md)
once the program is running, but the file itself is not re-read until the next restart.

## Syntax in a nutshell

`XROUTER.CFG` is plain ASCII text. The rules are few:

- Each directive is written **`KEYWORD=value`**, one per line.
- **Keywords are not case-sensitive**, `NODECALL`, `nodecall` and `NodeCall` are the same
  directive. (Filenames given as *values* still obey the filesystem's own case rules.)
- A line beginning with **`;`** or **`#`** in the leftmost column is a comment. Blank lines
  are allowed and ignored.
- A line may be at most **255 characters** long.

That is the whole grammar. Everything else is a matter of which keywords you use and which
*block* you put them in.

### Blocks, and the one ordering rule

Most of the file is the **GLOBAL** section: anything that is not inside a block. Your node
callsign, alias and most defaults live here. A few things are grouped into paired blocks:

| Block | Purpose |
| --- | --- |
| `INTERFACE=n` … `ENDINTERFACE` | A hardware or virtual connection (a TNC, a soundcard modem, an internet link). |
| `PORT=n` … `ENDPORT` | A logical port, usually one radio frequency, attached to an interface. |
| `RADIO=n` … `ENDRADIO` | A block for radio / PTT control settings. |
| `CONSOLE=n` … `ENDCONSOLE` | Per-console (sysop window) settings. |
| `APPL=n` … `ENDAPPL` | A hosted application (e.g. a separate BBS). |
| `ROUTES` … `***` | Locked-in NET/ROM routes. |

Blocks may otherwise appear in almost any order, with one exception that trips up nearly
everyone:

!!! warning "Define interfaces *before* the ports that use them"
    A `PORT` attaches to an `INTERFACE` by number (`INTERFACENUM=`). XRouter reads the file
    once, top to bottom, so **the `INTERFACE` block must appear earlier in the file than any
    `PORT` that references it.** Put your interfaces first and your ports after them and you
    will not hit this.

## What the bare minimum contains

XRouter will refuse to start unless the config supplies, at the very least:

- a node callsign (`NODECALL`) and node alias (`NODEALIAS`);
- at least one `INTERFACE` block, which itself must contain at least a `TYPE` and an `MTU`
  (most interface types also need a `PROTOCOL`); and
- at least one `PORT` block, which must contain an `ID` and an `INTERFACENUM`.

Everything else is optional and can be added later. Let's build a clean example that meets
all of that and adds a couple of sensible extras.

## A worked minimal config

The example below brings up a single radio port through a KISS TNC (or NinoTNC). Swap in
your own callsign and serial device. The inline comments explain each line.

```ini
; ------------------------------------------------------------------
;  XROUTER.CFG  -  minimal single-port example
; ------------------------------------------------------------------

; --- Node identity (GLOBAL) ---------------------------------------
NODECALL=M0XXX-1          ; this node's AX.25 / NET-ROM callsign (+SSID)
NODEALIAS=MYNODE          ; this node's alias, up to 6 characters, no SSID
CONSOLECALL=M0XXX         ; callsign used for connections you make from the console
QTH=Anytown IO00AA        ; free-text location, advertised to users
LOCATOR=IO00AA            ; Maidenhead locator (optional)

IPADDRESS=10.1.1.1        ; see the warning below, needed for ANY IP service

; --- Connect / info text ------------------------------------------
CTEXT
Welcome to MYNODE. Type ? for a list of commands.
***

INFOTEXT
MYNODE, an XRouter packet node at Anytown.
Sysop: M0XXX.  Running XRouter.
***

; --- One interface: a KISS TNC on a serial port -------------------
INTERFACE=1
        TYPE=ASYNC        ; serial / USB-serial device
        PROTOCOL=KISS     ; talk KISS to the TNC
        COM=/dev/ttyACM0  ; your TNC / NinoTNC device (COMn on Windows)
        SPEED=57600       ; serial speed to the TNC (not the RF speed)
        FLOW=0            ; no flow control (normal for KISS)
        MTU=256           ; 256 is the safe value for AX.25
ENDINTERFACE

; --- One port: the radio frequency on that interface --------------
PORT=1
        ID=2m 144.950 1k2  ; free-text label shown on the PORTS display
        INTERFACENUM=1     ; attach this port to INTERFACE 1
ENDPORT
```

That is a complete, startable configuration. Below, the parts worth understanding.

### Identity

`NODECALL` is your node's main AX.25 / NET-ROM callsign and **must** include an SSID
(for example `-1`) to distinguish the node from your personal station. `NODEALIAS` is a
short text alias of up to six characters with no SSID, it is what neighbours and users see
alongside your callsign in nodes lists.

`CONSOLECALL` is optional. By default the console uses `NODECALL`; set `CONSOLECALL` if you
want connections *you* originate from the local console to go out under your personal
callsign rather than the node's. `QTH` (a free-text location, up to 32 characters) and
`LOCATOR` (a Maidenhead grid square) are both optional but worth setting, they tell users
and mapping services where you are.

### The IPADDRESS gotcha

```ini
IPADDRESS=10.1.1.1
```

This one line saves a great deal of confusion:

!!! danger "No `IPADDRESS` means *no* IP services at all"
    If the global `IPADDRESS` is left at its default of `0.0.0.0` (or undefined), XRouter
    **deliberately disables all IP activity**, AXUDP, AXTCP, AXIP, the HTTP server, FTP,
    the lot, on *both* its own stack and the Linux stack. This is an intentional security
    feature, not a bug. If you have an AMPRNet (44Net) address, put it here. If you do not,
    set a private dummy address such as `10.1.1.1` so that the IP-based features work.
    Recent builds will not start cleanly with `IPADDRESS` omitted., *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

### Free-text blocks: CTEXT and INFOTEXT

`CTEXT` is the connect text shown to users when they connect to the node; `INFOTEXT` is what
the `INFO` (or `I`) command returns. Both are **multi-line blocks terminated by a line
containing `***`**:

```ini
CTEXT
Welcome to MYNODE. Type ? for a list of commands.
***
```

!!! tip "Don't forget the `***` terminator"
    Forgetting the `***` that closes a `CTEXT`, `INFOTEXT`, `IDTEXT` or `ROUTES` block is a
    classic error, without it, XRouter keeps reading following lines as part of the text.
    Keep `CTEXT` short, because users see it on *every* connection, and put something
    genuinely useful in `INFOTEXT` so the `I` command teaches a visitor something.

`IDTEXT` (an over-the-air ID beacon) works the same way but is optional, and is best left
until you have the node on the air and working.

### One interface and one port

The `INTERFACE` block describes *how* XRouter reaches the outside world; the `PORT` block
describes a logical channel on it. The example uses `TYPE=ASYNC` with `PROTOCOL=KISS` to
drive a serial KISS TNC, see [Interfaces](../configuration/interfaces.md) and
[KISS TNCs](../interfaces/kiss-tncs.md) for the other interface types (soundcard modems,
AGWPE, AXIP/AXUDP internet links, and so on). The `PORT` block needs only an `ID` (a label)
and `INTERFACENUM` (which interface it belongs to); see [Ports](../configuration/ports.md)
for per-port timing and behaviour keywords.

If you have no radio hardware yet and just want to confirm XRouter starts, the shipped
"dummy" configs use a `LOOPBACK` interface and port instead. That is fine for a first run,
but a loopback has no practical use beyond testing, so replace it with a real interface
before you go on the air.

## Verify, then iterate

Start XRouter from its own directory and watch the start-up output. Because the file is
parsed once and errors are reported **with their line number**, fixing problems is a tight
loop:

1. Start the node (for example `./xrlin` on Linux, or run the program on Windows).
2. If you see `ERROR in line N`, open `XROUTER.CFG` at that line and fix the one thing.
3. Restart and repeat.

A boot log is also written under `LOG/` (for example `BOOTLOG.TXT`), which is useful if the
program exits before you can read the screen. Once it comes up cleanly, connect to the node
locally over Telnet to confirm your `CTEXT` and try the `PORTS`, `NODES` and `INFO`
commands. From there, build the config up **one change at a time**, add a second port, a
mailbox callsign, an APRS IGate, restarting and checking after each. Making many changes
at once and then trying to work out which one broke start-up is the slow way round.

## Next steps

- The full set of GLOBAL directives: [Global settings](../configuration/global-settings.md).
- Define more connections: [Interfaces](../configuration/interfaces.md) and
  [Ports](../configuration/ports.md).
- Keep the node running unattended: [Running as a service](running-as-a-service.md).

---

**Sources:** [In-program manual, OARC wiki: XROUTER.CFG (man8)](https://wiki.oarc.uk/packet:xrouter:docs:man8)
· [configuration directives (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[installation and configuration topics (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6)
· [XRouter support group, groups.io](https://groups.io/g/xrouter) (the `IPADDRESS`
behaviour, from the author) · [ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/config01.htm)
(conceptual, stale on versions)
