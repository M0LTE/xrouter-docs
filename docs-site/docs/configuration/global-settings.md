# Global settings

The **GLOBAL** section of `XROUTER.CFG` is everything that sits outside an `INTERFACE`,
`PORT`, `RADIO`, `CONSOLE` or `APPL` block. It is where the node's identity lives, where the
built-in servers are switched on or moved, and where network-wide NET/ROM and IP defaults are
set. This page is a guided tour of the directives you are most likely to touch; the full
keyword-by-keyword listing is in the [directive reference](directive-reference.md).

Global directives can go anywhere outside a block, but convention — and readability — put
them near the top of the file, above the interfaces and ports.

!!! note "Defaults can vary by version"
    XRouter is closed-source and releases briskly (the v504 series at the time of writing).
    A few documented defaults differ between the in-program manual and older mirrors. Where
    that is the case this page flags it rather than asserting a single number. When in doubt,
    set the value explicitly and check it on your own build with the matching sysop command.

## Identity

These establish who the node is on the air and where it claims to be. `NODECALL` and
`NODEALIAS` are **mandatory** — XRouter will not start without them.

| Directive | Meaning | Notes |
| --- | --- | --- |
| `NODECALL` | The node's primary AX.25 / NET/ROM callsign. | Mandatory. A callsign plus an SSID (1–15), e.g. `M0XXX-1`. Used for all Layer-3/4 traffic and as the default Layer-2 callsign on each port. |
| `NODEALIAS` | The node's primary alias. | Mandatory. Up to 6 characters, **no SSID**, e.g. `MYNODE`. |
| `CONSOLECALL` | Callsign used for outgoing connections from the sysop console. | Defaults to `NODECALL`. Set it if you want console-originated connects to show a different callsign. |
| `QTH` | A free-text description of the node's location. | Shown to users and to mapping. Keep it short. |
| `LOCATOR` | Maidenhead locator for the site. | 6- or 8-character grid square, e.g. `IO91WM`. |

Other location directives exist for nodes that want to appear on a network map —
`CONTACT`, `LATITUDE`, `LONGITUDE`, `ALTITUDE`, `HAAT` and `MAPCOMMENT` among them — but
`QTH` and `LOCATOR` are enough for most stations. See the [directive
reference](directive-reference.md) for the rest.

## The IPADDRESS security behaviour

`IPADDRESS` sets the node's primary IP address (an AMPRNet / 44Net address if you have one).
It looks like an optional, IP-only directive — but it is not, because of a deliberate
security design choice.

!!! danger "No IPADDRESS, no IP services at all"
    If the global `IPADDRESS` is `0.0.0.0` or left undefined, XRouter **deliberately disables
    all IP activity** — AXUDP, AXTCP, AXIP, HTTP, FTP, the lot — on **both** the XRouter and
    Linux stacks. This is an intentional security feature, not a bug. If you do not have an
    AMPRNet address but want any IP-based service or internet link to work, set a private
    dummy address such as `10.1.1.1`. Recent builds will not work — and may refuse to start
    cleanly — with `IPADDRESS` omitted. — *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

```ini
IPADDRESS=10.1.1.1       ; real 44Net address if you have one, else a private dummy
```

Related global IP directives include `HOSTNAME`, `DNS`, `DOMAIN`, `DCACHE`, `MAXARP`,
`IPTTL`, and the tunnelling toggles `IPENCAP`, `IPIP` and `IPUDPPORT`. IP *routing* (routes,
ARP, NAT, RIP) is configured separately in `IPROUTE.SYS` — see [System and data
files](system-files.md).

## Built-in servers: enable, move or disable

XRouter ships a family of internet-style servers, each listening on a well-known TCP (or UDP)
port. They are enabled by default. Each has a `…PORT` directive that controls it:

- Give it **no value or `0`** to disable that server.
- Give it **one value** to move it to a different port.
- Give it **two whitespace-separated values** to set the XRouter-stack port and the
  Linux-stack port independently (for example `TELNETPORT=23 0` keeps Telnet on the XRouter
  stack but disables it on the Linux stack).

| Directive | Server | Default port |
| --- | --- | --- |
| `TELNETPORT` | Telnet (user sessions) | 23 |
| `HTTPPORT` | HTTP / web server (with REST API) | 80 |
| `FTPPORT` | FTP (sysop maintenance) | 21 |
| `FINGERPORT` | Finger | 79 |
| `RLOGINPORT` | Rlogin (sysop access) | 513 |
| `TTYLINKPORT` | TTYLINK (keyboard-to-keyboard) | 87 |
| `TELPROXYPORT` | Telnet proxy | 2323 |
| `CHATPORT` | Chat server | 3600 |
| `APRSPORT` | APRS server | 1448 |
| `MQTTPORT` | MQTT broker | 1883 |
| `SOCKSPORT` | SOCKS proxy | 1080 |
| `AGWPORT` | AGW Packet Engine emulator | 8000 |
| `RHPPORT` | Remote Host Protocol (host API) | 9000 |
| `ECHOPORT` / `DISCARDPORT` | Echo / Discard (link testing) | 7 / 9 |

