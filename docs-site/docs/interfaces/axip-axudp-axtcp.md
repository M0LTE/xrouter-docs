# AXIP / AXUDP / AXTCP linking

This page links your node to another node **over the internet** instead of over RF. It is for
a sysop who wants to join the wider packet network, or simply reach a partner node, across
the internet, carrying AX.25 (and NET/ROM on top of it) inside IP. If you want the bigger
picture of how nodes interconnect and route to each other, read
[Linking nodes and interoperating](../networking/linking-and-interop.md); this page is the
nuts and bolts of the three internet transports.

All three carry AX.25 frames inside IP, so to a NET/ROM neighbour the link looks just like an
RF hop. They differ only in the transport underneath:

| Transport | `TYPE` | Carried in | Choose it when… |
| --- | --- | --- | --- |
| **AXUDP** | `AXUDP` | UDP datagrams | Default choice, flexible, several partners can share one public IP. |
| **AXIP** | `AXIP` | Raw IP (protocol 93) | You want a few bytes less overhead per frame; one node per public IP. |
| **AXTCP** | `AXTCP` | A TCP stream | UDP is blocked, or a partner is behind NAT/CGNAT and needs TCP. |

!!! danger "No link works without a non-zero IPADDRESS"
    These are all IP links, so XRouter's security rule applies: if the global `IPADDRESS` is
    `0.0.0.0` or undefined, **all** IP activity, AXUDP, AXIP, AXTCP and the rest, is
    disabled, on both stacks. If you don't have an AMPRNet (44Net) address, set a private
    dummy such as `10.1.1.1`. Recent builds will not bring an AXIP/AXUDP link up at all
    without it., *see [Global settings](../configuration/global-settings.md)*

## AXUDP: the usual choice

AXUDP carries AX.25 over UDP. It is the most versatile of the three: it costs a handful of
bytes more per frame than AXIP, but several AXUDP nodes can sit behind a single public IP,
which AXIP cannot do.

The interface itself is minimal, just `TYPE` and `MTU`. The *partner* is defined on the
**port**:

- `IPLINK`, the partner's IP address or hostname.
- `UDPLOCAL`, the UDP port XRouter receives on. Default `93`.
- `UDPREMOTE`, the partner's UDP port (where you transmit to). Default `93`.

```ini
INTERFACE=2
        TYPE=AXUDP
        MTU=256
ENDINTERFACE

PORT=2
        ID=AXUDP link to G0XYZ
        INTERFACENUM=2
        IPLINK=partner.example.org   ; the far node's address
        UDPLOCAL=93                  ; we listen here
        UDPREMOTE=93                 ; the partner listens here
        QUALITY=192                  ; NET/ROM quality for this neighbour
ENDPORT
```

Both ends must agree: your `UDPREMOTE` must be the partner's `UDPLOCAL`, and vice versa. If
both nodes use the default `93` on each side, they match automatically.

## AXIP: slightly leaner

AXIP puts AX.25 directly into raw IP (protocol number 93), saving the UDP header, a little
less overhead per frame. The trade-off is that only **one AXIP node can live behind a given
public IP**, because there are no port numbers to tell two of them apart. The interface is
again just `TYPE`/`MTU`, and the partner is set on the port with `IPLINK`:

```ini
INTERFACE=3
        TYPE=AXIP
        MTU=256
ENDINTERFACE

PORT=3
        ID=AXIP link to G0XYZ
        INTERFACENUM=3
        IPLINK=partner.example.org
        QUALITY=192
ENDPORT
```

## AXTCP: for when UDP is blocked

AXTCP carries AX.25 inside a TCP stream, which traverses NAT and firewalls cleanly and suits
a partner on a mobile or CGNAT connection. One end listens (server) and the other connects
in (client); an AXTCP interface supports **one port only**.

- **Server end**, set `INTNUM` to the TCP port to listen on.
- **Client end**, set `CONFIG` to `<alias> <host> <tcp_port>` identifying the partner to
  dial.

```ini
; --- Server end (listens for the partner to connect) ---
INTERFACE=4
        TYPE=AXTCP
        INTNUM=9393          ; TCP port we listen on
        MTU=256
ENDINTERFACE

PORT=4
        ID=AXTCP server for G0XYZ
        INTERFACENUM=4
        QUALITY=192
ENDPORT
```

```ini
; --- Client end (dials the server above) ---
INTERFACE=4
        TYPE=AXTCP
        CONFIG=G0XYZ partner.example.org 9393   ; alias, host, port
        MTU=256
ENDINTERFACE

PORT=4
        ID=AXTCP client to G0XYZ
        INTERFACENUM=4
        QUALITY=192
ENDPORT
```

## Making NET/ROM run over the link

A link that passes AX.25 is not automatically a NET/ROM neighbour. For NET/ROM to route over
it, the port needs a non-zero **`QUALITY`** (the default neighbour quality is low, and
`QUALITY=0` disables NET/ROM on the port entirely). Set a `QUALITY` that reflects how good the
link is relative to your RF paths, the examples above use `192` as a placeholder; choose a
value that fits your network. The connection-control flags **`CFLAGS`** govern up/downlinking
and the L3RTT behaviour on the port; the defaults are usually fine for an internet link, but
see [Ports](../configuration/ports.md) and
[NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md) for the detail.

!!! tip "Firewall and port-forwarding"
    For partners to reach you, the relevant traffic must arrive at your node. For **AXUDP**
    forward the UDP port you listen on (`UDPLOCAL`, default 93); for **AXTCP server** forward
    the TCP `INTNUM`; for **AXIP** allow IP protocol 93. An **AXTCP client** dials *out* and
    usually needs no inbound rule, which is exactly why AXTCP is the answer when you cannot
    open ports. Coordinate the addresses and ports with your link partner before you start.

---

**Sources:** [In-program manual, OARC wiki: §6 Installation & Configuration](https://wiki.oarc.uk/packet:xrouter:docs:man6)
(AXIP/AXUDP/AXTCP interfaces, `IPLINK`, `UDPLOCAL`/`UDPREMOTE`, `INTNUM`, `CONFIG`) ·
[groups.io: IPADDRESS is effectively mandatory for IP links](https://groups.io/g/xrouter/message/1957) ·
[ohiopacket.org XRPi mirror: interfaces](https://ohiopacket.org/xrpi/docs/ifaces.htm)
