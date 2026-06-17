# Ports and service numbers

Two quick-reference tables: the **default TCP listening ports** for XRouter's
built-in internet servers (and the `XROUTER.CFG` directive that controls each),
and the **NetRom-X service numbers** XRouter uses to expose several services from
one node callsign. Both sets of defaults can be changed, and the exact list varies
a little by version — treat these as the out-of-the-box values, not protocol law.

!!! note "Defaults change; check your own build"
    XRouter is closed-source freeware under brisk development, and the numbers below
    are gathered from the in-program manual. Default ports and service numbers can be
    changed and may vary by version. Where a value is marked *unconfirmed* it appears in
    the broader convention but was not pinned to a primary source for the build series
    these docs were written against. The live answer for your node is always the
    in-program manual (`MAN`) and your own `XROUTER.CFG`.

## Default TCP server ports

Each of XRouter's internet servers listens on a well-known TCP port by default. The
port is set with the directive in the third column, used in the GLOBAL section of
[`XROUTER.CFG`](../configuration/global-settings.md). Setting a server's port — and,
on Linux, the second argument that selects the IP stack — is how you move a service,
or disable it on the Linux stack. See [Built-in TCP servers](../subsystems/built-in-servers.md)
for what each one does.

| Service | Default port | Controlling directive | Notes |
| --- | --- | --- | --- |
| Echo | 7/tcp | `ECHOPORT` | Link-test data sink that echoes input back. |
| Discard | 9/tcp | `DISCARDPORT` | Link-test data sink that swallows input. |
| FTP | 21/tcp | `FTPPORT` | Intended for sysop file maintenance, not general use. |
| Telnet | 23/tcp | `TELNETPORT` | Main interactive login to the node. |
| DNS | 53/udp | `DNS` | XRouter's own resolver; `DNS` names the upstream server, not a listen port. |
| Finger | 79/tcp | `FINGERPORT` | User-information server. |
| HTTP | 80/tcp | `HTTPPORT` | Web UI, REST API and HTTP proxy. |
| TTYLINK | 87/tcp | `TTYLINKPORT` | Keyboard-to-keyboard "talk" server. |
| NTTY | 87 | *(see note)* | NetRom-X keyboard chat; shares the TTYLINK role. |
| POP3 | 110/tcp | `POP3PORT` | Mailbox retrieval. Directive confirmed; default *unconfirmed*. |
| IPUDP | 94/udp | `IPUDPPORT` | Receives IP-over-UDP (IPUDP) traffic. |
| RLOGIN | 513/tcp | `RLOGINPORT` | Sysop remote login. |
| SOCKS | 1080/tcp | `SOCKSPORT` | SOCKS proxy. |
| APRS | 1448/tcp | `APRSPORT` | APRS server (for clients such as UI-View). |
| MQTT | 1883/tcp | `MQTTPORT` | Built-in MQTT broker. |
| Telnet proxy | 2323/tcp | `TELPROXYPORT` | Outbound Telnet proxy. |
| Chat | 3600/tcp | `CHATPORT` | Multi-channel chat / Converse server. |
| AGWPE emulator | 8000/tcp | `AGWPORT` | XRouter acting as an AGW Packet Engine for client apps. |
| RHP (Remote Host Protocol) | 9000/tcp | `RHPPORT` | Host API used by WhatsPac and others. |

!!! warning "The IPADDRESS security behaviour"
    If the global `IPADDRESS` is `0.0.0.0` or undefined, **all** IP activity is
    disabled — including these TCP servers and AXIP/AXUDP/AXTCP — on both XRouter's
    own stack and the Linux stack. This is deliberate. If you have no AMPRNet address
    but want IP services, set a non-routable dummy address (for example `10.1.1.1`).
    See [Security and hardening](../admin/security-hardening.md) and
    [The TCP/IP stack](../networking/tcpip.md).

!!! tip "On Linux, many of these are below 1024"
    Binding ports under 1024 (Echo, Discard, FTP, Telnet, Finger, HTTP, RLOGIN…) on
    the Linux stack needs either root or the `CAP_NET_BIND_SERVICE` capability. See
    [Running as a service](../getting-started/running-as-a-service.md).

## NetRom-X standard service numbers

[NetRom-X](../networking/netromx-services.md) lets one node callsign present many
numbered services, much like well-known TCP ports on an IP host: a caller connects to
the node and asks for a service number instead of needing a separate SSID per service.
The numbers below mirror the equivalent TCP/internet service where one exists (so
`8` is chat, `14` is APRS, `19` is chargen). The author has shipped a growing set of
"standard services" — APRS, CHARGEN, CHAT, DISCARD, ECHO, INFO, NFTP, NTTY, PMS, SMS,
TIME and WX have all been named in release notes.

The numbers in this table that are marked **documented** appear in the in-program
manual's service list; the remainder follow the well-known-port convention and are
included for orientation but should be **verified against your version** before you rely
on them.

| Service number | Service | Status |
| --- | --- | --- |
| 0 | CMD (node command prompt) | Convention — verify |
| 1 | INFO | Documented |
| 2 | PMS (mailbox) | Documented |
| 7 | ECHO | Convention — verify |
| 8 | CHAT (Converse) | Documented |
| 9 | DISCARD | Convention — verify |
| 13 | DAYTIME | Documented |
| 14 | APRS | Documented |
| 16 | WX (weather) | Documented |
| 18 | SMS (Short Message System) | Documented |
| 19 | CHARGEN | Documented |
| 21 | NFTP (NetRom File Transfer) | Documented |
| 23 | TELNET | Convention — verify |
| 79 | FINGER | Convention — verify |
| 80 | HTTP | Convention — verify |
| 87 | NTTY (keyboard chat) | Convention — verify |
| 110 | POP3 | Convention — verify |
| 1883 | MQTT | Convention — verify |

!!! note "Why some numbers are only \"convention\""
    NetRom-X numbering deliberately echoes TCP's well-known ports, and the named
    services above (PMS, ECHO, DISCARD, TELNET, FINGER, HTTP, POP3, MQTT…) all exist
    in XRouter. The numbers marked *documented* are those given in the in-program
    manual's service list (1, 2, 8, 13, 14, 16, 18, 19, 21); the rest are listed because
    the convention strongly implies them, not because a manual page names that exact
    number. If you are documenting or scripting against a specific build, confirm the
    number with the in-program manual.

---

**Sources:** [In-program manual mirrored on the OARC wiki — services and directives (man6/man7/man9)](https://wiki.oarc.uk/packet:xrouter:docs) ·
[In-program manual — service list (man9)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[ohiopacket.org XRPi mirror — XROUTER.CFG keyword reference](https://ohiopacket.org/xrpi/docs/xrcfg.htm) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
