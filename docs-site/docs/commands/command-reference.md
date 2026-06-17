# Command reference

This page lists XRouter's node and sysop commands, plus the mailbox and chat
command sets, in compact tables. It is for sysops and confident users who want
the whole picture in one place; for a guided introduction start with
[The node command prompt](node-prompt.md).

!!! note "The node's own HELP is authoritative"
    These tables are generated from XRouter's in-program manual (the man-style
    reference sections mirrored on the OARC wiki). Your build may differ slightly.
    On any node, **`?`** lists the commands it actually offers and
    **`HELP <command>`** gives its exact, version-correct syntax, treat that as
    the final word. Where the manual itself flagged a command's exact syntax as
    uncertain, it is marked **(syntax unverified)** below rather than guessed.

**How to read these tables.** The **Abbrev** column gives the shortest accepted
form (the manual writes commands like `C[onnect]`, meaning the un-bracketed stem
is the minimum). Commands are not case-sensitive. **Scope** is *user* (available
to ordinary connected users) or *sysop* (requires sysop status, see
[Sysop access and commands](sysop.md)); some commands are usable by both but do
more for a sysop, noted in Purpose.

## Node and sysop commands (Section 1)

The general command set, roughly 108 commands, `ACL` through `YELL`. Sysop-only
[PZTDOS](#pztdos-file-commands-section-3) file commands are reached through `DOS`
and listed separately below.

| Command | Abbrev | Scope | Purpose |
| --- | --- | --- | --- |
| ACL | `AC` | sysop | Edit the IP Access Control List (live packet-filter rules) |
| AMSG | `AM` | user | Enter APRS Messaging mode on a port (guests cannot send) |
| ANSI | `AN` | user | Enquire / set ANSI colour for the session |
| APPLMASK | `APP` | sysop | Display / set a port's application connectivity mask |
| ARP | `ARP` | sysop* | Display / edit the ARP table (user access configurable via ARP CMD flag) |
| AXROUTES | `AX` | sysop | Display AX.25 level-2 circuits |
| BCAST | `BC` | sysop | Trigger a NODES broadcast on a port |
| BCPOLL | `BCP` | sysop | Request NODES broadcasts from neighbours |
| BELL | `BE` | sysop | Display / set console bell hours |
| BLEVEL | `BL` | sysop | Set the NET/ROM budlist de-rate (leakage) level |
| BLOG | `BL` | user | Read the sysop's blog (only sysops create articles) |
| BYE | `B` | user | Disconnect from the node |
| CAPTURE | `CAP` | sysop | Enable / disable tracing to a disk file |
| CFLAGS | `CF` | sysop | Display / change a port's connection-control flags |
| CHAT | `CH` | user | Connect to the chat server (not guests) |
| CMD | `CM` | sysop | Add / delete command aliases |
| CONNECT | `C` | user | Make an outgoing AX.25 / NET/ROM / NetRom-X connection (not guests) |
| CQ | `CQ` | user | Send a CQ (UI), only while in LISTEN mode |
| CTEXT | `CT` | sysop | Display / set a port's connect text |
| CTFLAGS | `CTF` | sysop | Display / set connect-text control flags |
| CTRL | `CTRL` | sysop | Read / write the remote hardware control port |
| CX | `CX` | user | Outgoing AX.25 level-2 connect using Modulo-128 (not guests) |
| DATE | `DATE` | sysop | Enquire / set the system date |
| DHCP | `DHCP` | sysop | Display / manage DHCP-obtained IP configuration |
| DIAL | `DIA` | sysop | Dial a PSTN connection (DUN script) |
| DIGIFLAG | `DIGIF` | sysop | Display / set a port's digipeat options |
| DIGIPORT | `DIGIP` | sysop | Display / set the port to digipeat onto |
| DISCARD | `DIS` | sysop | Start a data-sink session (link testing) |
| DNS | `DNS` | sysop | DNS server commands |
| DOS | `DOS` | sysop | Enter PZTDOS file-maintenance mode |
| DUN | `DUN` | sysop | Dial-Up Networking configuration |
| DX | `DX` | user | Display distant APRS stations heard |
| ECHO | `ECHO` | user | Start an echo session (link testing) |
| EXCLUDE | `EXCLUDE` | sysop | Manage a port's AX.25 callsign blacklist (syntax unverified) |
| EXIT | `EXIT` | sysop | Terminate the program |
| FEC | `FEC` | sysop | Enable / disable Forward Error Correction on a port |
| FINGER | `FINGER` | user | Display user information (local or remote) |
| FRACK | `FRACK` | sysop | Display / set a port's AX.25 frame-ack timeout |
| FTP | `FTP` | sysop | FTP client / connect (syntax unverified) |
| FTP-CMDS | n/a  user | Reference: the FTP server's verbs |
| FULLDUP | `FULLDUP` | sysop | Display / set full-duplex on a port (SCC only) |
| GNET | `GNET` | sysop | Globalnet configuration (syntax unverified) |
| GPING | `GP` | user | Send a GCMP / GNET echo request (diagnostic) |
| HELP | `HELP` | user | Display help; `HELP <command>` for one command |
| HOST | `HO` | user | TCP/IP host lookup (name ↔ IP) |
| IDPATH | `IDPATH` | sysop | Display / set a port's ID-beacon path (syntax unverified) |
| IDS | `IDS` | sysop | Intrusion Detection System control (bans, honeypots, unban) |
| IDTEXT | `IDTEXT` | sysop | Display / set a port's ID-beacon text |
| IFACE | `IFACE` | sysop | Interface command (purpose given as a placeholder in the manual) |
| INFO | `I` | user | Display node / topic information |
| IP | `IP` | sysop* | IP routing-table sub-commands (users get the read-only `IPROUTE` view) |
| IPADDRESS | `IPADDRESS` | sysop | Display / set a port's IP address |
| IPLINK | `IPLINK` | sysop | Display / set an AXIP / AXUDP peer |
| IPROUTE | `IPR` | user | Display the IP routing table (AMPRNet view) |
| J | `J` | user | List recent connections (last 20) |
| KEEPALIVE | `KEEPALIVE` | sysop | Display / set link keepalive |
| KILL | `KILL` | sysop | Terminate a session; accepts multiple sessions and `ALL` |
| LANG | `LA` | user | Show / set the session language |
| LINKED | `LINKED` | gated | "*** LINKED AS" handling (controlled by `ENABLE_LINKED`) |
| LINKS | `LINKS` | user | List AX.25 level-2 links |
| LISTEN | `LIS` | user | Enter listen mode on a port (users: RADIO ports only) |
| LOADNODES | `LOADNODES` | sysop | Load the nodes / routes tables from file |
| LOG | `LOG` | sysop | Activity-logging control |
| MAN | `MAN` | user | Access the online sysop's manual (non-sysops may be barred) |
| MAXFRAME | `MAXFRAME` | sysop | Display / set max AX.25 frames on a port |
| MHCLEAR | `MHCLEAR` | sysop | Clear a port's MHeard list |
| MHEARD | `M` / `MH` | user | List recently heard stations |
| MHFLAGS | `MHFLAGS` | sysop | Display / set MHeard content flags |
| MHSIZE | `MHSIZE` | sysop | Display / set MHeard list size |
| MINQUAL | `MINQUAL` | sysop | Display / set minimum quality to add a node |
| MINTXQUAL | `MINTXQUAL` | sysop | Display / set minimum quality to broadcast a node |
| MMASK | `MMASK` | sysop | Display / set the console monitor mask (default `0x3ff`; `0x43ff` adds hex) |
| MONITOR | `MON` | sysop | Enable / disable live tracing |
| MOTD | `MOTD` | sysop | Message of the day |
| MPORT | `MPORT` | sysop | Select the console's monitored ports |
| MQTT | `MQTT` | sysop | MQTT control (syntax unverified) |
| MTO | `MTO` | sysop | Monitor frames to / from a destination (syntax unverified) |
| NAT | `NAT` | sysop | Network Address Translation control |
| NETMASK | `NETMASK` | sysop | Display / set a port's netmask |
| NFTP | `NF` | user | NetRom File Transfer (non-sysops: the node's own call only) |
| NODES | `N` | user | Display the NET/ROM nodes table (sysop can modify) |
| NODESINT | `NODESINT` | sysop | Display / set the NODES broadcast interval |
| NPING | `NP` | user | NET/ROM echo request (diagnostic) |
| NRR | `NRR` | user | Send a NET/ROM record-route packet |
| NTRACERT | `NT` | user | Trace the route to a NET/ROM node |
| NTTY | `NTTY` | user | NetRom TTY (keyboard-to-keyboard chat) (syntax unverified) |
| PACLEN | `PACLEN` | sysop | Display / set a port's packet length |
| PCAP | `PCAP` | sysop | Packet capture control |
| PEERS | `PEERS` | sysop | Display NCMP peers (syntax unverified) |
| PERSIST | `PERSIST` | sysop | Display / set a port's CSMA persistence |
| PING | `PING` | user | ICMP echo request to a TCP/IP host |
| PIPE | `PIPE` | sysop | Display / set a frame pipe between ports |
| PIPEFLAG | `PIPEFLAG` | sysop | Display / set frame-pipe flags |
| PMS | `PM` | user | Connect to the PMS / mailbox |
| PORTS | `P` | user | List the node's ports |
| PPP | `PPP` | sysop | PPP control |
| QUALITY | `QUALITY` | sysop | Display / set a port's NET/ROM quality |
| QUIT | `Q` | user | Disconnect from the node (synonym of BYE) |
| RADIO | `RADIO` | sysop | Radio-control commands |
| REBOOT | `REBOOT` | sysop | Reboot the node |
| RESPTIME | `RESPTIME` | sysop | Display / set a port's AX.25 delayed-ack time |
| RESTART | `RESTART` | sysop | Restart XRouter (re-reads the configuration) |
| RETRIES | `RETRIES` | sysop | Display / set a port's max retries |
| RIP | `RIP` | sysop | RIP routing-information control |
| ROUTES | `R` | user | List adjacent NET/ROM nodes (Add / Drop / Load are sysop-only) |
| SAVENODES | `SAVENODES` | sysop | Save the nodes / routes tables to file |
| SEND | `SE` | user | Send an unproto (UI) packet |
| SHELL | `SHELL` | sysop | Drop to an OS shell |
| SLOTTIME | `SLOTTIME` | sysop | Display / set a port's CSMA slot time |
| SMS | `SMS` | n/a  Short Message System (syntax unverified) |
| START | `START` | sysop | Start a port / service / daemon |
| STATS | `S` | user | Display router performance statistics |
| STOP | `STOP` | sysop | Stop a port / service / daemon (no argument lists daemons) |
| TALK | `TALK` | sysop | Talk / chat (syntax unverified) |
| TCP | `TCP` | sysop | TCP control / SYN cache |
| TELNET | `TELNET` | user | Connect to a TCP/IP host (Telnet) |
| TIME | `TI` | user | Report the local time (this or another node) |
| TNC | `TNC` | sysop | TNC control (e.g. connect to a VARA / ARDOP modem) (syntax unverified) |
| TRACERT | `TR` | user | Trace the route to a TCP/IP host |
| TSYNC | `TSYNC` | sysop | Time sync via an internet time server (syntax unverified) |
| TTYLINK | `TT` | user | Chat directly to TCP/IP users |
| TXDELAY | `TXDELAY` | sysop | Display / set a port's TX delay |
| TXOK | `TXOK` | sysop | Enable / disable transmit on a port |
| TXPORT | `TXPORT` | sysop | Display / set the TX port |
| TXTAIL | `TXTAIL` | sysop | Display / set a port's TX tail |
| UDP | `UDP` | sysop | UDP control (syntax unverified) |
| UDPLOCAL | `UDPLOCAL` | sysop | Display / set the local AXUDP port |
| UDPREMOTE | `UDPREMOTE` | sysop | Display / set the remote AXUDP port |
| UNPROTO | `UNPROTO` | sysop | Display / set a port's unproto path |
| USERS | `U` | user | Display circuits (who is using the node) |
| VERSION | `V` | user | Display the software version and author |
| WAIT | `WAIT` | sysop | Pause within a script (syntax unverified) |
| WALL | `W` | user | Connect to the message wall / guestbook |
| WATCH | `WAT` | user | Monitor traffic on RF port(s) (users: RADIO ports only) |
| WX | `WX` | user | Display local APRS weather stations |
| XLINK | `XLINK` | sysop* | Cross-link control (e.g. XLINK PPP for modem callers) |
| YELL | `Y` | user | Page the sysop for a chat |

!!! note "Script and shell-out commands"
    Two sysop commands are single punctuation characters rather than words:
    **`<`** executes an XRouter command script from a file, and **`!`** runs a
    command in an OS shell. The **`***`** form is the temporary console-callsign
    override ("LINKED AS"). The **`@`** character, by contrast, is a *user*
    facility: it requests and answers the remote-sysop password challenge.

## Mailbox commands (Section 4)

Entered with the node `PMS` command. Most are open to all mailbox users
(non-sysops are restricted to mail they originated or that is addressed to them;
private mail is hidden from third parties). The command grammar is terse: the
first letter is a verb (**L**ist, **K**ill, **R**ead, **S**end, **N** user-record,
**I** White-Pages lookup) and a second character selects a subset (`<` from a
call, `>` to a call/topic, `@` at a distribution, `$` awaiting forwarding, and
letter suffixes such as `N`=new, `P`=private, `B`=bulletins). Full detail is on
[PMS mailbox](../subsystems/pms-mailbox.md).

| Command | Scope | Purpose |
| --- | --- | --- |
| `BYE` | user | Disconnect from the mailbox |
| `HELP` | user | Mailbox help |
| `INFO` / `I` | user | Mailbox and White Pages information |
| `I@` `IC` `IH` `IN` `IQ` `IZ` | user | White-Pages lookup by BBS / callsign / hierarchical address / name / QTH / zip |
| `J` | user | Recent mailbox sessions |
| `LIST` / `L` | user | List messages by number |
| `L<` `L>` `L@` `L$` (`LQ`) | user | List messages from / to / at / awaiting forwarding |
| `LA` `LO` `LL` | user | List oldest / oldest / latest |
| `LB` `LC` | user | List bulletins / bulletin categories |
| `LF` | user | List successfully forwarded mail |
| `LH` | sysop | List held mail |
| `LM` `LN` `LP` `LR` `LS` `LT` `LU` | user | List mine / new / private / read-private / by subject / by text / unread |
| `M` / `MINE` | user | List messages you sent |
| `KILL` / `K` | user | Kill message(s) by number |
| `K<` `K>` | user | Kill messages from / to |
| `K@` `KF` `KH` `KR` | sysop | Kill messages at a distribution / forwarded / held / read |
| `KM` | user | Kill mine (read mail addressed to you) |
| `READ` / `R` | user | Read message(s) by number |
| `RH` | user | Read with routing headers |
| `R<` `R>` `R@` `RM` `RN` | user | Read from / to / at / mine / new |
| `U` / `UNREAD` | user | Mark a message unread |
| `SEND` / `S` | user | Send a personal message |
| `SB` `SP` `SR` | user | Send bulletin / personal / reply |
| `CB` `CP` | user | Copy a message to a bulletin / to private |
| `NAME` / `N` | user | Set your White-Pages first name |
| `NC` `NH` `NI` `NP` `NQ` `NZ` | user | Set ANSI colour / home BBS / show record / pagination / QTH / zip |
| `EH` | sysop | Edit a message header |
| `EXPORT` / `EX` | sysop | Export a message to a file |
| `IMPORT` / `IM` | sysop | Import message(s) from a file |
| `HOLD` / `HO` | sysop | Hold a message for review |
| `FF` `FP` | sysop | Force forwarding / force polling |
| `MAN` | user | Display the sysop manual pages |

A handful of rarely used sysop mailbox variants (`MB`, `MF`, `MFH`, `MP`, `SF`,
`UH`, `VF`) are named in the manual index but their exact syntax is unverified.

## Chat server commands (Section 2)

Used **inside** the chat server (reached with the node `CHAT` command). All are
`/`-prefixed. The full walkthrough is on [Chat server](../subsystems/chat.md).

| Command | Purpose |
| --- | --- |
| `/ALERT` | Enable / disable channel join-leave alerts |
| `/ANSI` | Enable / disable ANSI colour |
| `/BELL` | Audible-warning level (0 none … 3 all) |
| `/BYE`, `/EXIT`, `/QUIT` | Disconnect from the conference |
| `/CHANNEL` | Display / change channel(s); set the default |
| `/ECHO` | Control host echo |
| `/HEADERLN` | Header and text on the same line or separate |
| `/JOIN` | Also log to another channel |
| `/KEEPALIV` | Enable / set link keepalive |
| `/KM` | Kill stored personal chat messages |
| `/KNOWN`, `/NODES` | List known RoundTable nodes |
| `/LEAVE` | Log off a channel |
| `/LINKS` | Display / manage links and peer servers (ADD / DROP are sysop) |
| `/MSG` | Short message to a channel or user |
| `/NAME` | Set your name (required to join) |
| `/PERSONAL` | Display / change your personal description |
| `/PORTS` | List RoundTable ports / links (room 101 only) |
| `/QTH` | Set your QTH (required in room 101) |
| `/RECENT` | Last 10 messages in the past 24 hours |
| `/RM` | Read stored personal messages |
| `/STAMP` | Time-stamp messages |
| `/STATUS` | Set / show presence (Available, Away, Busy, …) |
| `/TOPIC` | Display / change a channel topic |
| `/USER` | (TCP/IP users) set callsign and name |
| `/USERS` | (room 101 only) list RoundTable users |
| `/VERBOSE` | Verbose cross-channel alerts |
| `/VERSION` | Chat server version, author and date |
| `/WHO` | List who is in the conference |

## Blog and wall commands

The sysop's **blog** (node command `BLOG`) and the message **wall** / guestbook
(node command `WALL`) each have a small sub-command set:

- **Blog:** `B`ye, `C`reate (sysop), `D`elete, `H`elp, `LIK`e / `K`, `L`ist,
  `N`ewer, `O`lder, `Q`uit, `R`ead, `REP`ly / `Y`, `V`iew.
- **Wall:** `H`elp, `L`ist, `O`lder, `N`ewer, `W`rite, `D`elete, `Q`uit.

## APRS messaging commands

Used inside APRS Messaging mode (node command `AMSG <port>`). All `/`-prefixed
and case-insensitive; the first letter suffices. See
[APRS digipeater and IGate](../subsystems/aprs-igate.md).

| Command | Purpose |
| --- | --- |
| `/A` | List APRS announcements |
| `/B` | List APRS bulletins |
| `/C` | List / cancel your unacknowledged outgoing messages |
| `/D` | List directly-heard stations |
| `/H` | Help |
| `/M` | Monitor mode (messages not addressed to you) |
| `/Q`, `/X` | Quit / exit the APRS session |
| `/T` | Set the message target |
| `/U` | UI-View message-format mode |
| `/V` | Set the digipeater path |

## PZTDOS file commands (Section 3)

These are reached by entering PZTDOS with the node `DOS` command, and are
**sysop-only**. They provide DOS-style file maintenance, useful for managing the
node's files remotely over a slow link. (A leading `drive:` is accepted only on
legacy Windows / DOS builds and is ignored on Linux; forward and back slashes are
interchangeable.) See [Sysop access and commands](sysop.md).

| Command | Purpose |
| --- | --- |
| `CD` | Change the working directory |
| `COPY` | Copy file(s) (also `COPY CON <file>`) |
| `DEL` | Delete file(s) |
| `DF` | Display free space (reported incorrectly on Linux) |
| `DIR` | Directory listing |
| `EDIT` / `E` | Invoke the line editor |
| `EXIT` | Leave PZTDOS mode |
| `MD` | Make a directory |
| `MOVE` | Move file(s), optionally renaming |
| `REN` | Rename file(s) (same drive only) |
| `RMDIR` | Remove a directory |
| `TYPE` | Display a text file |

## FTP server verbs

XRouter's FTP server (sysop maintenance; default TCP port 21) supports the
standard FTP command verbs: `ABOR`, `CDUP`, `CWD`, `DELE`, `HELP`, `LIST`, `MKD`,
`MODE`, `NLST`, `NOOP`, `PASS`, `PASV`, `PORT`, `PWD`, `QUIT`, `RETR`, `RMD`,
`RNFR`, `RNTO`, `STOR`, `STRU`, `SYST`, `TYPE`, `USER` (and the extensions `FEAT`,
`MDTM`, `MFMT`, `REST`, `SIZE`). Individual verb behaviour follows standard FTP;
the manual does not document each one separately.

---

**Sources:** [In-program manual, Section 1: General Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man1)
· [Section 3: PZTDOS Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man3)
· [Section 4: Mailbox Commands (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man4)
· [Chat Server Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:chatserverhelp)
· [Mailbox Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:mailboxhelp)
· [General Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:generalhelp)
· [APRS Message Server Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:aprsmessageserverhelp)
· [FTP Server Help (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:ftpserverhelp)
· [XRouter support group (groups.io)](https://groups.io/g/xrouter)
