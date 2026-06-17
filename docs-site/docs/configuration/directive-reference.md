# Directive reference

This page is a consolidated reference of the `XROUTER.CFG` configuration directives, grouped
by where they are used. It lists the directives documented in XRouter's in-program manual
(Section 7, "Configuration Directives") and on the author's mirror. It is a convenience index,
not a substitute for the manual: **defer to the in-program manual and the running program for
the authoritative current set of keywords and exact defaults.** Some defaults differ between
versions and between the global and per-port context, and where the sources disagree this page
says so rather than asserting a false precision.

For how the file is structured (blocks, comments, line length, ordering), see
[the configuration model](index.md) and [Global settings](global-settings.md). For the
keywords that define hardware and radio ports, see [Interfaces](interfaces.md) and
[Ports](ports.md). For the files these directives reference, see
[System and data files](system-files.md).

## How to read these tables

- Every directive takes the form `KEYWORD=value`, one per line. Keywords are **not
  case-sensitive**.
- **Scope** is where a directive is valid: **G** = GLOBAL section · **P** = inside a
  `PORT…ENDPORT` block · **I** = inside an `INTERFACE…ENDINTERFACE` block · **C** = inside a
  `CONSOLE…ENDCONSOLE` block · **A** = inside an `APPL…ENDAPPL` block · **R** = inside a
  `RADIO…ENDRADIO` block. Several keywords are valid both globally (where they set a default)
  and inside `PORT` (where they override per port); these show **G/P**.
- **Default** is the value the manual gives in parentheses, where one is documented. Where the
  sources conflict on the number, or it is version-dependent, the table reads
  *varies / see note* and the conflict is noted beneath.
- In the manual's own listings, mandatory keywords are flagged; the genuinely mandatory ones
  are `NODECALL`, `NODEALIAS`, per-port `ID` and `INTERFACENUM`, per-interface `TYPE` and
  `MTU`, `PROTOCOL` (most interface types) and `IPLINK` (AXIP/AXUDP ports). `COLS` is
  deprecated.

!!! note "Defaults can shift between versions"
    XRouter releases briskly and a handful of defaults have changed over the years (for
    example `CHATQUAL`, `RESPTIME` and the representation of `MAXTT`). Treat any number here as
    indicative and confirm against your build's manual (`MAN 7`) if it matters.

---

## Global — identity and location

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `NODECALL` | G | Primary AX.25 / NET-ROM callsign of the node (used for all L3/L4; default L2 per port). Mandatory | (none) |
| `NODEALIAS` | G | Primary AX.25 / NET-ROM alias of the node. Mandatory | (none) |
| `CONTACT` | G | Sysop contact details (sent to the mapping server) | (none) |
| `QTH` | G | Location of this node, free text | (empty) |
| `LOCATOR` | G | Maidenhead QTH locator (6 or 8 char) | (empty) |
| `LATITUDE` | G | Latitude, decimal degrees (−90..90) | 999 (unset) |
| `LONGITUDE` | G | Longitude, decimal degrees (−180..180) | 999 (unset) |
| `ALTITUDE` | G | Site altitude above mean sea level (metres) | 0 |
| `HAAT` | G | Antenna height above average terrain (metres) | 0 |
| `MAPCOMMENT` | G | Short text shown on the network map | (none) |
| `MAPSERVADDR` | G | Hostname/IP of the map server (override) | built-in |
| `MAPSERVPORT` | G | TCP port of the map server | 80 |

## Global — console and display

