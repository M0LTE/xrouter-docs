# Worked example configs

This page collects complete, annotated `XROUTER.CFG` examples for the common ways a node gets
on the air. They are written from scratch to be readable starting points, not copies of any
running station, substitute your own callsigns, devices and addresses throughout. Each one
obeys the rules from the [configuration model](index.md): identity in the GLOBAL section,
interfaces before the ports that use them, and a valid `IPADDRESS`.

!!! tip "Edit incrementally"
    XRouter reports configuration errors with a line number and reads the file once at
    startup. Change one thing, restart, check, repeat, it is far easier than writing a large
    file and debugging it all at once.

## 1. Soundcard-modem (KISS) RF node

A single VHF channel through a soundcard modem, Direwolf, UZ7HO SoundModem or QtSoundModem,
configured to present a KISS interface on a serial device or pseudo-TTY. This is the typical
"no hardware TNC" station.

```ini
; ---- Identity -------------------------------------------------------------
NODECALL=M0XXX-1            ; node callsign + SSID
NODEALIAS=MYNODE           ; alias, up to 6 chars, no SSID
QTH=Anytown
LOCATOR=IO92AB
IPADDRESS=10.1.1.1         ; private dummy so IP services work (see warning below)

; ---- Connect text ---------------------------------------------------------
CTEXT
Welcome to MYNODE. Type ? for commands, I for info.
***

; ---- Interface 1: soundcard modem as a KISS TNC ---------------------------
INTERFACE=1
        TYPE=ASYNC          ; the modem presents a serial / pseudo-TTY KISS port
        PROTOCOL=KISS
        COM=/dev/ttyACM0    ; the device your modem exposes
        SPEED=57600         ; host-side speed, not the RF baud rate
        FLOW=0              ; never use XON/XOFF flow control with KISS
        KISSOPTIONS=ACKMODE ; modem tells XRouter when a frame has gone out
        MTU=256
ENDINTERFACE

; ---- Port 1: the radio channel -------------------------------------------
PORT=1
        ID=144.950 MHz 1200 baud
        INTERFACENUM=1      ; binds to INTERFACE 1 above
        PACLEN=200
        MAXFRAME=4
        QUALITY=192         ; advertise this port as a NET/ROM neighbour
ENDPORT
```

Key points: the modem is an `ASYNC` + `KISS` interface (the audio side is the modem
application's job, not XRouter's); `FLOW=0` because KISS and XON/XOFF do not mix; and `SPEED`
here is the *host* link speed to the modem, not the over-the-air baud rate. See [Soundcard
modems](../interfaces/soundcard-modems.md) for the modem-side setup.

## 2. Serial KISS TNC / NinoTNC node

A hardware KISS TNC, including a NinoTNC, which appears as a USB-serial device, on one
channel. Almost identical to the soundcard case, because both speak KISS over a serial port;
only the device and speed differ.

```ini
; ---- Identity -------------------------------------------------------------
NODECALL=M0XXX-1
NODEALIAS=MYNODE
QTH=Anytown
LOCATOR=IO92AB
IPADDRESS=10.1.1.1

; ---- Interface 1: hardware KISS TNC --------------------------------------
INTERFACE=1
        TYPE=ASYNC
        PROTOCOL=KISS
        COM=/dev/ttyUSB0    ; or /dev/ttyACM0 for a NinoTNC
        SPEED=57600         ; match the TNC's serial speed
        FLOW=0
        KISSOPTIONS=NONE    ; plain KISS; most TNCs use this
        MTU=256
ENDINTERFACE

; ---- Port 1: the radio channel -------------------------------------------
PORT=1
        ID=145.825 MHz 1200 baud
        INTERFACENUM=1
        PACLEN=200
        MAXFRAME=4
        FRACK=7000          ; the 1200-baud default; lower only on fast links
        QUALITY=192
ENDPORT
```

Key points: `KISSOPTIONS=NONE` is plain KISS, which suits most TNCs; raise `MAXFRAME` above 7
only on a clean channel (it would switch on modulo-128). A BPQ-KISS EPROM is the exception,
it needs `KISSOPTIONS=POLLED,CHECKSUM`. See [KISS TNCs](../interfaces/kiss-tncs.md).

## 3. Internet AXUDP link to a partner node

