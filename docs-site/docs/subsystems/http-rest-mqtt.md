# HTTP, REST and MQTT

XRouter exposes a set of web and automation surfaces that make it unusually scriptable and
monitorable for a packet node: a built-in HTTP server with a web UI, a REST API over that
same HTTP server, and an MQTT broker that can publish node events as JSON. This page
introduces all three and how to enable them. For turning the resulting data into ongoing
monitoring, see [Logging and monitoring](../admin/logging-and-monitoring.md).

## The built-in HTTP server and web UI

XRouter contains its own HTTP server. It serves a web interface for viewing and operating
the node from a browser, drawing its pages from the node's `/HTTP` directory (relocatable
with `HTTPROOT`). It is enabled by the `HTTPPORT` directive, which defaults to port **80**:

```ini
HTTPPORT=80        ; built-in web server (default 80)
```

Because the web UI lets you inspect and control the node, treat it like any other
remote-management surface.

!!! warning "Binding port 80 does not require running as root"
    On Linux, binding a privileged port such as 80 is better solved with the
    `cap_net_bind_service` capability than by running XRouter as root — see
    [Getting started on Raspberry Pi](../getting-started/raspberry-pi.md) and
    [Security and hardening](../admin/security-hardening.md). Also remember the global
    `IPADDRESS` rule: if `IPADDRESS` is unset, XRouter disables **all** IP services,
    including HTTP.

## The REST API

Layered on the same HTTP server is a **REST API**: request a documented URL and XRouter
returns structured data about the node, suitable for dashboards, scripts and integrations.
The in-program manual documents REST endpoints under an `/api/v1/` path, including:

| Endpoint | Returns |
| --- | --- |
| `/api/v1/netrom` | NET-ROM nodes / routing information. |
| `/api/v1/mail` | Mailbox (PMS) data. |
| `/api/v1/weather` | APRS weather data held by the node. |
| `/api/v1/blog` | The sysop blog. |
| `/api/v1/wall` | The message wall / guestbook. |

These let an external program read node state over plain HTTP without scraping the web UI.
The exact endpoint set grows between versions; check your build's HTTP documentation for the
authoritative list before depending on a particular path.

## The MQTT broker and event publishing

XRouter also includes an **inbuilt MQTT broker**, enabled by the `MQTTPORT` directive
(default port **1883**). MQTT is a lightweight publish/subscribe protocol; running a broker
inside the node means local automation and dashboards can subscribe to its event stream
directly.

```ini
MQTTPORT=1883      ; inbuilt MQTT broker (default 1883)
```

In addition to hosting its own broker, XRouter can **publish to an external broker**. Point
it at one with:

| Directive | Purpose |
| --- | --- |
| `MQTTSERVADDR` | Hostname/IP of an external MQTT broker to publish to. Absent means the external publisher is off. |
| `MQTTSERVPORT` | The external broker's port (default 1883). |

Either way, XRouter emits **JSON event messages** on topics of the form:

```text
xrouter/event/{nodecall}/...
```

Each message is a JSON object describing something that happened on the node. For example,
a node coming up produces an event whose payload carries the node's callsign, alias,
locator, coordinates, software name and version — the kind of structured event you can feed
straight into a monitoring system. AX.25/KISS activity and mailbox events are published in
the same way.

!!! tip "This is what makes XRouter easy to monitor at scale"
    The combination of a REST API for state and an MQTT event feed for changes is unusual
    in this niche. Together they let you build live network maps, dashboards and alerting
    around a node without touching its console — see
    [Logging and monitoring](../admin/logging-and-monitoring.md) for ways to use them.

---

**Sources:** [In-program manual, OARC wiki — Section 9 (HTTP, REST, MQTT)](https://wiki.oarc.uk/packet:xrouter:docs:man9) ·
[OARC wiki — XRouter overview (REST/MQTT)](https://wiki.oarc.uk/packet:xrouter) ·
[In-program manual — Section 7 (directives)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