These control the local sysop console(s). Most colours are set inside a `CONSOLE` block; a few
have global equivalents. Colour names are taken verbatim from the manual.

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `NUMCONSOLES` | G | Number of consoles (0–5). The manual's keyword list misspells this `NUMCONOLES` | 3 |
| `ROWS` | G | Display height in rows | 25 |
| `COLS` | G | Display width — **deprecated** | 80 |
| `BOOTWIN` | G | Window shown after bootup | 6 (overview) |
| `BELL` | G | Allowed hours for console bells | 0800-2200 |
| `AUDIODEVICE` | G | Audio output device for sounds | PC speaker / `/dev/audio` (Pi) |
| `CONSOLECALL` | C | Callsign for console-originated connects | `NODECALL` |
| `CONSOLELANG` | G/C | Console language number | 0 (English) |
| `MMASK` | C | Protocols to trace (hex bitmask) | varies / see note |
| `MPORTS` | C | Ports to monitor by default | all |
| `REVIEW` | C | Scrollback lines | 400 |
| `ACTIONCOLOR` | G/C | Colour for pending actions/events | YELLOW |
| `CAPTIONCOLOR` | G/C | Colour for captions/headings | LIME |
| `PROMPTCOLOR` | G/C | Colour for prompts | varies / see note |
| `WARNCOLOR` | G/C | Colour for warnings/errors | CERISE |
| `TEXTCOLOR` | C | Colour for normal text | WHITE |
| `ECHOCOLOR` | C | Colour for echoed command text | YELLOW |
| `RXCOLOR` | C | Colour for RX tracing | LIME |
| `TXCOLOR` | C | Colour for TX tracing | PINK |
| `TOPWINBGCOLOR` | C | Status-line background | CYAN |
| `TOPWINTXTCOLOR` | C | Status-line text | WHITE |
| `BOTWINBGCOLOR` | C | Menu-bar background | CYAN |
| `BOTWINTXTCOLOR` | C | Menu-bar text | WHITE |
| `CMDWINBGCOLOR` | C | Command-line background | NAVY |
| `CMDWINTXTCOLOR` | C | Command-line text | YELLOW |
| `MIDWINBGCOLOR` | C | Central-window background | BLACK |
| `MIDWINTXTCOLOR` | C | Central-window text | WHITE |

!!! note "Colour defaults"
    The manual quotes `PROMPTCOLOR` as TURQUOISE in one place and *cyan* in the `XROUTER.CFG`
    keyword list, and `MMASK` as `3f8` / `03FF`. These are cosmetic; take them from your build.

## Global — languages

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `DEFAULTLANG` | G | Default language number for unassigned sessions | 0 |
| `CONSOLELANG` | G/C | Console language number (see console) | 0 |

## Global — NET-ROM and routing

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `MINQUAL` | G/P | Minimum quality to ADD a node to the nodes table | 10 |
| `QUALADJUST` | G | Derate NET-ROM quality by callsign (`default`, or call + 0–255) | 255 |
| `NODESINTERVAL` | G/P | Minutes between NODES broadcasts | 60 |
| `MAXHOPS` | G/P | Hop-count horizon (also a `ROUTE ADD` parameter) | 30 |
| `MAXTT` | G/P | Trip-time horizon (also a `ROUTE ADD` parameter) | varies / see note |
| `MAXNODES` | G | Maximum size of the nodes table | 200 |
| `MAXROUTES` | G | Maximum number of neighbour routes | 30 |
| `MAXCIRCUITS` | G | Maximum concurrent NET-ROM L4 circuits | 20 |
| `MAXSESSIONS` | G | Maximum simultaneous sessions | 20 |
| `OBSINIT` | G | NET-ROM obsolescence initial value | 5 |
| `OBSMIN` | G | NET-ROM obsolescence minimum to broadcast | 3 |
| `L3TTL` | G | NET-ROM Layer 3 time-to-live (hops) | 25 |
| `L4DELAY` | G | NET-ROM Layer 4 delay (s) | 3 |
| `L4RETRIES` | G | NET-ROM Layer 4 retries | 3 |
| `L4T3` | G | NET-ROM Layer 4 link-check interval (s) | 840 |
| `L4TIMEOUT` | G | NET-ROM Layer 4 timeout (s) | 120 |
| `L4WINDOW` | G | NET-ROM Layer 4 window | 10 |
| `L3RTTINTERVAL` | G | Seconds between L3RTT round-trip checks | 300 |
| `INPBCINTERVAL` | G | Seconds between scheduled INP3 unicasts | 600 |
| `L3EXCLUDE` | G | Callsigns whose L3 traffic is disrupted | (none) |
| `L3BUDLEVEL` | G | Leakage level (0–255) for `L3EXCLUDE` (a.k.a. `BLEVEL`) | 0 |
| `BLEVEL` | G | Leakage level (0–255) for `L3EXCLUDE` | 0 |
| `HIDENODES` | G | Visibility of `#` nodes | 0 |
| `SORTBYCALL` | G | Sort the nodes table by callsign rather than alias | 0 |
| `PROXY` | G/P | NET-ROM proxy / AX-IP proxy mapping (NetRom↔AX25 / NetRom↔TCP) | (none) |
| `ROUTES` | G block | Begins the locked-in NET-ROM routes section (terminated by `***`) | (none) |

