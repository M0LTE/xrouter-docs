# System and data files

XRouter reads and writes a family of configuration and data files — around forty of them —
all living in (or beneath) the working directory that holds the executable. Only two are
mandatory: the program itself and `XROUTER.CFG`. Everything else is optional, and the system
runs in a restricted mode without them. This page is a reference to the files named in the
manual's "Configuration and System Files" section.

Most of these are read **only at bootup**; a few are reloaded by a live command. The mailbox
(PMS) control files live in the `PMS` subdirectory rather than the working directory.

!!! warning "On Linux these names are UPPER CASE and case-sensitive"
    Linux filesystems are case-sensitive, and XRouter looks for its files in **UPPER CASE**.
    `IPROUTE.SYS` and `iproute.sys` are different files, and XRouter only reads the former.
    Getting the case wrong is the single most common reason an expected file is silently
    ignored. The executable itself is the exception — on the Pi build it is the lowercase
    `xrpi`.

## How to read this table

- **File** — the exact (upper-case) name. A trailing `.n` (e.g. `PPPHOST.n`) means one file
  per port, with `n` replaced by the port number.
- **Notes** lists where the file lives if not the working directory, and cross-references the
  page that explains it.

## Core configuration

| File | Purpose | Notes |
| --- | --- | --- |
| `XROUTER.CFG` | **Main configuration file (mandatory).** `KEYWORD=value` directives plus the `INTERFACE`, `PORT`, `RADIO`, `CONSOLE`, `APPL` and `ROUTES` blocks. Read only at program start | See [the configuration model](index.md) and the [directive reference](directive-reference.md) |
| `BOOTCMDS.SYS` | Commands executed at bootup, after `XROUTER.CFG` and `IPROUTE.SYS` — for settings that have no `XROUTER.CFG` keyword (e.g. `GNET`, `PPP`). One command per line | — |
| `CRONTAB.SYS` | Scheduler: runs node commands at given times (`min hour date month day command …`) | Lines ≤255 chars |

## NET/ROM and routing

| File | Purpose | Notes |
| --- | --- | --- |
| `XRNODES` | **NET/ROM nodes-table recovery file.** Populates the nodes table at boot so the node can restart without losing routes (rebuilding from broadcasts can take hours). Saved every `NODESINTERVAL`, at close-down and on `SAVENODES`. Plain text, no extension, sysop-editable | **Delete it before booting if the node has been down for a long time** — stale data re-introduces nodes that have since expired. See [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md) |

## IP, DNS and routing

| File | Purpose | Notes |
| --- | --- | --- |
| `IPROUTE.SYS` | IP router configuration — routes, ARP, NAT, RIP and filtering (`ACL`, `ARP`, `DUN`, `IP`, `NAT`, `RIP` commands). Needed only for IP routing/facilities | Read at boot or on `IP ROUTE LOAD`. See [The TCP/IP stack](../networking/tcpip.md) |
| `DOMAIN.SYS` | Hostname resolution, checked before external DNS (`hostname [ttl] IN A/CNAME/MX`) | Case-insensitive; can read a standard `DOMAIN.TXT` |
| `ENCAP.TXT` | AMPRNet encapsulated routing (IPEncap), read into the IP routing table at boot | `route addprivate 44.x.x.x/bits encap <ip>` |
| `XENCAP.TXT` | Extended `ENCAP.TXT` allowing modes other than encap (`ipip`, `ipudp`). Experimental | — |
| `DYNDNS.CFG` | Dynamic DNS update-client configuration | `;` comments; fixed line order |

## Security and access control

| File | Purpose | Notes |
| --- | --- | --- |
| `PASSWORD.SYS` | **Sysop password file** for AX.25 remote `@`, RLOGIN and FTP. Absent ⇒ no such access. `<callsign> <password>`, no SSID, not case-sensitive | Sysops only. See [Security and hardening](../admin/security-hardening.md) |
| `USERPASS.SYS` | **User passwords** for Telnet (port 23), the Telnet proxy (2323) and the APRS server (1448). *Not* for sysop / RLOGIN / FTP | See [Security and hardening](../admin/security-hardening.md) |
| `ACCESS.SYS` | Telnet access control by caller IP (`<subnet>[/bits] <flags>`; flags 1 = valid callsigns, 2 = password, 4 = guest, 8 = trusted/LAN). Absent ⇒ logins need only a valid callsign | See [Security and hardening](../admin/security-hardening.md) |
| `IPBAN.SYS` | Banned IPs and honeypot ports for the Intrusion Detection System | See [Security and hardening](../admin/security-hardening.md) |
| `HTTPBAN.SYS` | Blocks malicious HTTP request URLs by signature and bans the sender IP. `ANYCASE` prefix for case-independent matching | — |
| `POPUSERS.SYS` | POP3 server accounts | Default/format unverified in the manual |

## Egress control (`.ACL` files)

`.ACL` files share one format — `<PERMIT|DENY> <src_ip>[/mask] <dst_ip>[/mask] <port(s)>`,
first match wins, ordering is significant. Where a proxy is password-less, the matching `.ACL`
is essential to prevent it being abused as an open relay. See
[Security and hardening](../admin/security-hardening.md).

