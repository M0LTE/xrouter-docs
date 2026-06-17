# Built-in TCP servers

XRouter bundles a family of small internet servers, Telnet, HTTP, FTP, Finger and several
more, so the node can be operated, queried and integrated without any external software.
This page explains the common pattern for enabling, moving and disabling them, lists the
servers and their default ports, and flags the security points that matter. The complete
reference table lives at [Ports and service numbers](../reference/ports-and-services.md).

## One pattern: the `*PORT` directive

Every built-in server is controlled by a global directive ending in `PORT`, and they all
behave the same way:

- **Set the directive to a port number to enable the server** on that port.
- **Leave it at its default** to run the server on its well-known port.
- **Set it to `0` (or omit it where the server is off by default) to disable** the server.

Moving a server is therefore just a matter of changing its port; disabling one is setting it
to zero. For example:

```ini
TELNETPORT=23      ; Telnet server on the standard port
FTPPORT=0          ; FTP server disabled
HTTPPORT=8080      ; move the web server off port 80
```

This makes it easy to expose only the servers you actually use, and to relocate any that
clash with something else on the host.

## The servers and their default ports

The directives and defaults below are drawn from the in-program manual. This is a summary;
the authoritative, complete list (including the proxy and IP-tunnel ports) is at
[Ports and service numbers](../reference/ports-and-services.md).

| Server | Directive | Default port |
| --- | --- | --- |
| Telnet | `TELNETPORT` | 23 |
| Telnet proxy | `TELPROXYPORT` | 2323 |
| TTYLINK (keyboard-to-keyboard) | `TTYLINKPORT` | 87 |
| Rlogin (sysop) | `RLOGINPORT` | 513 |
| FTP | `FTPPORT` | 21 |
| Finger | `FINGERPORT` | 79 |
| HTTP (web UI + REST) | `HTTPPORT` | 80 |
| Echo | `ECHOPORT` | 7 |
| Discard | `DISCARDPORT` | 9 |
| SOCKS proxy | `SOCKSPORT` | 1080 |
| APRS server | `APRSPORT` | 1448 |
| Chat server | `CHATPORT` | 3600 |
| MQTT broker | `MQTTPORT` | 1883 |
| AGWPE emulator | `AGWPORT` | 8000 |
| Remote Host Protocol | `RHPPORT` | 9000 |

The HTTP, REST and MQTT servers are covered in detail on
[HTTP, REST and MQTT](http-rest-mqtt.md); the chat and APRS servers have their own pages
([Chat server](chat.md), [APRS digipeater and IGate](aprs-igate.md)).

!!! note "FTP is for maintenance, not a file service"
    XRouter's FTP server exists for **system maintenance**, uploading config and managing
    files on a remote node, rather than as a public file library. Limited non-sysop FTP
    accounts are possible via `USERPASS.SYS`, but treat FTP as an admin tool.

## Security notes

These servers are network-facing, so a few cautions apply.

!!! danger "`IPADDRESS` gates every IP server"
    None of these servers run if the global `IPADDRESS` is unset or `0.0.0.0`, XRouter
    deliberately disables all IP activity in that case. Set a (private if necessary)
    `IPADDRESS` to bring the IP servers up. See
    [Getting started on Raspberry Pi](../getting-started/raspberry-pi.md).

!!! warning "`SYSOP=1` and secure links"
    Several servers grant administrative reach (Rlogin, and the Telnet server when a caller
    authenticates as sysop). The per-port `SYSOP=1` directive gives **every** caller on that
    port full sysop status *without a password challenge*. The manual restricts this to
    genuinely secure links, RS232 or a trusted Ethernet segment, and the default is `0`.
    Never set `SYSOP=1` on a port reachable from RF or the open internet. Protect remote
    sysop access with `PASSWORD.SYS` and, where appropriate, the access-control files. See
    [Security and hardening](../admin/security-hardening.md).

!!! tip "Run only what you need, and not as root"
    Disable servers you don't use by setting their `*PORT` to `0`, and bind the privileged
    ports (Telnet, HTTP, FTP) using Linux capabilities rather than running XRouter as root,
    see [Getting started on Raspberry Pi](../getting-started/raspberry-pi.md).

---

**Sources:** [In-program manual, OARC wiki: Section 9 (servers)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[In-program manual: Section 7 (directives, default ports)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[ohiopacket.org XRPi mirror: servers](https://ohiopacket.org/xrpi/docs/index.htm) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