!!! note "`MAXTT` units and default"
    The `XROUTER.CFG` keyword list gives `MAXTT` as 500 ("Maximum Trip Time in secs"); other
    pages reference a 0–60000 range where 60000 = 600 s, and the author's mirror lists a global
    default of 5000 (hundredths of a second). The representation differs by page — confirm
    against your build. See [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).

## Global — TCP/IP stack

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `IPADDRESS` | G/P | Core (AMPRNet/44-net) IP, inherited by ports; per-port override; `0.0.0.0` disables all IP | 0.0.0.0 |
| `NETMASK` | P | Subnet mask for the port | (none) |
| `DHCP` | P | Obtain the port IP via DHCP (1) or static (0) | 0 |
| `DNS` | G | DNS server to use | internal |
| `DOMAIN` | G | Domain suffix | `ampr.org.` |
| `DCACHE` | G | Domain cache size | 10 |
| `HOSTNAME` | G | Host name for TCP/IP operations | `NODEALIAS:NODECALL` |
| `MAXARP` | G | Maximum ARP table size | 20 |
| `MAXTCP` | G | Maximum simultaneous TCP connections | 20 |
| `IPENCAP` | G | Enable IPENCAP (protocol 4) via the Linux stack | 0 |
| `IPIP` | G | Enable IPIP (protocol 94) via the Linux stack | 0 |
| `IPTTL` | G | IP time-to-live | 255 |
| `IPUDPPORT` | G | UDP port for receiving IPUDP traffic | 94 |
| `SYNCACHELIFE` | G | TCP SYN cache lifetime (s) | 10 |
| `SYNCACHESIZE` | G | TCP SYN cache slots | 1000 |

## Global — built-in TCP/IP server ports

Each of these sets the listening TCP/UDP port of a built-in server. See
[Ports and service numbers](../reference/ports-and-services.md) and
[Built-in TCP servers](../subsystems/built-in-servers.md) for the full service map.

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `TELNETPORT` | G | Telnet server | 23 |
| `TELPROXYPORT` | G | Telnet proxy server | 2323 |
| `TTYLINKPORT` | G | TTYLINK (keyboard-to-keyboard) | 87 |
| `RLOGINPORT` | G | Rlogin server (sysop access) | 513 |
| `FTPPORT` | G | FTP server | 21 |
| `FINGERPORT` | G | Finger server | 79 |
| `HTTPPORT` | G | HTTP server | 80 |
| `HTTPROOT` | G | HTTP server root directory | `HTTP` |
| `ECHOPORT` | G | Echo server | 7 |
| `DISCARDPORT` | G | Discard server | 9 |
| `SOCKSPORT` | G | SOCKS proxy | 1080 |
| `APRSPORT` | G | APRS server | 1448 |
| `CHATPORT` | G | Chat server | 3600 |
| `MQTTPORT` | G | MQTT broker | 1883 |
| `AGWPORT` | G | AGWPE emulator | 8000 |
| `RHPPORT` | G | Remote Host Protocol (host API) server | 9000 |
| `HAMLIBPORT` | G | Hamlib emulator port | (unset) |
| `RIGSRVPORT` | G | Radio control server port | (unset) |
| `POP3PORT` | G | POP3 server (default unverified in the manual) | 110 |

