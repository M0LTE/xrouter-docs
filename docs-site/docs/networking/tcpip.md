# The TCP/IP stack

XRouter carries its **own** multi-homed TCP/IP stack, running alongside the host operating
system's stack rather than replacing it. This page explains what that stack does, the all-important
`IPADDRESS` requirement, how IP is routed (`IPROUTE.SYS`, NAT, RIP and tunnels), and how to
choose XRouter's stack or the kernel's per route. It is aimed at sysops doing IP over packet,
joining AMPRNet/44Net, or tunnelling AX.25 over the internet.

If amateur IP is new to you, read **[IP over AX.25 and AMPRNet](../basics/ip-over-ax25.md)**
first; this page is about XRouter's implementation, not the concepts.

## Two stacks, side by side

A node running XRouter on Linux (or Windows) has **two** IP stacks available:

- the **host's** stack (the Linux kernel, or Windows), and
- **XRouter's own** multi-homed stack, built into the program.

They are independent, and the sysop decides which services and which traffic use which. The
broad rule of thumb is:

- If you define **no** Ethernet/WiFi port and do **no** routing between radio and LAN, all
  IP simply uses the host stack.
- If you define an Ethernet/WiFi port (an `EXTERNAL` interface with a port), XRouter's own
  stack takes over non-localhost IP, giving the node its own LAN address and the ability to
  route IP between radio ports and the LAN.

This is what people mean when they say XRouter has IP "designed in" rather than bolted on:
the same program that switches AX.25 and NET/ROM also routes IP, with one consistent
configuration. It also means XRouter is **largely independent of the Linux kernel's AX.25
subsystem**, it brings its own AX.25 *and* IP stacks and talks KISS, serial and TCP/UDP
directly. As the kernel AX.25 code ages, XRouter keeps working.

## IPADDRESS: required, and a security feature

The single global directive that gates all of this is `IPADDRESS`. It is the node's core
(AMPRNet / 44Net) IP address, which the ports inherit unless overridden. It also has a
deliberate security behaviour that surprises newcomers:

!!! danger "No IPADDRESS means no IP, on purpose"
    If the global `IPADDRESS` is `0.0.0.0` or left undefined, XRouter **deliberately disables
    all IP activity**, AXUDP, AXTCP, AXIP, HTTP, FTP and the rest, on **both** stacks. This
    is an intentional security feature, not a bug. If you do not have an AMPRNet (44Net)
    address but want any IP-based feature to work, set a private dummy such as `10.1.1.1`.
    Recent builds will not work with `IPADDRESS` omitted., *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

This is the most common reason a sysop finds that their internet links or web server "just
don't work" on a fresh install. The fix is one line. The same warning appears on the
[getting-started](../getting-started/raspberry-pi.md) path because it bites so often.

A minimal global IP setup looks like:

```ini
IPADDRESS=10.1.1.1        ; your 44Net address, or a private dummy if you have none
NETMASK=255.255.255.0     ; on the relevant port, where applicable
```

## IP routing with IPROUTE.SYS

XRouter's IP router is configured from the optional **`IPROUTE.SYS`** file, read at boot (and
reloadable live with `IP ROUTE LOAD`). One entry per line, it defines routes and the IP-layer
facilities that ride on the router. Its command set includes:

- `IP ROUTE ADD` / `IP ROUTE DEFAULT`, add routes, including the default route, each tagged
  with the stack to use (see below).
- `ARP`, static ARP entries and ARP control.
- `NAT`, Network Address Translation (`NAT ADD STATIC`, `NAT ADD OVERLOAD`), so a node can
  masquerade radio-side hosts behind one address.
- `RIP`, the RIP routing-information protocol, to learn and advertise routes dynamically
  (enabled with `RIP LEARN ON`).
- `ACL`, packet-filtering rules.

You only need `IPROUTE.SYS` if you are actually doing IP routing or IP facilities; a node that
just runs the built-in servers over a single address does not require it.

