# Security and hardening

A packet node is a network-facing service, often unattended on a hilltop, and frequently
reachable from both radio and the internet. This page collects the generic, forward-looking
hardening measures every XRouter sysop should consider: a strong sysop password, careful use
of the trust controls, running without root, restricting who can reach the node, and turning
off services you do not use. None of it is exotic — it is ordinary good practice applied to a
node — and most of it is a few lines of configuration.

!!! note "Scope of this page"
    This is generic best practice, not an audit of any particular node. It assumes a working
    node from one of the [Getting started](../getting-started/index.md) pages. Where a control
    lives in a system file, the [System and data files](../configuration/system-files.md)
    reference gives the exact format.

## Set a strong sysop password

Sysop status gives full control of the node — every command, the OS shell, file maintenance,
the lot. Protect it.

XRouter keeps sysop passwords in **`PASSWORD.SYS`**, which governs remote sysop access via the
AX.25 `@` challenge, RLOGIN and FTP. Each line is `<callsign> <password>` (no SSID, no spaces,
not case-sensitive). If the file is absent, none of those remote-sysop routes work at all —
which is itself a safe default.

- Use a long, unpredictable passphrase, not a word anyone could guess from your callsign or QTH.
- Treat `PASSWORD.SYS` as a secret: it is plain text, so keep its file permissions tight and
  do not leave copies in a web-served directory.
- The separate **`USERPASS.SYS`** holds *ordinary user* passwords (Telnet, the Telnet proxy,
  the APRS server). It is **not** a route to sysop privilege — keep the two files distinct in
  your mind.

The AX.25 remote-sysop challenge (`@`) deliberately never sends the password over the air in
clear: you are shown a numbered matrix and reply with only selected characters of the
password. That protects the password from a listener, but it does not protect against someone
who already knows it, so password strength still matters.

## Beware SYSOP=1 on a port

A port may be marked sysop-privileged with `SYSOP=1`. The manual is blunt about what that
does:

!!! danger "SYSOP=1 grants full sysop status with no password"
    > "If you set `SYSOP=1`, all users who connect on this port get full sysop status without
    > needing to answer a password challenge. This is intended ONLY FOR USE ON SECURE LINKS,
    > such as RS232 or Ethernet, and the default is zero." — *XRouter manual*

In other words, `SYSOP=1` is appropriate only on a link that is *physically* trustworthy — a
direct serial cable, or a private wired LAN segment you control. **Never** set it on an RF
port, an AXIP/AXUDP internet link, or anything an outsider could connect to, because it hands
them the keys. The default (`0`) is the safe choice everywhere else; leave it alone unless you
have a specific, secure reason.

## Understand the IPADDRESS security behaviour

XRouter treats IP as off-by-default. If the global `IPADDRESS` is `0.0.0.0` or undefined, the
node **deliberately disables all IP activity** — AXUDP, AXTCP, AXIP, HTTP, FTP, everything, on
both its own stack and the Linux stack.

