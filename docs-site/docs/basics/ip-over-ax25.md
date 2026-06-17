# IP over AX.25 and AMPRNet

Because [AX.25](ax25.md) is only a layer-2 link, you can run a network protocol on top of it
— including TCP/IP. This primer explains IP over AX.25, the AMPRNet (44Net) address space,
why a node might route IP at all, and how it relates to XRouter's own IP stack.

## Running TCP/IP over packet

AX.25 carries frames between stations; it does not care what is inside them. So just as it
can carry NET/ROM, it can carry **IP packets**, giving you TCP/IP over amateur radio. The
landmark early software was **KA9Q NET/NOS** by Phil Karn — an MS-DOS program implementing
AX.25, IP, TCP and UDP over a KISS TNC, complete with an email mailer.

## AMPRNet and 44Net

**AMPRNet** (Amateur Packet Radio Network), also called **Network 44** or **44Net**, is an
amateur TCP/IP internetwork carried over amateur links and internet tunnels. It is
historically remarkable: in **1981 Hank Magnuski** obtained the class-A block
**44.0.0.0/8** — some 16.7 million addresses — for amateur use worldwide, *before* the public
internet's TCP/IP flag day in 1983. Addresses are coordinated by volunteers.

The picture has changed with IPv4 scarcity. In 2019 the block `44.192.0.0/10` was sold for
conventional use, the proceeds funding **Amateur Radio Digital Communications (ARDC)**, which
now stewards the space and makes grants. The remaining amateur allocations are `44.0.0.0/9`
(US) and `44.128.0.0/10` (rest of the world).

## How it links together

In practice many 44Net subnets are not RF-adjacent to one another, so they are interconnected
by **IP-in-IP tunnels** over the internet. Closer to home, amateur AX.25 nodes commonly
interlink over the internet using **AXIP** and **AXUDP** — encapsulations that tunnel raw
AX.25 frames inside IP or UDP. (Note that amateur regulations restrict encryption and some
content, and rules vary by country.)

## Why a node might route IP

Even though TCP/IP was always a minority on classic packet — its overhead bites at
1200–9600 baud — there are good reasons a node carries it: to provide local users IP
connectivity, to reach other systems on the same machine, and to link to neighbouring nodes
over internet tunnels. Higher-speed amateur IP today often uses adapted Wi-Fi gear (HAMNET)
rather than classic packet, but the concept is the same: IP running *on top of* the radio
link.

## On XRouter

XRouter is unusual in having **its own multi-homed TCP/IP stack** alongside the host's, so it
can route IP-over-AX.25, run AMPRNet, build tunnels, and do NAT and RIP — whether or not you
run amateur IP. The AXIP/AXUDP tunnels above are also how XRouter nodes often link to one
another over the internet. See [The TCP/IP stack](../networking/tcpip.md).

---

**Sources:**
[AMPRNet (Wikipedia)](https://en.wikipedia.org/wiki/AMPRNet) ·
[AX.25 (Wikipedia)](https://en.wikipedia.org/wiki/AX.25) ·
[OARC packet history (G4KLX)](https://wiki.oarc.uk/packet:history) ·
[ARDC](https://www.ardc.net/)