## Global — chat, PMS, blog and wall

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `CHATCALL` | G/P | Chat server callsign (SSID −8 recommended) | (none) |
| `CHATALIAS` | G/P | Chat server alias (end in "CHT"; ≤6 chars) | (none) |
| `CHATQUAL` | G | Chat server NET-ROM quality (0 = invisible) | varies / see note |
| `CHATLOG` | G | Chat-activity logging bitmask | 0 |
| `CHATLINKS` | G | Callsigns of linked chat-server peers | (none) |
| `PMSCALL` | G/P | Mailbox (PMS) callsign (omit ⇒ PMS disabled) | (none) |
| `PMSALIAS` | G/P | Mailbox alias | (none) |
| `PMSHADDR` | G | Mailbox hierarchical address | (none) |
| `PMSQUAL` | G | PMS NET-ROM quality to broadcast (0 = suppress) | 0 |
| `PMSTYPE` | G | Mailbox type/mode (0–4; 1 = standard PMS, 3 = BBS-as-PMS) | 1 |
| `KILONFWD` | G | Kill a PMS message after forwarding | 0 |
| `BLOGFLAGS` | G | Sysop blog options bitmask (1/2/4/8) | omitted = disabled |
| `WALLFLAGS` | G | Message wall / guestbook options bitmask | omitted = disabled |

!!! note "`CHATQUAL` default"
    The author's mirror gives `CHATQUAL` as 255 in detail ("visible over the same distance as
    `NODECALL`") but 150 in an overview table. 255 is the likely current default; confirm
    against your build. See [Chat server](../subsystems/chat.md).

## Global — APRS and IGate identity

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `APRSCALL` | G | Callsign used by the IGate | `NODECALL` |
| `UITRACE` | G | APRS N-n digipeating alias (TRACE) | varies / see note |
| `UIFLOOD` | G | APRS N-n digipeating alias (WIDE/flood) | WIDE |
| `DXFLAGS` | G | DX-list display control flags | 0 |

!!! note "`UITRACE` / `UIFLOOD`"
    The two sources give the defaults slightly differently (one lists `UITRACE`=TRACE,
    `UIFLOOD`=WIDE; the other treats `UITRACE`=WIDE for the New-N paradigm). Set these
    explicitly for your region. See [APRS digipeater and IGate](../subsystems/aprs-igate.md).

## Global — weather, DynDNS, MQTT publisher, logging

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `WXFILE` | G | Weather import file name/path | (none) |
| `WXMAXAGE` | G | Maximum age of a weather record (s) | 21600 (6 h) |
| `WXMAXRECS` | G | Maximum weather records stored (0–255) | 5 |
| `MQTTSERVADDR` | G | External MQTT broker host/IP (absent ⇒ publisher off) | (none) |
| `MQTTSERVPORT` | G | External MQTT broker port | 1883 |
| `LOG` | G | Activity-logging options bitmask (0 = none) | 0 |
| `IGATE` | G | Whether the IGate daemon starts at bootup | 0 |
| `IDLETIME` | G | Idle-link shutdown time (s) | 900 |
| `CTRLADDR` | G | Hex address of the hardware control port (not functional on Pi/Linux) | 378 |
| `COMMAND` | G | Creates a custom single-word command alias | (none) |
| `ENABLE_LINKED` | G | Controls who may use `*** LINKED AS` (Y/A/N) | varies / see note |

## Global — connect text and access defaults

These set defaults that can be overridden per port (next section).

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `CTEXT` | G/P | Connect text (global multi-line block ending `***`, or per-port one line / filename) | (none) |
| `CTFLAGS` | G/P | Which callers receive `CTEXT` (1 = alias, 2 = call, …) | 9 |
| `INFOTEXT` | G | Text for the `I` / INFO command (multi-line block ending `***`) | (none) |
| `INFOMSG` | G | Text for the INFO command | (none) |
| `EXCLUDE` | G/P | AX.25 L2 callsign blacklist | (none) |
| `DEFAULTLANG` | G | Default language number (see Languages) | 0 |

---

## INTERFACE block

