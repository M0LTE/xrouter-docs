# Getting started on Linux PC (XrLin)

This page stands up an XRouter node on an ordinary x86 / x86-64 Linux PC from nothing:
download the right build, unpack it, write a minimal config, and run it, including how to
run it **without root**, which is the part most newcomers trip over.

XRouter for the desktop is called **XrLin**. It is the *same program* as the Raspberry Pi
build ([XrPi](raspberry-pi.md)), the same source, simply compiled for the x86 / x86-64
processor family rather than ARM. (The Pi build adds GPIO support that a PC does not have;
nothing else differs.) It is a single self-contained binary, a few hundred kilobytes, with
no installer. If you have used a hardware TNC or a soundcard modem before, you can have a
node on the air in well under an hour.

!!! tip "Which page do I need?"
    This is the x86 / x86-64 Linux path. For a Raspberry Pi use
    [Raspberry Pi (XrPi)](raspberry-pi.md); for Windows use [Windows (XrWin)](windows.md).
    The config file and almost everything else is identical across them.

## 1. What you need

- A PC running a 64-bit or 32-bit Linux distribution. XRouter is undemanding, it has run on
  lightweight distros and needs well under a megabyte of disk for the program and its files.
- A way onto the air: a hardware **KISS** TNC, a **NinoTNC**, or a soundcard modem such as
  **[Direwolf](../interfaces/soundcard-modems.md)**, or just an internet
  [AXUDP/AXIP link](../interfaces/axip-axudp-axtcp.md) to another node if you're starting
  without radio.
- Your callsign, and a spare SSID for the node (for example `-1`).

## 2. Download the right build

Current XrLin binaries are published in the **Files** area of the
[XRouter support group on groups.io](https://groups.io/g/xrouter) (you must join the group
to download). Builds are named by their target and version, for example `xrlin502r`, with
the version baked into the filename.

A couple of choices to make:

- **32-bit vs 64-bit**, pick the build that matches your installation. A 64-bit Linux
  system normally wants the 64-bit XrLin build; use the 32-bit build on a 32-bit system (or
  on a 64-bit system that has 32-bit library support).
- **`-static` vs dynamically linked**, a **`-static`** build bundles what it needs and is
  the path of least resistance, because it avoids any mismatch with your distribution's
  system libraries. A dynamically linked build is smaller but depends on libraries being
  present on your system.

!!! note "If a dynamic build complains about a missing library, use the static one"
    Some dynamically linked builds expect a system library to be installed, for example an
    audio library such as `libasound2` for sound support. If a dynamic build fails to start
    with a missing-library error, the cleanest fix is to download and use a **`-static`**
    build instead, which sidesteps the dependency entirely. (The author is famously
    dependency-averse, which is exactly why static builds are offered.)

!!! note "Mirrors lag behind"
    You may find older XRouter documentation and binaries on `ohiopacket.org/xrpi` or
    `vk2dot.dyndns.org/xrpi`. These are historic mirrors, good for concepts, but **out of
    date on versions** (and many vk2dot links are now dead). Get current binaries from the
    groups.io Files area. See [Upgrading and versions](../admin/upgrading.md).

## 3. Unpack and make it runnable

Put the download in its own directory, XRouter reads and writes its files from wherever the
binary lives, then unzip, rename the binary to something convenient, and mark it
executable:

```bash
mkdir -p ~/xrlin && cd ~/xrlin
unzip ~/Downloads/xrlin502r.zip        # use the file you downloaded
mv xrlin502r xrlin                      # the descriptive name is just a label
chmod +x xrlin
```

!!! warning "Filenames are case-sensitive, and XRouter wants UPPER CASE"
    On Linux, `XROUTER.CFG` and `xrouter.cfg` are different files. XRouter expects its
    configuration and data files in **UPPER CASE** (`XROUTER.CFG`, `IPROUTE.SYS`,
    `PASSWORD.SYS`, …). Getting the case wrong is the single most common "it won't start"
    cause on a fresh install. (The executable itself is lower case, e.g. `xrlin`.)