| File | Purpose | Notes |
| --- | --- | --- |
| `HTTP.ACL` | HTTP proxy egress control. Absent / no rules ⇒ all blocked (403) | — |
| `SOCKS.ACL` | SOCKS proxy egress control. Absent / no rules ⇒ all blocked | — |
| `TELGUEST.ACL` | Telnet guest egress control (guest = Telnet login with password "guest"). Absent ⇒ all blocked | Guests cannot downlink to AX.25/NET-ROM |
| `TELPROXY.ACL` | Telnet proxy egress control | Vital if the proxy is password-less |

## Web (HTTP server and proxy)

| File | Purpose | Notes |
| --- | --- | --- |
| `HTTP.SYS` | HTTP rewrite and proxy control (`REWRITE`, `PROXY`, `PROXYTIMEOUT`) | See [HTTP, REST and MQTT](../subsystems/http-rest-mqtt.md) |
| `EXEC.HTM` | HTML template wrapping the HTTP server's `/exec?cmd=` output (replaces the `<TEXT>` tag) | Kept in the working directory, not the HTTP tree |
| `XWEB.CLASS` | Java applet for connecting to XRouter from a web browser (Telnet or HTTP-tunnel) | Kept in the HTTP tree |

## Mailbox (PMS) — in the `PMS` subdirectory

These control files all live in the `PMS` subdirectory. See
[PMS mailbox](../subsystems/pms-mailbox.md).

| File | Purpose | Notes |
| --- | --- | --- |
| `PMS.CFG` | Mailbox configuration (`keyword=value`, e.g. `SysopCallsign`, `HousekeepInterval`, `ForwardInterval`, `MaxAgeB`). Absent ⇒ mailbox defaults | In `PMS` |
| `FWD.SYS` | Forwarding control — when and how to deliver queued mail to neighbours (per-BBS blocks, day/hour lines, a script) | In `PMS` |
| `DISTRIB.SYS` | Mail distribution control — queue mail to neighbour mailboxes; `^`-prefixed entries reject | In `PMS` |
| `HOLD.SYS` | Mail holding by TO/FROM/AT/local; held mail is sysop-only and not forwarded | In `PMS` |
| `REJECT.SYS` | Mail rejection control — reject offered mail by type/to/from/at/size | In `PMS` |
| `EXPORT.SYS` | Mail export control — archive or create files for other mail systems | In `PMS` |
| `BADWORDS.SYS` | Bad-words filter; matching mail is held for sysop review. One word/part-word per line, trailing `*` wildcard | In `PMS` |

## APRS

| File | Purpose | Notes |
| --- | --- | --- |
| `IGATE.CFG` | APRS IGate daemon configuration (`SERVER`, `IFILTER`, `PFILTER`, `RADIUS`, `PAUSE`, `MAXTRIES`, `LOG`, `SKIP`, `WAIT`) | See [APRS digipeater and IGate](../subsystems/aprs-igate.md) |

## Clients, modems and serial links

| File | Purpose | Notes |
| --- | --- | --- |
| `FTPCLI.SYS` | FTP client account file — automate logins to FTP servers (`<name> <host>[:port] [user [pass …]]`) | `*` = empty placeholder |
| `PPPHOST.n` | PPP configuration, one file per modem port (`n` = port). Needed only for auto-answer PSTN | — |
| `*.SCR` | Dialler scripts | — |

## Telnet proxy and language

| File | Purpose | Notes |
| --- | --- | --- |
| `TELPROXY.MSG` | Custom logon message for the Telnet proxy (replaces the default greeting) | Plain text |
| `LANGS.SYS` | Language-by-callsign selection at session start (`<callsign_mask> <lang_number>`; English 0, French 1, Spanish 2, German 3, Dutch 4). Unlisted callers get `DEFAULTLANG` | — |
| `ENGLISH.SYS` | English language text file (`<number> "quoted text"`) | Restart to apply |
| `FRANCAIS.SYS` | French language text file | Restart to apply |
| `ESPANOL.SYS` | Spanish language text file | Restart to apply |
| `DEUTSCHE.SYS` | German language text file | Restart to apply |

## Logs

| File | Purpose | Notes |
| --- | --- | --- |
| `PPPLOG.TXT` | PPP activity log | See [Logging and monitoring](../admin/logging-and-monitoring.md) |

---

!!! note "Platform-specific filenames"
    A few items use platform-specific names on the legacy Windows/DOS builds — for example the
    main configuration file is referenced as `XR16.CFG` / `XR32.CFG` for some XR16/XR32-only
    settings (such as the NDISXPKT driver). On current XrPi/XrLin/XrWin the file is
    `XROUTER.CFG`.

---

**Sources:** In-program manual mirrored on the OARC wiki —
[Section 8, Configuration and System Files (`man8`)](https://wiki.oarc.uk/packet:xrouter:docs:man8) ·
[ohiopacket.org XRPi mirror — "Files used by XRPi" and `install.htm`](https://ohiopacket.org/xrpi/docs/index.htm)
