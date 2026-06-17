# Logging and monitoring

Knowing what your node is doing, who is connecting, what is passing over the air, whether a
link is healthy, is half of running one well. XRouter gives you several overlapping ways to
watch it: live status and monitor screens, on-demand traffic capture to a file, activity logs,
performance statistics, and two automation surfaces (a REST API and an MQTT event feed) for
feeding a dashboard. This page covers each, and how to capture a crash backtrace if the worst
happens.

It assumes a working node; the commands below are typed at the [node command
prompt](../commands/node-prompt.md) or a console window, and most monitoring controls are
sysop-only.

## The status and console screens

Run interactively (not in `-d` daemon mode) and XRouter presents a carousel of full-screen
windows you move between with the arrow keys or `Alt`+`W`. Alongside the command console there
are dedicated screens for **status/overview** (shown at startup), a **session monitor**, a
**nodes monitor**, a **routes monitor**, a **chat monitor**, and the **Security Monitor** that
shows intrusion-detection activity. These give you an at-a-glance picture without typing
anything.

If you run the node headless under a service manager you will not see these, that is the
trade-off of daemon mode. See [Running as a service](../getting-started/running-as-a-service.md)
for keeping the TUI available under `screen`/`tmux`, and operate a headless node over HTTP,
Telnet or packet instead.

## Capturing traffic

For live troubleshooting, XRouter can trace frames to the screen and to a file. The author's
recipe is short:

!!! tip "Decode all traffic to a text file"
    > "To decode all traffic in xrpi to a text file … `MON on`, `Cap on`, job done. If you
    > want each packet hex dumped, set `mmask 43ff` instead of the default `3ff`.",
    > *Paula G8PZT*

In other words:

```text
MON ON        ; enable monitoring (tracing)
CAP ON        ; also write the trace to a capture file on disk
```

- **`MON`/`MONITOR`** turns tracing on; **`CAP`/`CAPTURE`** mirrors that trace to a disk file.
- **`MMASK`** is a hex bitmask selecting which protocols are traced. The default is **`0x3ff`**.
  Setting **`MMASK 43ff`** adds a full **hex dump** of each packet, invaluable when you need to
  see exactly what is on the wire, noisy otherwise.
- **`MPORT`** selects which ports are monitored; users can also use `LISTEN`/`WATCH` to view
  RF ports.

Turn capture off again (`CAP OFF`) when you are done. There is also a separate
Wireshark-compatible IP packet capture (`PCAP`), but note the author's warning that it "can
fill the disk fast", it is for short diagnostic sessions, not long-term running.

!!! warning "Capture files grow quickly"
    A capture with `MMASK 43ff` on a busy port produces a lot of text. Capture for as long as
    you need to catch the problem, then stop, do not leave it running unattended on a node
    with limited storage.

## Log files and the LOG directive

Separately from on-demand capture, XRouter keeps **activity logs** in the `LOG` subdirectory
(auto-created at runtime). What gets logged is controlled by the global **`LOG`** directive, a
bitmask where `0` means no logging; you raise bits to log more categories (IDS events and
packet-capture events have their own bits, for instance). The live `LOG` command adjusts it
without a restart. A boot log (`BOOTLOG.TXT`) records startup, and is the first place to look
if the node will not start.

!!! note "Viewing logs on Windows? Use the CRLF option"
    XRouter offers a CRLF line-ending option on its logs "for those who view the logs on a
    Windows machine". Linux-native log viewers handle bare `LF` fine, but some Windows text
    viewers show everything on one line without it, enable the CRLF option if your logs look
    like a single run-on line in Notepad.

## Statistics

The **`STATS`** command reports running performance counters. On its own it gives an overview;
with an argument it narrows to a layer or subsystem, for example `STATS L2`, `STATS L4`,
`STATS TCP`, `STATS IP`, and `STATS IDS` for intrusion-detection counters. It is a quick,
non-intrusive health check: rising error or retry counts on a link, or a steadily growing IDS
hit count, tell you where to look next.

## Automation: REST API and MQTT

For anything beyond eyeballing a screen, a status page, a Grafana panel, an alerting rule,
XRouter exposes two machine-readable surfaces. Both are covered in detail on
[HTTP, REST and MQTT](../subsystems/http-rest-mqtt.md); in summary:

- **REST API.** Served over the built-in HTTP server (so it rides on `HTTPPORT`, default 80),
  returning JSON. Endpoints under `/api/v1/` cover the NET/ROM nodes and routes
  (`/api/v1/netrom`), mail, weather, the blog and the wall, handy for polling node state into
  your own monitoring.
- **MQTT event feed.** XRouter has an inbuilt MQTT broker (`MQTTPORT`, default 1883) and can
  also publish to an external broker (`MQTTSERVADDR`/`MQTTSERVPORT`). It emits node and traffic
  events as JSON, for example a `nodeup` event carrying the node call, alias, locator,
  software and version, so a subscriber can react to the node coming up, links changing, mail
  arriving and so on. This is an event *push*, complementing the REST *pull*.

Between them, a pull-based REST poll and a push-based MQTT subscription cover most monitoring
needs without screen-scraping.

## Capturing a crash backtrace

If XRouter crashes, the single most useful thing you can give the author is a backtrace from a
debugger. On Linux, run the node under `gdb`:

```bash
gdb --ex run ./xrpi
```

Let it run normally. When it crashes, `gdb` will stop at the fault; then ask for a full
backtrace and copy the output:

```text
bt full
```

Send that backtrace (with your version string and a note of what you were doing) to the
[support group](https://groups.io/g/xrouter). This mirrors the long-standing gdb-backtrace
practice in the packet community and gives the author something concrete to diagnose, rather
than "it crashed". See also [Troubleshooting](troubleshooting.md#freezes-segfaults) for the
context around historic stability fixes.

---

**Sources:** [XRouter support group, groups.io](https://groups.io/g/xrouter) (the `MON`/`CAP`/
`MMASK` capture recipe, the MQTT `nodeup` event shape, and the gdb backtrace workflow, all
community/author guidance) ·
[in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs:man1) (the `MON`,
`CAP`, `MMASK`, `STATS`, `LOG`, `PCAP` commands;
[man9](https://wiki.oarc.uk/packet:xrouter:docs:man9) for the REST and MQTT surfaces) ·
release notes (the `LOG` directory move, IDS/PCAP log bits, and the CRLF log option for
Windows viewers)
