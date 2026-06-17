# Getting started on Raspberry Pi (XrPi)

This page stands up an XRouter node on a Raspberry Pi from nothing: download the right
build, unpack it, write a minimal config, and run it, including how to run it **without
root**, which is the part most newcomers trip over.

XRouter for the Pi is called **XrPi**. It is a single binary with no installer and no
dependencies to speak of: a few hundred kilobytes that will run on any Raspberry Pi. If
you have used a hardware TNC or a soundcard modem before, you can have a node on the air in
well under an hour.

!!! tip "Which page do I need?"
    This is the Raspberry Pi path. For an x86 PC use [Linux PC (XrLin)](linux-pc.md);
    for Windows use [Windows (XrWin)](windows.md). The config file and almost everything
    else is identical across them.

## 1. What you need

- A Raspberry Pi running Raspberry Pi OS (any model; older Pis are fine).
- A way onto the air: a hardware **KISS** TNC, a **NinoTNC**, or a soundcard modem such as
  **[Direwolf](../interfaces/soundcard-modems.md)**, or just an internet
  [AXUDP/AXIP link](../interfaces/axip-axudp-axtcp.md) to another node if you're starting
  without radio.
- Your callsign, and a spare SSID for the node (for example `-1`).

## 2. Download the right build

Current XrPi binaries are published in the **Files** area of the
[XRouter support group on groups.io](https://groups.io/g/xrouter) (you must join the group to
download). Builds are named by their target, for example `xrpi32v504j-stretch` or a
`-static` variant.

- **32-bit vs 64-bit**, most Pi installs use the 32-bit build, which also runs on a 64-bit
  OS that has 32-bit support. A native 64-bit build is available too.
- **`-static` builds** avoid mismatches with your system's C library; reach for one if a
  dynamically linked build complains about libraries on startup.

!!! note "Mirrors lag behind"
    You may find XRPi on `ohiopacket.org/xrpi` or `vk2dot.dyndns.org/xrpi`. These are
    historic mirrors, good for concepts, but **out of date on versions** (and many vk2dot
    links are now dead). Get current binaries from the groups.io Files area. See
    [Upgrading and versions](../admin/upgrading.md).

## 3. Unpack and make it runnable

Put the download in its own directory, XRouter reads and writes its files from wherever
the binary lives, then unzip, rename the binary to something convenient, and mark it
executable:

```bash
mkdir -p ~/xrpi && cd ~/xrpi
unzip ~/Downloads/xrpi32v504j-stretch.zip      # use the file you downloaded
mv xrpi32v504j-stretch xrpi                     # the descriptive name is just a label
chmod +x xrpi
```

!!! warning "Filenames are case-sensitive, and XRouter wants UPPER CASE"
    On Linux, `XROUTER.CFG` and `xrouter.cfg` are different files. XRouter expects its
    configuration and data files in **UPPER CASE** (`XROUTER.CFG`, `IPROUTE.SYS`,
    `PASSWORD.SYS`, …). Getting the case wrong is the single most common "it won't start"
    cause on a fresh install.

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
cd ~/xrpi
./xrpi
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
sudo setcap cap_net_raw,cap_net_bind_service=ep ./xrpi
```

Now run it as your normal user with `./xrpi` and it can open low ports and raw sockets
without elevated privileges.

!!! tip "Don't run XRouter as root"
    Running a network-facing service as root is unnecessary here and best avoided. Use
    `setcap` as above. You'll need to re-apply `setcap` after replacing the binary with a
    new version. The author and the community both recommend the capability approach over
    `sudo`., see [Security and hardening](../admin/security-hardening.md).

## 7. Keep it running

For a permanent node you'll want XRouter to start at boot and survive crashes. It can run
fully headless as a daemon (`./xrpi -d`, controllable over HTTP/Telnet/packet) or inside a
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
[in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/install.htm)