Defined inside `INTERFACE=n … ENDINTERFACE`, in the GLOBAL section. `TYPE` and `MTU` are
mandatory; `PROTOCOL` is required for most types. Several keywords are *overloaded* — their
meaning depends on the selected `TYPE`. See [Interfaces](interfaces.md) for the per-type detail.

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `TYPE` | I | Hardware/driver type (`AGW`, `ASYNC`, `AXIP`, `AXTCP`, `AXUDP`, `EXTERNAL`, `LOOPBACK`, `TCP`, `TUN`, `UDP`, `YAM`). Mandatory | (none) |
| `MTU` | I | Maximum Transmission Unit (≤1500; ≤256 recommended on AX.25). Mandatory | (none) |
| `PROTOCOL` | I | Protocol on this interface (`ASCII`, `AX25`, `AXIP`, `AXTCP`, `AXUDP`, `DEDHOST`, `ETHER`, `HDLC`, `IP`, `KISS`, `MODEM`, `NETROM`, `NONE`, `PPP`, `SLIP`, `TNC`, `TNC2`) | (none) |
| `COM` | I | Serial device (ASYNC/YAM, e.g. `/dev/ttyUSB0`); tunnel name (TUN); remote UDP port (UDP); `COM=0` parks the interface | (none) |
| `SPEED` | I | Serial (ASYNC) or RF (SCC) baud rate, no comma | (none) |
| `FLOW` | I | Flow control (ASYNC): 0 none / 1 hardware RTS-CTS / 2 software XON-XOFF / 3 both | (none) |
| `ID` | I | Interface identification string (also device name, e.g. `eth0`, for EXTERNAL) | (none) |
| `CONFIG` | I | Hardware/type-specific config (e.g. AGWPE password, AXTCP client list) | (none) |
| `KISSOPTIONS` | I | KISS options: `NONE`, `POLLED`, `CHECKSUM`, `ACKMODE`, `SLAVE`, `NOPARMS` | NONE |
| `CHANNEL` | I | Channel on multi-channel hardware (SCC; SCC unsupported on Pi) | (none) |
| `CHANNELS` | I | Number of TNC channels (DEDHOST/WA8DED only; 1–32) | (none) |
| `APPLNUM` | I | Application number (DEDHOST/WA8DED hostmode only; matches an `APPL` block) | (none) |
| `INTNUM` | I | Overloaded: AGWPE TCP port / AXTCP/TCP listen port / UDP local port | (none) |
| `IOADDR` | I | Overloaded: target IP for AGW/TCP/UDP | 127.0.0.1 |
| `ETHADDR` | I | Ethernet address (NDISXPKT/XR32 only; legacy) | (none) |
| `RADIO` | I | Associates a `RADIO` block with the interface | (none) |
| `ENDINTERFACE` | I | Ends the interface block. Mandatory | — |

---

## PORT block

Defined inside `PORT=n … ENDPORT`, in the GLOBAL section, **after** the interface it binds to.
`ID`, `INTERFACENUM` and `ENDPORT` are mandatory. Many keywords mirror a global default and
override it for this port only.

### Port — identity and binding

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `ID` | P | Port identification text shown on the PORTS display. Mandatory | (none) |
| `INTERFACENUM` | P | Interface number this port binds to. Mandatory | (none) |
| `CHANNEL` | P | Channel (A–P) on a multi-channel interface | A |
| `PORTCALL` | P | Additional/override callsign for the port | (none) |
| `PORTALIAS` | P | Additional/override alias for the port | (none) |
| `PORTALIAS2` | P | Secondary alias (digipeating only) | (none) |
| `CHATCALL` | P | Per-port override of global `CHATCALL` | (none) |
| `CHATALIAS` | P | Per-port override of global `CHATALIAS` | (none) |
| `PMSCALL` | P | Per-port override of global `PMSCALL` | (none) |
| `PMSALIAS` | P | Per-port override of global `PMSALIAS` | (none) |

### Port — AX.25 and link timing

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `PACLEN` | G/P | Maximum packet length; global value sets the NET-ROM L3 default (max 256) | 120 |
| `MAXFRAME` | P | Max unacked AX.25 L2 frames (>7 enables Modulo-128) | 3 |
| `FRACK` | P | AX.25 T1 frame-ack timeout (ms) | 7000 |
| `RESPTIME` | P | AX.25 delayed-ack timer (ms) | varies / see note |
| `RETRIES` | P | Max connect/disconnect/resend attempts | 10 |
| `PERSIST` | P | CSMA probability to transmit (0–255) | 64 |
| `SLOTTIME` | P | CSMA slot interval (ms) | 100 |
| `TXDELAY` | P | PTT-to-data delay (ms) | 300 |
| `TXTAIL` | P | Data-end-to-PTT-drop delay (ms) | 100 |
| `T3` | G | AX.25 link-check timer (s) | 180 |
| `MAXLINKS` | G | Max simultaneous AX.25 L2 links | 30 |
| `SESSLIMIT` | G/P | Max sessions per user (G) / per port (P) | varies / see note |
| `USERS` | P | Max simultaneous users on the port | 255 |
| `CFLAGS` | P | Connection-control flags (1 = uplink, 2 = downlink, 4 = apps unconditionally, 8 = suppress L3RTT, 16 = allow L2 frag) | 3 |
| `RFBAUDS` | P | Over-the-air baud rate | 1200 |
| `KEEPALIVE` | P | Link keepalive (set via the live command; see [command reference](../commands/command-reference.md)) | (per port) |
| `INITSTR` | P | Modem init string (modem ports) | (none) |

