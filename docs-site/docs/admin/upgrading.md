# Upgrading and versions

XRouter releases briskly, and keeping reasonably current is itself a form of maintenance,
many of the "it keeps crashing" and "the link won't stay up" reports in the support group turn
out to be old builds. This page explains the version scheme, where to get current binaries,
how to upgrade a node safely without losing your configuration or your NET/ROM routes, and
what to expect from XRouter's release style.

## The version scheme

XRouter version numbers run in series. The modern Linux/Windows builds are numbered in the
**500s**; the current series at the time of writing is **~v504** (for example v504v, January
2026). The table below gives selected highlights, see the release notes for specifics:

| Series | Era | Selected highlights |
| --- | --- | --- |
| **501** | 2019 | v501c was the last full release of the 2019 era |
| **502** | ~2023 | Per the V502 release notes: a SYN cache, an FTP client and NFTP, packet SMS, IP packet capture (Wireshark/pcap) and expanded IDS/security features |
| **503** | ~2024 | Tightened AXIP/AXUDP error-checking |
| **504** | current | The current series, for example **v504v** (January 2026) |

A point letter (the `v` in `v504v`) marks each incremental build within a series. Don't
over-pin to one exact letter: the current build moves often, and the right answer to "which
version?" is usually "the newest one in the Files area".

!!! note "One codebase, several builds"
    XrPi (Raspberry Pi/ARM) and XrLin (x86 Linux) are the *same source* compiled for different
    processors; XrWin is the current Windows build. XR32 (Windows) and the original DOS
    XRouter are legacy and no longer tracked here. A given version letter is released across
    the current builds together. See the [home page](../index.md) for the full family.

## Where to get current builds

!!! tip "Get binaries from the groups.io Files area"
    The most up-to-date and authentic source is the **Files** area of the
    [XRouter support group on groups.io](https://groups.io/g/xrouter/files) (you must join the
    group to download). The [OARC wiki downloads page](https://wiki.oarc.uk/packet:xrouter:downloads)
    mirrors recent builds and the changelog.

Older mirrors lag, sometimes by years:

!!! warning "ohiopacket and vk2dot are stale"
    The historic `ohiopacket.org/xrpi` mirror (and the original `vk2dot.dyndns.org/xrpi`) are
    excellent for *concepts* but **out of date on versions**, ohiopacket still shows the
    v501c era, and many vk2dot links are now dead. Do not judge "the current version" by them;
    go to the groups.io Files area for binaries.

Builds are named by target and flavour, for example a `-static` variant (self-contained, runs
on any Linux, but you must set `DNS=` in `XROUTER.CFG` because it has no system resolver) or a
distribution-matched build such as `-stretch`/`-bullseye`. The
[Raspberry Pi getting-started page](../getting-started/raspberry-pi.md#2-download-the-right-build)
covers choosing between them.

## How to upgrade safely

Upgrading is essentially "swap the binary", XRouter never overwrites your configuration, but
do it in a controlled order so you can fall back if a new build misbehaves.

1. **Read the release notes first.** Each release ships notes describing what changed, and
   crucially any **new configuration files or directives** you may want to add. Skim them
   before upgrading, and if you are jumping several versions, check the intermediate ones too.
2. **Stop the node cleanly.** Shut XRouter down (or stop the service) so it is not running
   while you replace its executable. A clean shutdown also saves the NET/ROM nodes table.
3. **Keep your old binary and your config.** Rename the existing executable (for example
   `xrpi` → `xrpi504n`) rather than deleting it, so you can roll back. Your `XROUTER.CFG` and
   `.SYS`/`.CFG` files stay exactly as they are, leave them in place.
4. **Drop in the new binary.** Put the new download in the working directory and rename it to
   the name your service/start command expects (e.g. `xrpi`). Mark it executable with
   `chmod +x`.
5. **Re-apply `setcap`.** Capabilities do not survive replacing the file, so if you run
   unprivileged (you should, see [Security and hardening](security-hardening.md#run-as-a-non-root-user))
   re-run `sudo setcap cap_net_raw,cap_net_bind_service=ep ./xrpi`.
6. **Restart and watch the boot.** Start the node and check the boot log and status screen for
   errors. If something is wrong, stop, swap your renamed old binary back, and you are
   running again while you investigate.

!!! warning "RESTART refuses a non-executable new binary"
    If you replace the binary on a *running* node and intend to use the `RESTART` command, you
    must `chmod +x` the new file first (and re-apply `setcap` if unprivileged), or the command
    is refused. The stop/replace/start order above sidesteps this. See
    [Running as a service](../getting-started/running-as-a-service.md).

### Your NET/ROM routes survive: usually

XRouter persists the NET/ROM nodes table to the **`XRNODES`** file and reloads it at startup,
so an upgrade or routine restart does **not** cost you the routing table (which can take hours
to rebuild from broadcasts). You normally want to keep `XRNODES` across an upgrade.

The one exception is a *long outage*: if the node has been down for hours, the saved table is
stale and re-introduces nodes that have since expired. In that case delete `XRNODES` before
booting so the node rebuilds a fresh table. For a quick version swap, leave it alone. The file
and this caveat are detailed in [System and data files](../configuration/system-files.md).

## Releases are beta by nature

XRouter is developed and released informally. Builds are frequent, distributed through the
group rather than packaged repositories, and explicitly offered to a community that is, in
effect, beta-testing them, the author asks users to report bugs and ships fixes quickly.

That has two practical consequences. First, **reporting problems is part of the deal**: if a
new build breaks something, say so in the [support group](https://groups.io/g/xrouter), ideally
with a [gdb backtrace](logging-and-monitoring.md#capturing-a-crash-backtrace) for a crash.
Second, **keep your fallback**: because any given build is a development release, the renamed
previous binary from step 3 is your safety net, keep it until the new one has proven itself on
your node.

---

**Sources:** [XRouter support group, groups.io](https://groups.io/g/xrouter) (current builds in
the Files area; the version-progression and stability history) ·
[OARC wiki downloads](https://wiki.oarc.uk/packet:xrouter:downloads) (build flavours and the
v504 series, e.g. v504v, Jan 2026) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/install.htm) (the upgrade
procedure, re-applying `setcap`, and the `RESTART`-needs-`chmod +x` note, mirror stale on
versions) · release notes (the per-series feature highlights and the `XRNODES` persistence
behaviour)