Linking to another node over the internet, with no radio involved. AXUDP carries AX.25 inside
UDP datagrams; you and your partner agree on UDP ports and each points at the other's address.

!!! danger "IPADDRESS is required for any internet link"
    AXUDP, AXIP and AXTCP are all IP services, so they are **disabled entirely** if the global
    `IPADDRESS` is `0.0.0.0` or undefined, XRouter switches off all IP activity as a
    deliberate security measure. Set a real 44Net address, or a private dummy such as
    `10.1.1.1`, before an internet link will work., *Paula G8PZT,
    [groups.io](https://groups.io/g/xrouter/message/1957)*

```ini
; ---- Identity -------------------------------------------------------------
NODECALL=M0XXX-1
NODEALIAS=MYNODE
IPADDRESS=10.1.1.1         ; MUST be set, or all IP links are disabled

; ---- Interface 2: AXUDP transport ----------------------------------------
INTERFACE=2
        TYPE=AXUDP
        MTU=256
ENDINTERFACE

; ---- Port 2: the link to the partner node --------------------------------
PORT=2
        ID=AXUDP link to PARTNER
        INTERFACENUM=2
        IPLINK=partner.example.org   ; partner's hostname or public IP
        UDPLOCAL=93                  ; UDP port XRouter listens on
        UDPREMOTE=93                 ; partner's AXUDP receive port
        FRACK=2000                   ; wired link: a shorter T1 is fine
        RESPTIME=200                 ; and a short delayed-ack timer
        QUALITY=200
ENDPORT
```

Key points: the `AXUDP` interface needs only `TYPE` and `MTU`; the partner details live on the
*port* via `IPLINK`, `UDPLOCAL` and `UDPREMOTE`. Because the path is wired, a lower `FRACK` and
`RESPTIME` than an RF channel are appropriate. For raw-IP (AXIP) and TCP (AXTCP) variants, and
for sharing one public IP across several partners, see [AXIP / AXUDP /
AXTCP](../interfaces/axip-axudp-axtcp.md).

## 4. Adding a LAN / Ethernet port

A LAN port gives XRouter its own address on your home network, useful for IP routing between
radio ports and the LAN, and for reaching the node's web and Telnet servers from another
machine. This snippet adds an Ethernet interface and port to any of the configs above; the
`EXTERNAL` interface's IP address must differ from the host Linux box's own IP.

```ini
; ---- Interface 8: Ethernet / WiFi ----------------------------------------
INTERFACE=8
        TYPE=EXTERNAL       ; "external" is historical; no extra driver needed
        ID=eth0             ; the Linux adaptor name (eth0, wlan0, ...)
        PROTOCOL=ETHER
        MTU=1064
ENDINTERFACE

; ---- Port 8: the LAN side -------------------------------------------------
PORT=8
        ID=Ethernet LAN
        INTERFACENUM=8
        IPADDRESS=192.168.1.50   ; XRouter's own LAN address, NOT the host's IP
        NETMASK=255.255.255.0
ENDPORT
```

!!! note "Capabilities for Ethernet and low ports"
    The `EXTERNAL` interface uses raw sockets, and binding service ports below 1024 needs
    privilege. Rather than running as root, grant the binary the right Linux capabilities
    once with `setcap`, see [Raspberry Pi](../getting-started/raspberry-pi.md) and [Security
    and hardening](../admin/security-hardening.md).

If you only need to reach IP services *from* the LAN (not route between the LAN and radio
ports), you may not need an `EXTERNAL` port at all, the Linux kernel stack can carry it. The
trade-offs between the two approaches are covered on [The TCP/IP
stack](../networking/tcpip.md).

## Putting it together

A real node usually combines several of these: one or more RF ports, perhaps an internet link
to a neighbour, and a LAN port for administration. Stack the blocks in one `XROUTER.CFG`,
GLOBAL identity at the top, then each `INTERFACE` followed (anywhere below it) by its
`PORT`(s). Give each interface and port a unique number, and keep your descriptive `ID`
strings meaningful, because that is what you will see on the `PORTS` display when something
needs attention.

---

**Sources:**
[Installation and configuration topics, OARC wiki (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6) ·
[Configuration directives, OARC wiki (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[Miscellaneous topics, OARC wiki (man9)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[IPADDRESS guidance, groups.io](https://groups.io/g/xrouter/message/1957) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/docs/config02.htm)