!!! note "`RESPTIME` and `SESSLIMIT`"
    `RESPTIME` is documented as 1500 ms in the directive section and 2000 ms in the
    `XROUTER.CFG` keyword list. The global `SESSLIMIT` default is given as both 32767
    ("unrestricted") and, in worked examples, 10; the per-port default is 255. Use whichever
    your build reports.

### Port — SCC-only (inert on Pi/Linux)

The Pi and Linux builds have no SCC card slot, so these keywords are accepted but inert there.

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `FULLDUP` | P | Full duplex (SCC only) | 0 |
| `SOFTDCD` | P | Software DCD (SCC only) | 0 |
| `INTERLOCK` | P | TX interlock group (SCC; non-zero shares; won't TX together) | 0 |
| `CWID` | P | CW Morse ID every 30 min (SCC only) | (off) |
| `FEC` | P | Reed-Solomon FEC (needs FEC-capable KISS/SCC/YAM) | 0 |

### Port — NET-ROM and routing (per port)

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `QUALITY` | P | Default NET-ROM neighbour quality on the port (0 disables L3/L4 on the port; 256–511 enables AUTOQUAL) | 10 |
| `MINQUAL` | G/P | Min quality to add a node to the table (per-port override) | 10 |
| `MINTXQUAL` | P | Min quality to broadcast a node | 0 |
| `NODESINTERVAL` | G/P | Minutes between NODES broadcasts (per-port override) | 60 |
| `MAXHOPS` | G/P | Hop-count horizon (per-port override) | 30 |
| `MAXTT` | G/P | Trip-time horizon (per-port override) | varies / see note |
| `PROXY` | G/P | NET-ROM systems to tunnel AX.25 to (per-port override) | (none) |

### Port — IP (per port)

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `IPADDRESS` | G/P | Per-port IP address override (must differ from the Linux IP on EXTERNAL) | (inherits) |
| `NETMASK` | P | Subnet mask used with `IPADDRESS` | (none) |
| `DHCP` | P | Enable (1) / disable (0) the DHCP client | 0 |
| `DYNDNS` | P | Enable/disable the Dynamic DNS update client | (per page) |
| `IPLINK` | P | IP address / hostname of the AXIP/AXUDP peer. Mandatory on AXIP/AXUDP ports | (none) |
| `UDPLOCAL` | P | RX UDP port for AXUDP operations | 93 |
| `UDPREMOTE` | P | TX UDP port / partner's AXUDP RX port | 93 |

### Port — digipeating, beacons and APRS

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `DIGIFLAG` | P | Digipeat options bitmask (1 = UI, 2 = non-UI, 4 = RELAY, 8 = TRACE, 16 = WIDE, 32 = APRS 3rd-party via L4, 64 = to Internet, 128 = from Internet, 256 = UITRACE, 512 = UIFLOOD) | 7 |
| `DIGIPORT` | P | Destination port for digipeated frames | 0 (this port) |
| `TXPORT` | P | Port on which to transmit if not this one | 0 |
| `IDTEXT` | G/P | Single ID-beacon line broadcast every `IDINTERVAL` | (none) |
| `IDINTERVAL` | G | Minutes between `IDTEXT` beacons (0 disables) | 15 |
| `IDPATH` | P | AX.25 destination + digi path for ID beacons | "ID" |
| `UNPROTO` | P | Destination/digi path for unproto (UI) broadcasts | (none) |
| `BCAST` | P | AX.25 destination addresses for UI broadcasting | (none) |
| `BCFROM` | P | Callsigns allowed to use UI broadcast | (unrestricted if absent) |
| `APRSPATH` | P | Default digi path for APRS frames | (none) |
| `FREQUENCY` | P | Radio frequency (Hz) | 0 |

### Port — MHeard and frame pipes

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `MHEARD` | P | Enable MHeard and its size on the port (0–50) | 15 |
| `MHFLAGS` | P | MHeard content flags bitmask (1 = direct, 2 = direct digis, 4 = digipeated) | 255 |
| `PIPE` | P | Frame pipe to another port (optional callsign filter) | (none) |
| `PIPEFLAG` | P | Frame-pipe option flags bitmask | 3 |

### Port — access and applications

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `APPLMASK` | P | Which hosted apps are AX.25-L2 connectable on the port (bitmask) | 255 |
| `EXCLUDE` | G/P | Callsigns not allowed to connect (blacklist) | (none) |
| `VALIDCALLS` | P | Whitelist — accept only these callsigns | (none) |
| `SYSOP` | P | If 1, all users on this port get sysop status with **no** password challenge — secure links only | 0 |
| `CTEXT` | G/P | Per-port connect text (one line or filename) | (none) |
| `CTFLAGS` | G/P | Per-port connect-text control flags | 9 |

!!! danger "`SYSOP=1` grants full control with no challenge"
    Setting `SYSOP=1` on a port gives every connecting user full sysop privileges without a
    password. The manual restricts this to genuinely secure links (RS232, Ethernet); the
    default is 0. See [Security and hardening](../admin/security-hardening.md).

---

## APPL block

Defined inside `APPL=n … ENDAPPL`. Describes a hosted application (e.g. a BBS reached through
the node).

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `APPLNAME` | A | Application name | (none) |
| `APPLCALL` | A | Application callsign | (none) |
| `APPLALIAS` | A | Application alias | (none) |
| `APPLQUAL` | A | Application NET-ROM quality to broadcast (non-zero ⇒ L4-connectable and in NODES) | 150 |
| `APPLFLAGS` | A | Flags controlling the application | (none) |
| `APPLTYPE` | A | Application type (`=TCP[,ip:]port`) | (none) |
| `APPLNUM` | A | Application number (matches a host-mode interface) | (none) |
| `ENDAPPL` | A | Ends the application block. Mandatory | — |

---

## RADIO block

Defined inside `RADIO=n … ENDRADIO`. Describes a CAT-controlled radio.

| Directive | Scope | Purpose | Default |
| --- | --- | --- | --- |
| `NAME` | R | Radio name | (none) |
| `COM` | R | TTY device or `ip:port` for radio control | (none) |
| `BAUDS` | R | Control-port baud rate | (none) |
| `STOPBITS` | R | Control-port stop bits | (none) |
| `TYPE` | R | Radio type | (none) |
| `MODE` | R | Operating mode | (none) |
| `FREQUENCY` | R | Receive frequency | (none) |
| `TXFREQ` | R | Transmit frequency | (none) |
| `OFFSET` | R | Frequency offset | (none) |
| `SQUELCH` | R | Squelch level | (none) |
| `VOLUME` | R | Volume level | (none) |
| `PTTMETHOD` | R | PTT keying method | (none) |
| `RXAUDIODEV` | R | Receive audio device | (none) |
| `ENDRADIO` | R | Ends the radio block | — |

---

!!! note "Typos in the manual's keyword list"
    The wiki's `XROUTER.CFG` keyword listing reproduces several source typos verbatim, including
    `NUMCONOLES` (for `NUMCONSOLES`), `Aplication`, `protcol`, `coonfig`, `qulity` and
    `squuelch`. Use the corrected spellings shown above; expect the misspellings if you grep the
    raw manual.

---

**Sources:** In-program manual mirrored on the OARC wiki —
[Section 7, Configuration Directives (`man7`)](https://wiki.oarc.uk/packet:xrouter:docs:man7) and
[`XROUTER.CFG` keyword list, Section 8 (`man8`)](https://wiki.oarc.uk/packet:xrouter:docs:man8) ·
[ohiopacket.org XRPi mirror — `xrcfg.htm` (global keywords), `ports.htm`, `ifaces.htm`](https://ohiopacket.org/xrpi/docs/xrcfg.htm)