Binding ports below 1024 needs either root or the right Linux capability — see the `setcap`
guidance on [Raspberry Pi](../getting-started/raspberry-pi.md) and [Security and
hardening](../admin/security-hardening.md). A full list of servers, ports and NET/ROM
service numbers is on [Ports and service numbers](../reference/ports-and-services.md).

## NET/ROM globals

These tune the node's NET/ROM behaviour network-wide. Per-port overrides for several of them
(notably `QUALITY`, `MINQUAL` and `NODESINTERVAL`) live in the [port block](ports.md).

| Directive | Meaning | Default |
| --- | --- | --- |
| `NODESINTERVAL` | Minutes between NODES broadcasts. | 60 |
| `MAXNODES` | Maximum size of the nodes table. | 200 |
| `MAXROUTES` | Maximum number of neighbour routes. | 30 |
| `MINQUAL` | Minimum quality for a node to be added to the table. | 10 |
| `OBSINIT` / `OBSMIN` | NET/ROM obsolescence: initial value / minimum to broadcast. | 5 / 3 |
| `L3TTL` | NET/ROM Layer-3 time-to-live (hops). | 25 |
| `L4WINDOW` | NET/ROM Layer-4 window. | 10 |
| `L4TIMEOUT` | NET/ROM Layer-4 timeout (seconds). | 120 |
| `L4RETRIES` | NET/ROM Layer-4 retries. | 3 |
| `L4DELAY` | NET/ROM Layer-4 delay (seconds). | 3 |
| `MAXCIRCUITS` | Maximum concurrent NET/ROM Layer-4 circuits. | 20 |

The INP3 link-state extension adds `MAXHOPS` and `MAXTT` (the hop-count and trip-time
*horizons*), along with `L3RTTINTERVAL` and `INPBCINTERVAL`. INP3 and L3RTT are covered on
[NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).

!!! tip "Be a good NET/ROM neighbour"
    Don't drop the *global* `NODESINTERVAL` below 60 to satisfy one fussy neighbour — that
    floods the whole network with broadcasts. If a particular link needs more frequent
    broadcasts, set `NODESINTERVAL` inside that one [port block](ports.md) instead. Likewise
    use `MINQUAL`, the per-port `QUALITY`/`MINTXQUAL`, `CHATQUAL` and `PMSQUAL` to keep your
    table from bloating and to limit needless "DXing" of distant services.

## Chat, PMS and APRS identity

The chat server, the personal mailbox (PMS) and the APRS IGate each have their own callsign,
alias and NET/ROM quality so they can appear in the nodes table as separate destinations.

| Subsystem | Directives | Notes |
| --- | --- | --- |
| Chat server | `CHATCALL`, `CHATALIAS`, `CHATQUAL` | An SSID of `-8` is conventional; aliases conventionally end in `CHT`. A `CHATQUAL` of 0 makes the chat server invisible over NET/ROM. The documented default quality differs between sources (150 vs 255), so set it explicitly. Linked peers are listed with `CHATLINKS`. |
| PMS mailbox | `PMSCALL`, `PMSALIAS`, `PMSQUAL` | Omitting `PMSCALL` disables the mailbox. An SSID of `-2` is conventional. `PMSQUAL` defaults to 0 (not broadcast over NET/ROM); set it non-zero to advertise the mailbox. |
| APRS IGate | `APRSCALL` | The callsign used by the IGate; defaults to `NODECALL`. The IGate proper is configured in `IGATE.CFG`. |

```ini
CHATCALL=M0XXX-8
CHATALIAS=MYCHT
CHATQUAL=150             ; set explicitly — the documented default varies by version

PMSCALL=M0XXX-2
PMSALIAS=MYPMS
PMSQUAL=0               ; 0 = mailbox not advertised over NET/ROM
```

See [Chat server](../subsystems/chat.md), [PMS mailbox](../subsystems/pms-mailbox.md) and
[APRS digipeater and IGate](../subsystems/aprs-igate.md) for the full picture.

## DNS and name resolution

XRouter has an internal resolver and can also use an external one. The `DNS` directive names
the DNS server to use; `DOMAIN` sets the default domain suffix (it defaults to `ampr.org.`)
and `DCACHE` sizes the domain cache. Local host overrides go in `DOMAIN.SYS`.

!!! tip "Comment out a baked-in DNS line if it misbehaves"
    Some builds ship `XROUTER.CFG` with a line such as `DNS=8.8.8.8`. If name resolution
    causes trouble on your network, comment that line out and let the internal resolver, or
    your own DNS, take over.

---

**Sources:**
[Configuration directives, OARC wiki (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[XROUTER.CFG, OARC wiki (man8)](https://wiki.oarc.uk/packet:xrouter:docs:man8) ·
[ohiopacket.org XRPi mirror — global keywords](https://ohiopacket.org/xrpi/docs/xrcfg.htm) ·
[IPADDRESS guidance, groups.io](https://groups.io/g/xrouter/message/1957)
