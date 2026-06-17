# NetRom-X service numbers

NetRom-X (also written NetromX or NET/ROM-X) is XRouter's scheme for exposing many services
behind a **single node callsign**, each reached by a numbered service — much like well-known
TCP ports. This page explains why it exists, the standard service numbers, and how a user
connects to one. It is aimed at sysops and at users who want to reach a node's mailbox or chat
server directly.

## Why it exists: SSID exhaustion

Traditionally each service on a packet station got its own SSID. The author explains the
problem plainly:

> "Long ago, ssid 0 was your keyboard ssid... PMS were usually -2, IP was -5. Bpq chat is
> -11, xrchat is -8, and it all goes to pot if you run more than one node... That's why I
> developed NetromX in XRouter. You have one node callsign, which supports up to 65535
> Services. The service numbers are standard, like tcp/udp well known ports, so if you
> connect to service 2 on any xrpi it is the PMS." — *Paula G8PZT (community, OARC)*

An AX.25 callsign has only 16 SSIDs (0–15), so a node running a mailbox, a chat server, an
APRS server, an information server and so on quickly runs out — and a sysop running more than
one node makes it worse. NetRom-X sidesteps the limit entirely: **one** node callsign exposes
up to **65535** numbered services, and because the numbers are standardised, the same number
means the same thing on any XRouter node. Connect to service 2 anywhere and you reach that
node's PMS; service 8 is always the chat server.

## The standard service numbers

The numbers below are the standard NetRom-X services documented in the manual. (XRouter
defines more than these; the table lists the common, human-usable ones.)

| Number | Service | What it is |
| --- | --- | --- |
| `1` | INFO | Node information server (the node's "I" info) |
| `2` | PMS | Personal Message System — the integral mailbox |
| `7` | ECHO | Echoes data back; link testing |
| `8` | CHAT | The XRChat chat / conference server |
| `9` | DISCARD | Data sink; link testing |
| `13` | DAYTIME | Local date and time |
| `14` | APRS | APRS server |
| `23` | TELNET | Layer-4 login (equivalent to the node command line) |
| `79` | FINGER | Finger user-information server |
| `80` | HTTP | NetRomWeb — HTTP carried over NET/ROM |

A few of these deliberately mirror the equivalent TCP well-known ports (7 echo, 9 discard, 13
daytime, 23 telnet, 79 finger, 80 http), which makes them easy to remember. Note that the
service number is carried *inside* the NET/ROM connection, so this works over the radio network
without any IP at all.

!!! note "Numbers can vary by build"
    The exact set and meaning of higher service numbers has grown over time, and some are
    reserved for future use rather than active. The numbers above are the well-established,
    user-facing ones; for the complete current list defer to the in-program manual and the
    [Ports and service numbers reference](../reference/ports-and-services.md).

## Connecting to a service

You reach a service by adding its number to a normal `C[onnect]`. The general form is:

```text
C <node> <service>
```

So to reach the PMS on a node whose alias is `KIDDER` (or by callsign), a user types:

```text
C KIDDER 2
```

and to drop straight into that node's chat server:

```text
C KIDDER 8
```

Connecting with no service number — `C KIDDER` — lands you at the node command line
(service 0) as usual, from where the ordinary commands (`PMS`, `CHAT`, `INFO`, and so on)
are also available. The service number is just a shortcut that connects you directly to the
service, which is convenient over a slow link or from an automated client.

Because the destination is resolved through XRouter's routing tables, you can connect to a
distant node's service by name from anywhere in the network — the routing layer (see
[NET/ROM, INP3 and L3RTT](netrom-inp3.md)) finds the path, and the service number selects
what you talk to once you arrive.

## See also

- [Ports and service numbers reference](../reference/ports-and-services.md) — the full table,
  alongside the TCP/IP service ports.
- [NET/ROM, INP3 and L3RTT](netrom-inp3.md) — how the node you are addressing is reached.
- [PMS mailbox](../subsystems/pms-mailbox.md) and [Chat server](../subsystems/chat.md) — the
  two services users reach most via NetRom-X.

---

**Sources:** in-program manual on the OARC wiki —
[Subsystems and features (man9), Services list](https://wiki.oarc.uk/packet:xrouter:docs:man9)
and the `generalhelp` SERVICES list; author's explanation of the scheme and its origin
(community knowledge, OARC), corroborated by the manual's service-number table.
