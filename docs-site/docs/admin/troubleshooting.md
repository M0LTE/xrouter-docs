# Troubleshooting and FAQ

Most XRouter problems are not subtle once you know the cause, and a handful of them account for
the great majority of "it won't start" and "it won't work" reports. This page is a question-
and-answer guide to those real-world issues, each with the underlying cause and the fix, drawn
from the support group, the community, and the manual. If you are setting a node up for the
first time, reading it once will save you an evening.

!!! tip "Read configuration errors literally"
    XRouter reads `XROUTER.CFG` once at startup and reports configuration errors **with the
    line number** (`ERROR in line N`). That makes fixing them a one-at-a-time job: open the
    config at that line, fix it, restart, repeat. Make incremental changes rather than rewriting
    half the file at once. The boot log (`LOG/BOOTLOG.TXT`) records startup and is the first
    place to look if the node quits immediately.

## Starting up

### IP services are dead: HTTP, FTP, AXUDP "won't start" {#ip-services-wont-start}

**Symptom.** None of the IP-based services work. The web server, FTP, and AXUDP/AXTCP/AXIP
internet links are all silent or refuse to start, even though they look configured.

**Cause.** The global `IPADDRESS` is unset or `0.0.0.0`. XRouter then **deliberately disables
all IP activity**, on both its own stack and the Linux stack. This is an intentional security
feature, not a bug: a node with no IP address exposes no IP attack surface.