## Choosing the XRouter stack or the kernel stack, per route

Because both stacks exist, each IP route can be pinned to one of them. In XRouter's IP routing,
routes carry a **mode** letter, notably `d` for XRouter's own stack and `k` for the host
(kernel) stack. To force particular traffic onto the kernel stack, you give it a `k`-mode route;
otherwise it travels via XRouter's own (`d`) stack. (The `k` "kernel" mode was previously
called `w` for "Windows" and the old letter is still accepted for backward compatibility.)

This per-route choice is what lets a node, for example, serve AMPRNet traffic on its own stack
while sending some other traffic straight out through the host's normal networking.

## NAT, RIP and packet filtering

The facilities above are worth a sentence each in context:

- **NAT** lets the node present radio-side or downstream hosts to the wider network behind a
  single IP, the familiar masquerading pattern, configured with `NAT ADD STATIC` /
  `NAT ADD OVERLOAD`.
- **RIP** lets XRouter participate in dynamic routing rather than relying solely on static
  routes, handy on a larger 44Net subnet.
- **Packet filtering** via `ACL` rules gives the IP router a basic firewall.

These are all part of the same `IPROUTE.SYS` / IP-router subsystem; reach for them only when
your topology needs them.

## IP tunnels: IPIP, IPUDP and AXIP

XRouter can tunnel IP and AX.25 across the internet several ways:

- **IPIP** (IP-in-IP, protocol 94) and **IPENCAP** (protocol 4), the AMPRNet encapsulation
  schemes, enabled with `IPIP=1` / `IPENCAP=1`, with encapsulated routes read from the
  `ENCAP.TXT` / `XENCAP.TXT` files. This is how 44Net hosts reach each other across the public
  internet.
- **IPUDP**, IP carried over UDP, with the receive port set by `IPUDPPORT`.
- **AXIP / AXUDP / AXTCP**, these carry *AX.25* (not raw IP) over the internet and are how you
  link **nodes** over the net. They are interfaces in their own right and are documented on
  **[AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md)**; the practical linking
  walk-through is on [Linking nodes and interoperating](linking-and-interop.md). Note that
  because they are IP-based, they too are subject to the `IPADDRESS` rule above.

## Relation to AMPRNet / 44Net

Most of this stack's reason for existing is amateur IP, the **44.0.0.0/8** AMPRNet (44Net)
address space. XRouter's `IPADDRESS` is, properly, your 44Net address; its IPIP/IPENCAP tunnels
are the standard way 44Net is carried over the internet; and its router, NAT and RIP let a node
act as a real 44Net gateway between radio and the wider network. The conceptual background to
all of that is on **[IP over AX.25 and AMPRNet](../basics/ip-over-ax25.md)**.

!!! tip "Bind a service to one stack only"
    Many of the service-port directives accept two values, the XRouter-stack port and the
    host-stack port, so you can, for instance, run Telnet on the radio/AMPRNet side but not
    expose it on your LAN, or vice versa. See the built-in servers and the
    [Ports and service numbers reference](../reference/ports-and-services.md).

## See also

- [IP over AX.25 and AMPRNet](../basics/ip-over-ax25.md), the concepts behind 44Net.
- [AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md), linking nodes over the
  internet.
- [Security and hardening](../admin/security-hardening.md), running the IP services safely.

---

**Sources:** in-program manual on the OARC wiki,
[Installation and configuration topics (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6),
including the two-IP-stacks topic ·
[Configuration Directives (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[Subsystems and features (man9)](https://wiki.oarc.uk/packet:xrouter:docs:man9);
the `IPADDRESS` security behaviour from the author,
[groups.io](https://groups.io/g/xrouter/message/1957);
author's IP-stack notes on the ohiopacket mirror,
[ohiopacket.org/xrpi](https://ohiopacket.org/xrpi/docs/ip-stacks.htm).