## 4. Write a minimal XROUTER.CFG

XRouter will not start without an `XROUTER.CFG` containing at least a node callsign, a node
alias, one interface and one port. The full walk-through is on
**[Your first XROUTER.CFG](first-config.md)**; the shortest useful starting point looks like
this:

```ini
NODECALL=M0XXX-1
NODEALIAS=MYNODE
IPADDRESS=10.1.1.1        ; see the warning below, required for IP services

INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/ttyACM0   ; your TNC / NinoTNC device
        SPEED=57600
        FLOW=0
        MTU=256
ENDINTERFACE

PORT=1
        ID=Radio port
        INTERFACENUM=1
ENDPORT
```

!!! danger "Set IPADDRESS, or you lose *all* IP services"
    If the global `IPADDRESS` is `0.0.0.0` or left undefined, XRouter **deliberately
    disables all IP activity**, AXUDP, AXTCP, AXIP, HTTP, FTP, the lot, on both stacks.
    This is an intentional security feature. If you don't have an AMPRNet (44Net) address,
    set a private dummy such as `10.1.1.1` so the IP-based features work. (Recent builds
    refuse to start cleanly without it.), *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

## 5. First run

Start it from its own directory:

```bash
cd ~/xrlin
./xrlin
```

XRouter comes up as a colour text UI with several status screens. It reads `XROUTER.CFG`
once at startup and reports configuration errors **with the line number**, which makes
fixing them straightforward, change one thing, restart, repeat. If you see
`ERROR in line N`, open the config at that line.

Once it's running, connect to the node prompt locally over Telnet to try it out, then learn
your way around with the [node command prompt](../commands/node-prompt.md).

## 6. Run it without root (recommended)

XRouter needs to bind privileged ports (Telnet on 23, HTTP on 80) and, for some interfaces
such as `EXTERNAL`/Ethernet, to use raw sockets. The wrong way to satisfy that is to run the
whole thing as `root` or under `sudo`. The right way is to grant the binary just those two
Linux capabilities once:

```bash
sudo setcap cap_net_raw,cap_net_bind_service=ep ./xrlin
```

Now run it as your normal user with `./xrlin` and it can open low ports and raw sockets
without elevated privileges. You can confirm the capabilities were set with
`getcap ./xrlin`.

!!! danger "Don't run XRouter as root"
    Running a network-facing service as root is unnecessary here and best avoided, if a
    miscreant ever broke out to a shell, they would have only your user's directory tree,
    not the whole machine. Running under `sudo` is worse still, because the `sudo` session
    timeout can leave XRouter unable to respond or restart. Use `setcap` as above. You'll
    need to **re-apply `setcap` after replacing the binary with a new version**., see
    [Security and hardening](../admin/security-hardening.md).

## 7. Keep it running

For a permanent node you'll want XRouter to start at boot and survive crashes. It can run
fully headless as a daemon (`./xrlin -d`, controllable over HTTP/Telnet/packet) or inside a
`screen`/`tmux` session so you can re-attach to the TUI. Both approaches, and a sample
`systemd` service, are on **[Running as a service](running-as-a-service.md)**.

## Next steps

- Flesh out the config: [Your first XROUTER.CFG](first-config.md) →
  [Global settings](../configuration/global-settings.md) →
  [Ports](../configuration/ports.md).
- Connect a radio: [KISS TNCs](../interfaces/kiss-tncs.md) or
  [soundcard modems](../interfaces/soundcard-modems.md).
- Link to the wider network: [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) and
  [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).

---

**Sources:** [XRouter support group, groups.io](https://groups.io/g/xrouter) (downloads,
and the `IPADDRESS` and `setcap` guidance from the author) ·
[in-program manual, OARC wiki: running as root / capability flags (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6)
· [ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/install.htm) (XrLin shares
the XrPi install model)
