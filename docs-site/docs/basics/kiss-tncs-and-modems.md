# KISS, TNCs and soundcard modems

Between your radio and the software that speaks [AX.25](ax25.md) sits a modem — and a thin
protocol called KISS that carries frames between the two. This page explains what a TNC is,
what KISS does, and how today's software "soundcard" modems compare with traditional
hardware TNCs.

## What a TNC is

A **Terminal Node Controller (TNC)** is the device between the radio and the computer.
Traditionally it combined a microprocessor running an AX.25 implementation in firmware with
a modem, so that a plain terminal plus a radio could get on packet. Early standardised TNCs
came from VADCG and then TAPR (in kit form, 1983); the **TNC-2** and its clones became a
de-facto standard.

## What KISS does

**KISS** ("Keep It Simple, Stupid") is a minimal framing protocol for the link between a host
computer and a TNC. It was introduced by Mike Cheponis K3MC and Phil Karn KA9Q in 1987 to
carry AX.25 frames to and from the host, so that the host runs the intelligence (the AX.25
or IP stack) and the TNC becomes a "dumb" modem that simply turns bytes into on-air signals
and back. A few points matter for newcomers:

- KISS is **not part of AX.25** and is **never sent over the air**. It only wraps frames on
  the wire between computer and TNC.
- Its framing comes from SLIP: frames are delimited by a special byte, with in-band
  occurrences of that byte escaped.
- KISS can set a few common TNC parameters (such as transmit delay and the CSMA persistence
  settings) and addresses up to 16 ports on a multi-port TNC, but it has no flow control or
  error handling of its own.

### Serial and over TCP

KISS is just a byte stream, so it can travel over different transports:

- **KISS over a serial port** (RS-232, USB serial or Bluetooth) is the classic way to reach
  a hardware TNC.
- **KISS over TCP** runs the same byte stream over a TCP socket, so node software can talk to
  a software modem or to a TNC on another machine. This is the usual way XRouter and similar
  programs connect to a soundcard modem such as Direwolf.

## Hardware TNCs vs soundcard modems

Hardware TNCs are still made and used — a notable modern example is the FEC-capable
**NinoTNC**, popular for building point-to-point node links. But much of today's practice
replaces the hardware TNC with **software plus a sound card**, letting the computer's own
processing do the modulation and demodulation:

- **Dire Wolf (Direwolf)**, by John Langner WB2OSZ, is a free, open-source software TNC and
  APRS encoder/decoder. It implements AX.25 v2.2 plus FX.25 and IL2P, can act as an APRS
  tracker, digipeater and IGate, and presents a KISS interface over TCP, serial or
  Bluetooth (as well as the AGW interface) for clients including XRouter.
- **UZ7HO SoundModem** is a widely used Windows software modem, and **QtSoundModem** is a
  cross-platform relative. Both present KISS/AGW interfaces in the same way.

### The role of the sound card

With a software modem, **the sound card is the modem**. Receive audio from the radio goes
into the sound input and is demodulated in software; transmit audio is generated in software
and sent to the radio's microphone input, with PTT keyed over a serial, CAT or GPIO line (or
by VOX). Audio-and-PTT interfaces such as the SignaLink or DRAWS simply package that wiring
tidily. This approach is cheaper than 1980s hardware and often outperforms it.

## On XRouter

XRouter connects to all of these. For hardware connections see
[KISS TNCs](../interfaces/kiss-tncs.md); for software modems and the sound-card path see
[soundcard modems](../interfaces/soundcard-modems.md).

---

**Sources:**
[KISS (Wikipedia)](https://en.wikipedia.org/wiki/KISS_(TNC)) ·
[Chepponis & Karn, "The KISS TNC" (1987)](http://www.ka9q.net/papers/kiss.html) ·
[Dire Wolf](https://github.com/wb2osz/direwolf) ·
[Hibby's Packet Radio Guide — modems](https://guide.hibbian.org/modems/)
