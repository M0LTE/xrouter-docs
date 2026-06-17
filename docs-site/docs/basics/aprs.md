# APRS in brief

APRS shares the same [AX.25](ax25.md) layer-2 as connected packet, but works in a completely
different style: short broadcasts rather than connections. This primer explains what APRS is,
how its WIDEn-N digipeating works, and what IGates and APRS-IS do, useful background because
XRouter includes an APRS digipeater and IGate.

## What APRS is

The **Automatic Packet Reporting System (APRS)** is an amateur system for real-time,
local-area exchange of information of immediate value: GPS positions, objects, weather
telemetry, short messages, bulletins and queries, typically plotted on a live map. It was
developed from the late 1980s by **Bob Bruninga WB4APR** at the US Naval Academy; the name
derives from his callsign.

## How it differs from connected packet

APRS is **connectionless**. It works entirely as a broadcast using **UI frames**, no
connections, no per-packet acknowledgement, no retransmission. Its reliability comes from
*repetition* rather than acknowledgement: a packet is rebroadcast by many digipeaters, so it
tends to be multiplied faster than it is lost. In its most common form APRS runs over AX.25
at 1200 baud Bell 202 AFSK on a single shared frequency per region (for example 144.390 MHz
in North America and 144.800 MHz across much of Europe, frequencies vary by country).

## WIDEn-N digipeating

Rather than naming specific digipeaters, APRS uses **generic, aliased paths**. Modern
practice is the **WIDEn-N** scheme: a typical mobile station uses a path of `WIDE1-1,WIDE2-1`.
Each qualifying digipeater repeats the packet and **decrements** the N counter; when it
reaches zero the packet stops, which caps how many hops it can take. "Smart" digipeaters also
remember recently seen packets and suppress duplicates, stopping loops. Because the channel
is shared, fixed stations should use a minimal path, or none, to avoid congesting it.

## IGates and APRS-IS

An **IGate (Internet Gateway)** bridges on-air APRS to the **APRS Internet System
(APRS-IS)**, a worldwide backbone. Packets received from RF are forwarded to APRS-IS
(duplicates discarded), where they become visible on databases and maps such as aprs.fi.
Some IGates also gate selected traffic, typically messages, from the internet back to RF
near the intended recipient.

## On XRouter

XRouter includes an APRS feature, so it helps to remember that APRS sits on the same AX.25
data link as "connected" packet but is a different, broadcast-style application with its own
paths and conventions. XRouter can act as an APRS digipeater and IGate; see
[APRS digipeater and IGate](../subsystems/aprs-igate.md).

---

**Sources:**
[APRS (Wikipedia)](https://en.wikipedia.org/wiki/Automatic_Packet_Reporting_System) ·
[aprs.org (Bob Bruninga)](http://www.aprs.org/) ·
[APRS-IS](http://www.aprs-is.net/) ·
[Dire Wolf](https://github.com/wb2osz/direwolf)