!!! warning "No IP address means no IP services — by design"
    > "If you set the global IPADDRESS to 0.0.0.0 or leave it undefined, *all* IP activity is
    > disabled … This is a deliberate security feature. Therefore at present, if you don't
    > have an amprnet address, and you want to use any IP services, it is suggested that you
    > set a non-zero dummy IP address such as `10.1.1.1`." — *Paula G8PZT,
    > [groups.io](https://groups.io/g/xrouter/message/1957)*

This matters two ways round. As a *feature*, it means a node with no IP configuration exposes
no IP attack surface at all — a sensible default for an RF-only station. As a *gotcha*, it
means that the moment you do want HTTP, FTP or an internet link you must give the node an
address (a private dummy such as `10.1.1.1` is fine if you have no AMPRNet allocation). The
flip side of that coin: only set an address — and so light up the IP services — once you
actually intend to run them. See
[IP services dead](troubleshooting.md#ip-services-wont-start) in the troubleshooting page for
the failure this causes.

## Run as a non-root user

Running a network-facing program as `root` means that any compromise of the program is a
compromise of the whole machine. XRouter does not need it. The manual recommends the
non-root path and explains why:

> "Non-root is safer. Even if a miscreant managed to break out to a command shell, he would
> only have access to that user's directory tree." — *XRouter manual*

The only reason XRouter ever wants elevated privilege is to bind low service ports (Telnet on
23, HTTP on 80, …) and to use raw sockets for `EXTERNAL`/Ethernet and AXIP. Grant it exactly
those two Linux capabilities once, with `setcap`, instead of running the whole node as root:

```bash
sudo setcap cap_net_raw,cap_net_bind_service=ep ./xrpi
```

Then run it as your ordinary user. Check the capabilities with `getcap ./xrpi`, and remember
to **re-apply `setcap` after replacing the binary** with a new version. The full walk-through
is on [Getting started on Raspberry Pi](../getting-started/raspberry-pi.md#6-run-it-without-root-recommended).

!!! tip "Don't use sudo either"
    Avoid `sudo ./xrpi` as a shortcut. Besides the security objection, the author notes the
    sudo session time-limit can make a long-running node stop responding or break the
    `RESTART` command. Use a genuine root account only if you must, and prefer the capability
    flags above.

## Restrict who can reach the node

Beyond authentication, you can limit *who connects in the first place*. XRouter offers
controls at several layers; reach for the one that fits the threat.

- **Telnet access control — `ACCESS.SYS`.** Set per-caller-IP login requirements: each line is
  `<subnet>[/bits] <flags>` where the flags sum `1` = valid callsign required, `2` = password
  required, `4` = guest allowed, `8` = trusted (LAN). Absent, a Telnet login needs only a
  valid callsign. Use it to demand a password from the wider internet while trusting your LAN.
- **AX.25 call filtering — `VALIDCALLS` and `EXCLUDE`.** Per port, `VALIDCALLS` is a whitelist
  (accept only these callsigns) and `EXCLUDE` is a blacklist (refuse these). A whitelist on a
  private backbone port, or a blacklist to keep a persistent nuisance off, are both reasonable.
- **IP banning — the `IP BAN` command and `IPBAN.SYS`.** Ban an individual address or a whole
  range by netmask; bans persist across restarts (and you can schedule `IP BAN SAVE` in
  `CRONTAB.SYS` so they survive a power cut).
- **Egress controls on the proxies.** If you run the SOCKS, Telnet or HTTP proxies, their
  `.ACL` files (`SOCKS.ACL`, `TELPROXY.ACL`, `HTTP.ACL`, `TELGUEST.ACL`) decide where a caller
  may be relayed *to*. These default to deny-all — leave them that way unless you deliberately
  open specific destinations, or an open proxy becomes someone else's free relay.

## The built-in IDS and honeypots

XRouter inherits an intrusion-detection subsystem from its DOS firewall days. At a high level
it watches the node's IP-facing side for hostile patterns and can react automatically:

- It detects TCP and UDP **port scans**, and classic spoofed-traffic patterns (Smurf, Fraggle).
- It can run **honeypots** on commonly probed but unused ports — an attacker who touches one is
  flagged and can be banned, with the ban recorded (and IDS events logged to the `LOG`
  directory). The author's own long-standing practice is to honeypot the popular unused ports
  and put real services on non-standard ports.
- Bans accumulate in `IPBAN.SYS` and persist across restarts; live control is via the `IDS`
  and `IP BAN` commands, and the dedicated Security Monitor console window shows activity.

Honeypots are a useful tripwire, not a wall — the author is candid that they are "not
foolproof" against an attacker with an unlimited supply of source addresses. Treat the IDS as
one layer among several, not the whole defence.

## Only expose the servers you need

Every open server is attack surface. XRouter starts a generous set of built-in TCP/UDP servers
by default — Telnet, HTTP, FTP, Finger, Echo, Discard, SOCKS, the APRS server, MQTT, the chat
server and more — and each has a matching `*PORT` directive. The convention is simple:

- a `*PORT` directive with **no value, or `0`, disables** that server;
- one value **moves** it to a different port;
- two whitespace-separated values set the XRouter-stack and Linux-stack ports independently.

So, for example, closing services you have no use for is just a line each:

```ini
ECHOPORT=0       ; close the Echo server
DISCARDPORT=0    ; close the Discard sink
FTPPORT=0        ; close FTP if you never do remote file maintenance
```

Decide deliberately which servers a given node should offer, and turn the rest off. A station
that only switches packet does not need a web server or an FTP daemon exposed to the world; an
RF-only node may not need any IP services at all (in which case leaving `IPADDRESS` unset, as
above, closes them all at a stroke). The fewer doors you leave open, the less there is to
defend. See [Built-in TCP servers](../subsystems/built-in-servers.md) and
[Ports and service numbers](../reference/ports-and-services.md) for the full list and defaults.

## A short hardening checklist

- [ ] A strong passphrase in `PASSWORD.SYS`, with tight file permissions.
- [ ] `SYSOP=1` only on physically secure links — never on RF or internet ports.
- [ ] Running as a non-root user with `cap_net_raw,cap_net_bind_service` via `setcap`.
- [ ] `IPADDRESS` set only if you actually run IP services.
- [ ] Access restricted where appropriate (`ACCESS.SYS`, `VALIDCALLS`/`EXCLUDE`, `IP BAN`).
- [ ] Proxy `.ACL` files left deny-all unless you deliberately opened a destination.
- [ ] Unused `*PORT` servers closed.
- [ ] Kept up to date — see [Upgrading and versions](upgrading.md).

---

**Sources:** [XRouter support group, groups.io](https://groups.io/g/xrouter) — the
`IPADDRESS` security behaviour ([message/1957](https://groups.io/g/xrouter/message/1957)) and
`setcap` guidance ([message/1958](https://groups.io/g/xrouter/message/1958)) ·
[in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs:man6) (running as
root vs non-root, capability flags; the `SYSOP=1` warning on
[man8](https://wiki.oarc.uk/packet:xrouter:docs:man8)) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/runroot.htm) (root vs non-root,
the sudo caveat)
