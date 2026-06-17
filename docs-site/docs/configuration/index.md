# Configuration

Almost everything XRouter does is steered by a single text file, **`XROUTER.CFG`**.
This page explains the shape of that file — the one mandatory config, its syntax, the
handful of block types, and the absolute minimum you need to make the program start. Read
it once and the [global settings](global-settings.md), [interfaces](interfaces.md) and
[ports](ports.md) pages will all make sense.

## One file to rule it

XRouter has exactly one mandatory configuration file: **`XROUTER.CFG`**. It lives in the
same directory as the binary, and the program reads it **once, at startup**. There is no
installer and no registry; configuring XRouter *is* editing this file.

A few consequences follow from "read once at startup":

- Changes to `XROUTER.CFG` take effect only when you restart XRouter (from the console, the
  sysop `RESTART` command, or by stopping and re-running the binary). Many individual
  parameters can also be changed live with sysop commands, but the file itself is not
  re-read on the fly.
- Errors are reported **with the line number**. If the program prints something like
  `ERROR in line 42`, open the file at that line — the parser stopped there. This makes the
  natural workflow "change one thing, restart, repeat", and it is why incremental edits are
  so much easier than big rewrites.

!!! warning "Filenames are UPPER CASE on Linux"
    On Linux (XrPi and XrLin), filenames are case-sensitive and XRouter expects its config
    and data files in **UPPER CASE**: `XROUTER.CFG`, not `xrouter.cfg`. A lower-case or
    mixed-case name is simply not found, and the program behaves as if the file is missing.
    See [System and data files](system-files.md).

## Syntax in one minute

`XROUTER.CFG` is plain ASCII text. The rules are few:

- Most directives are **`KEYWORD=value`**, one per line — for example `NODECALL=M0XXX-1`.
- **Keywords are not case-sensitive.** `NODECALL`, `nodecall` and `NodeCall` are the same
  directive. (Filenames given as *values* still obey the case rule above.)
- **Comments** start with `;` or `#` in the leftmost column. Blank lines are allowed and
  ignored. There is no inline-comment guarantee for every directive, so the safe habit is to
  put comments on their own line.
- **Lines must not exceed 255 characters.**

Throughout this site, config keywords appear in `code` (e.g. `MTU`, `PACLEN`) and complete
snippets in fenced blocks:

```ini
; This is a comment
NODECALL=M0XXX-1        ; node callsign with an SSID
NODEALIAS=MYNODE        ; up to 6 characters, no SSID
```

## The block structure

Everything in `XROUTER.CFG` is either **global** or inside a **block**. A block is a pair of
keywords that open and close a section; anything that is *not* inside a block belongs to the
GLOBAL section, where the node's identity and most network-wide defaults live.

| Block | Opens / closes | What it defines |
| --- | --- | --- |
| Interface | `INTERFACE=n` … `ENDINTERFACE` | A physical or virtual point of connection (a COM port, a soundcard link, an internet socket). See [Interfaces](interfaces.md). |
| Port | `PORT=n` … `ENDPORT` | An AX.25 port — usually one radio channel — bound to an interface. See [Ports](ports.md). |
| Radio | `RADIO=n` … `ENDRADIO` | A rig-control definition (frequency, PTT method, etc.). |
| Console | `CONSOLE=n` … `ENDCONSOLE` | Per-console (sysop window) settings such as colours and the default monitor mask. |
| Application | `APPL=n` … `ENDAPPL` | A hosted application — its name, callsign, alias and NET/ROM quality. |
| Routes | `ROUTES` … `***` | Locked-in NET/ROM routes that survive restarts. |

Everything outside those blocks is **GLOBAL**. Identity directives (`NODECALL`,
`NODEALIAS`), server port numbers, NET/ROM tuning and the like all sit in the global
section, typically near the top of the file.

!!! note "Interfaces must come before the ports that use them"
    Directives may otherwise appear in almost any order, but there is one rule the parser
    enforces: **an `INTERFACE` block must be defined before any `PORT` block that references
    it** with `INTERFACENUM`. Define your interfaces first, then your ports. This is the
    single most common ordering mistake in a hand-edited config.

## Free-text blocks end with `***`

A few directives introduce a *block of free text* rather than a single value, and these are
terminated not by an `END…` keyword but by a line containing only `***`:

- `CTEXT` — the connect text shown to users when they connect to the node.
- `INFOTEXT` — the text returned by the `INFO` (or `I`) command.
- `IDTEXT` — the identification beacon.
- `ROUTES` — the locked-in routes block (also `***`-terminated).

```ini
CTEXT
Welcome to MYNODE. Type ? for a list of commands.
73 de M0XXX
***
```

!!! tip "Don't forget the ***"
    Omitting the `***` terminator is a classic error: the parser keeps swallowing
    subsequent lines as part of the text block, so the directives below it silently vanish.
    If a keyword you *did* set seems to be ignored, check that the free-text block above it
    was closed with `***`. Keep `CTEXT` short — users see it on every connection — and put
    something genuinely useful in `INFOTEXT`.

## The minimum to start

XRouter refuses to start unless the configuration provides, at the very least:

1. `NODECALL` — the node's AX.25 / NET/ROM callsign (a callsign plus an SSID, e.g. `-1`).
2. `NODEALIAS` — the node's alias (up to 6 characters, no SSID).
3. **One `INTERFACE` block**, which itself must contain at least `TYPE` and `MTU`.
4. **One `PORT` block**, which itself must contain at least `ID` and `INTERFACENUM`.

A complete, minimal file therefore looks like this:

```ini
NODECALL=M0XXX-1
NODEALIAS=MYNODE
IPADDRESS=10.1.1.1        ; see the IPADDRESS warning on the Global settings page

INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/ttyACM0
        SPEED=57600
        MTU=256
ENDINTERFACE

PORT=1
        ID=Radio port
        INTERFACENUM=1
ENDPORT
```

The shipped "dummy" configuration uses a `LOOPBACK` interface and port to get a brand-new
sysop past the "won't start" hurdle quickly. A loopback has no practical on-air use; once you
have added a real interface and port, comment the loopback out. The full guided walk-through
is on [Your first XROUTER.CFG](../getting-started/first-config.md).

## Where to go next

- [Global settings](global-settings.md) — identity, server ports, NET/ROM and IP globals.
- [Interfaces](interfaces.md) — the `INTERFACE` block: `TYPE`, `PROTOCOL`, `MTU` and the hardware.
- [Ports](ports.md) — the `PORT` block: AX.25 timing, NET/ROM quality and behaviour flags.
- [Worked example configs](examples.md) — complete, annotated configurations to adapt.
- [Directive reference](directive-reference.md) — every keyword, grouped by block.
- [System and data files](system-files.md) — `XROUTER.CFG` and the optional `*.SYS` / `*.CFG` files.

---

**Sources:**
[XROUTER.CFG, OARC wiki (man8)](https://wiki.oarc.uk/packet:xrouter:docs:man8) ·
[Installation and configuration topics, OARC wiki (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[ohiopacket.org XRPi mirror — configuration](https://ohiopacket.org/xrpi/docs/xrcfg.htm) ·
[XRouter support group, groups.io](https://groups.io/g/xrouter)