!!! danger "Set IPADDRESS, or you lose every IP service"
    > "If you set the global IPADDRESS to 0.0.0.0 or leave it undefined, *all* IP activity is
    > disabled, including AXUDP, AXTCP, AXIP, HTTP, FTP etc on BOTH stacks! This is a deliberate
    > security feature. … if you don't have an amprnet address, and you want to use any IP
    > services, it is suggested that you set a non-zero dummy IP address such as `10.1.1.1`.",
    > *Paula G8PZT, [groups.io](https://groups.io/g/xrouter/message/1957)*

**Fix.** Give the node an address. If you have an AMPRNet (44Net) allocation, use it; otherwise
set a private dummy:

```ini
IPADDRESS=10.1.1.1
```

Recent builds tightened the AXIP/AXUDP checks and will "definitely not work if IPADDRESS is
omitted", so this is effectively mandatory the moment you want any IP feature. The background
is on [Security and hardening](security-hardening.md#understand-the-ipaddress-security-behaviour).

### "ERROR … opening … port 23" or Error 13 {#error-13-privileged-port}

**Symptom.** On startup XRouter reports it cannot open the Telnet server port 23 (or HTTP on
80), often as `Error 13`.

**Cause.** Ports below 1024 are privileged on Linux. A program running as an ordinary user
cannot bind them without the right capability, and `Error 13` is "permission denied".

**Fix.** Do **not** reach for `sudo`/root. Grant the binary the two capabilities it needs, once:

```bash
sudo setcap cap_net_raw,cap_net_bind_service=ep ./xrpi
```

Then run as your normal user. `cap_net_bind_service` lets it bind the low ports;
`cap_net_raw` covers raw sockets for `EXTERNAL`/Ethernet and AXIP. Re-apply `setcap` whenever
you replace the binary. The full rationale, and why root is the wrong fix, is on
[Security and hardening](security-hardening.md#run-as-a-non-root-user) and the
[Raspberry Pi page](../getting-started/raspberry-pi.md#6-run-it-without-root-recommended).

### The config won't load: "Bad or missing protocol" {#bad-or-missing-protocol}

**Symptom.** XRouter refuses to start and complains about a bad or missing protocol on an
interface, or stops at a numbered line in an `INTERFACE` block.

**Cause.** Most interface types need a `PROTOCOL=` directive (a KISS TNC needs
`PROTOCOL=KISS`, an Ethernet interface `PROTOCOL=ETHER`, and so on), and every interface needs
`TYPE` and `MTU`. Leave the protocol out and you get `ERROR … Bad or missing protocol`.

**Fix.** Add the missing directive. A minimal serial KISS interface, for instance:

```ini
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS      ; the line that's usually missing
        COM=/dev/ttyACM0
        SPEED=57600
        MTU=256
ENDINTERFACE
```

Because errors are line-numbered, fix the one it names, restart, and see if it gets further.
Two related classics: **interfaces must be defined before the ports that reference them**, and
multi-line text blocks (`CTEXT`, `INFOTEXT`, `IDTEXT`, `ROUTES`) must be closed with their
terminator, forgetting it is a common parse error. See
[Interfaces](../configuration/interfaces.md) and [Ports](../configuration/ports.md).

### Files not found, or odd behaviour, on Linux {#case-sensitive-filenames}

**Symptom.** A configuration or data file you definitely created is ignored; a feature that
needs a `.SYS` file behaves as if the file is absent.

**Cause.** Linux filesystems are case-sensitive, and XRouter looks for its files in **UPPER
CASE**. `IPROUTE.SYS` and `iproute.sys` are different files, and XRouter only reads the former.

!!! warning "XRouter wants UPPER CASE filenames"
    On Linux, name every configuration and data file in upper case, `XROUTER.CFG`,
    `PASSWORD.SYS`, `IPROUTE.SYS`, `XRNODES`, and the rest. The executable is the exception: on
    the Pi build it is the lowercase `xrpi`. Getting the case wrong is the single most common
    reason a file is silently ignored.

**Fix.** Rename the file to upper case. The full list of file names is on
[System and data files](../configuration/system-files.md).

## Links and connectivity

### A Direwolf link keeps reconnecting or dropping {#direwolf-reconnects}

**Symptom.** A KISS-over-TCP link to Direwolf repeatedly reconnects or drops out, never settling.

**Cause.** This was a fault in **very old** XrPi builds, from before Direwolf was widely used.

> "I don't think Direwolf was much of a thing when that version of xrpi was written.",
> *Paula G8PZT*

**Fix.** Update to a current build; the behaviour was fixed in later versions. See
[Upgrading and versions](upgrading.md) and [Soundcard modems](../interfaces/soundcard-modems.md).

### Can't reach Telnet or HTTP from the LAN {#cant-reach-from-lan}

**Symptom.** The node is up, but a browser or Telnet client on another machine on your LAN
cannot reach its web or Telnet server.

**Cause.** Usually one of: the service is not actually listening (it was disabled, moved to a
non-default port, or IP is off because `IPADDRESS` is unset, see
[above](#ip-services-wont-start)); the node is answering on its *own* IP stack rather than the
Linux one; or a firewall between you and the node is blocking the port.

**Fix.** Work through it in order:

- Confirm the service is enabled and note its port, `TELNETPORT` (default 23), `HTTPPORT`
  (default 80). A `*PORT` of `0` disables it; two values set the XRouter-stack and Linux-stack
  ports separately, so a service can be reachable on one stack and not the other.
- Confirm `IPADDRESS` is set, or no IP service runs at all.
- Check there is no host or network firewall dropping the port between client and node.

See [Built-in TCP servers](../subsystems/built-in-servers.md) and
[The TCP/IP stack](../networking/tcpip.md).

### AXIP / AXUDP socket errors {#axip-axudp-socket-errors}

**Symptom.** Startup or runtime errors such as "Can't open AXIP socket", or an AXUDP/AXIP link
that never passes traffic.

**Cause.** AXIP and AXUDP ride on the IP stack, so they fall over for the same reasons any IP
feature does, most often `IPADDRESS` being unset (which disables them entirely), but also a
wrong local/remote UDP port, or a firewall/NAT in the path. (AXIP uses IP protocol 93; AXUDP
defaults to UDP port 93, don't confuse the two.) Recent builds added stricter checks here and
will not start with `IPADDRESS` omitted.

**Fix.**

- Set `IPADDRESS` (see [above](#ip-services-wont-start)).
- Check the per-port `UDPLOCAL`/`UDPREMOTE` (AXUDP) and `IPLINK` peer values.
- For an internet link, make sure your firewall/router forwards the relevant protocol/port to
  the node, and remember **only one AXIP node may share a given public IP** (AXUDP can share one).

The interface details are on [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md).

## Stability and tuning

### Random freezes or segfaults {#freezes-segfaults}

**Symptom.** The node freezes or crashes (segfaults) at random.

**Cause.** Builds **before 501b** had known random-freeze and segfault problems, which were
fixed. A current build should be stable; a crash on a current build is worth reporting.

**Fix.** First, **update**, see [Upgrading and versions](upgrading.md). If a current build
still crashes, capture a backtrace under a debugger and send it to the author:

```bash
gdb --ex run ./xrpi
```

After it crashes, run `bt full` in gdb and send the output (with your version and what you were
doing) to the [support group](https://groups.io/g/xrouter). The full procedure is on
[Logging and monitoring](logging-and-monitoring.md#capturing-a-crash-backtrace).

### MTU and PACLEN confusion {#mtu-paclen}

**Symptom.** Uncertainty about what to set `MTU` (per interface) and `PACLEN` (per port) to,
or links that behave badly with large values.

**Cause.** AX.25 and the TNCs behind it do not like oversized frames; values above 256 are not
recommended on AX.25 ports, and TNC buffers can struggle.

!!! tip "If in doubt, use 256"
    The manual's own guidance for `MTU` on AX.25 ports is simply: *"If in doubt, use 256."* A
    `PACLEN` around 256 (or a little less, e.g. 160–240) is a safe default for ordinary RF
    ports. Only raise these on fast or wired links where you know the path can carry it.

**Fix.** Set `MTU=256` on AX.25 interfaces and a sensible `PACLEN` per port; XRouter can also
adapt `PACLEN` to channel conditions. See [Interfaces](../configuration/interfaces.md) and
[Ports](../configuration/ports.md).

### A build ships with DNS=8.8.8.8 {#dns-default}

**Symptom.** Name resolution behaves oddly, or you notice the shipped `XROUTER.CFG` contains a
hard-coded public DNS server.

**Cause.** Some builds ship with `DNS=8.8.8.8` set in the example config. That is fine on many
networks but can cause trouble on others (no route to it, a local resolver expected, a
captive/filtered network).

**Fix.** If it causes problems, comment it out:

```ini
; DNS=8.8.8.8
```

XRouter has an internal resolver and will use `DOMAIN.SYS` and its defaults. Note the opposite
case: a `-static` build has no system resolver, so there you may *need* to set a working
`DNS=`. See [System and data files](../configuration/system-files.md) and
[Upgrading and versions](upgrading.md).

## Frequently asked

### Should I run XRouter as root?

No. Run it as an ordinary user with `cap_net_raw,cap_net_bind_service` granted via `setcap`
(see [Error 13](#error-13-privileged-port)). Root, and especially `sudo`, whose session
time-limit can make a long-running node stop responding, is best avoided. Full reasoning on
[Security and hardening](security-hardening.md#run-as-a-non-root-user).

### How do I restart or update without losing my NET/ROM routes?

You don't lose them: XRouter saves the nodes table to `XRNODES` and reloads it at boot. Keep
that file across a restart or upgrade. The only time to delete it is after a *long* outage,
when the saved data is stale. See [Upgrading and versions](upgrading.md#your-netrom-routes-survive-usually).

### Where do I get the current version, and how do I know it's current?

From the **Files** area of the [groups.io support group](https://groups.io/g/xrouter/files).
Do not gauge "current" from the `ohiopacket.org` or `vk2dot` mirrors, they are stale. See
[Upgrading and versions](upgrading.md#where-to-get-current-builds).

### My problem isn't here: where do I ask?

The [XRouter support group on groups.io](https://groups.io/g/xrouter) is the primary venue, and
the author answers there. Include your version string, the relevant slice of `XROUTER.CFG`
(minus passwords), the exact error text or boot-log line, and a [gdb
backtrace](logging-and-monitoring.md#capturing-a-crash-backtrace) for a crash. The maintained
reference is the [in-program manual on the OARC wiki](https://wiki.oarc.uk/packet:xrouter).

---

**Sources:** [XRouter support group, groups.io](https://groups.io/g/xrouter), the `IPADDRESS`
behaviour ([message/1957](https://groups.io/g/xrouter/message/1957)), the `Error 13`/`setcap`
fix ([message/1958](https://groups.io/g/xrouter/message/1958)), and the pre-501b stability,
Direwolf, MTU and DNS guidance (author/community) ·
[in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs:man6) (interface/port
rules, case-sensitive filenames, the "if in doubt use 256" MTU note) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/install.htm) (case sensitivity,
incremental editing, root vs non-root)
