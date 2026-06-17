# What is packet radio?

This page is a plain-language introduction for newcomers: what packet radio is, where
it came from, the bands and speeds you will meet, and why it is still worth doing today.
It sets up the protocols, AX.25, NET/ROM, that XRouter implements.

**Packet radio** applies the ideas of packet switching, the same family of techniques
that underpins the internet, to digital communication over amateur radio. Your data is
broken into **packets**, each one carrying a source and destination address, and packets
bound for different stations share a single radio channel. A packet can be addressed to
everyone, or stored and forwarded onward by an intermediate **node** until it reaches its
destination. In amateur radio the dominant protocol is **AX.25** (Amateur X.25), and every
AX.25 packet carries the sender's callsign, which conveniently satisfies the licence
requirement to identify your station.

## A short history

Packet's roots are in 1970s research: the University of Hawaii's **ALOHAnet** pioneered
shared-channel access, and DARPA's **PRNET** in the San Francisco Bay area was an early
packet-radio network. Amateurs followed soon after. Montreal operators began experimenting
with data over VHF in 1978, and Canadian authorities permitted amateur packet on 220 MHz
from May that year. In 1980 the Vancouver Area Digital Communications Group started
producing standardised **Terminal Node Controllers (TNCs)** in quantity, and the FCC
authorised US amateurs to transmit ASCII. **Tucson Amateur Packet Radio (TAPR)** formed in
the early 1980s and offered a landmark TNC in kit form; packet then boomed across North
America, with packet bulletin board systems appearing from around 1984.

## Bands and radios

Most packet activity is on VHF and UHF:

- **2 m (144–148 MHz)** is the classic and most common packet band.
- **70 cm (430/440 MHz)** is also widely used.

The traditional newcomer setup is 1200 baud on 2 m through an ordinary narrow-band FM voice
radio. HF packet exists too, at 300 baud over SSB, but is far less common. Exact
frequencies and band plans vary by country and IARU region, so check your local
arrangements.

## The two speeds you will meet

Two on-air speeds dominate VHF/UHF packet:

- **1200 baud AFSK (Bell 202).** Audio tones of 1200 Hz and 2200 Hz are fed through a voice
  FM radio's microphone and speaker connections. It is simple, ubiquitous and slow.
- **9600 baud G3RUH FSK.** Developed by James Miller G3RUH, this feeds the modem signal
  directly to the radio's modulator and discriminator rather than through the audio path.
  It needs a "9600-capable" or modified radio, but fits 9600 baud into a standard channel.

Higher and experimental rates exist, and newer schemes add forward error correction (FX.25,
IL2P), but those two speeds remain the staples.

## Why it still matters

Packet is far from a museum piece. It carries keyboard-to-keyboard chat, mail and bulletin
boards, DX clusters, emergency traffic and APRS, and it is a rewarding hobby in itself:
you can build nodes, links and whole networks that you control entirely, with no reliance on
the internet. Cheap radios, Raspberry Pi-class computers, software modems such as Direwolf
and modern FEC-capable hardware have all helped a noticeable resurgence.

## Where to go next

The rest of these primers build up the picture: [AX.25](ax25.md) is the data-link layer
that moves packets between stations; [NET/ROM](netrom.md) adds automatic routing across many
nodes. XRouter is software that implements both, turning a computer and one or more radios
into a packet [node](nodes-and-sysops.md).

---

**Sources:**
[Packet radio (Wikipedia)](https://en.wikipedia.org/wiki/Packet_radio) ·
[AX.25 (Wikipedia)](https://en.wikipedia.org/wiki/AX.25) ·
[OARC packet history (G4KLX)](https://wiki.oarc.uk/packet:history) ·
[Hibby's Packet Radio Guide](https://guide.hibbian.org/)
