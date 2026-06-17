# Running as a service

A node is only useful if it is up. This page covers how to run XRouter permanently on Linux:
the two ways to keep it running (daemon mode under `systemd`, or inside a `screen`/`tmux`
session), a clean generic `systemd` unit you can adapt, and the trade-off between them. It
assumes you already have a working node from one of the
[Getting started](index.md) pages.

XRouter on Linux is normally an interactive, full-screen text program. Left to itself it
runs in your terminal, and closing the terminal closes the node — no good for a station that
should be on the air 24/7. There are two established ways to fix that, and they suit
different needs.

## Two ways to keep it running

### Daemon mode (`-d`) under systemd

XRouter has a built-in **headless daemon mode**. Starting it with the `-d` flag runs it with
no text interface at all:

```bash
./xrlin -d
```

In this mode you control and monitor the node entirely over the network — its HTTP server,
Telnet, or by connecting in over packet. There is no TUI to attach to, which is exactly what
you want under a service manager: the program runs quietly in the background and `systemd`
looks after starting it at boot and restarting it if it ever exits.

!!! note "Daemon mode means no TUI"
    > "If you start xrpi with `xrpi -d` it runs completely headless, and you can only
    > control it via http or telnet (or packet)." — *Paula G8PZT*

    That is the clean option for a `systemd` service. If you specifically want the
    interactive screens, use the `screen`/`tmux` approach below instead.

### Inside screen or tmux (keep the TUI)

If you would rather keep XRouter's interactive interface — the status, monitor and console
windows — run it **without** `-d` inside a detachable terminal multiplexer such as `screen`
or `tmux`. The node runs in a session you can detach from and re-attach to at will, so it
keeps running after you log out but the full TUI is there whenever you reconnect:

```bash
# start the node inside a named tmux session, then detach with Ctrl-b d
tmux new -s xrouter -c ~/xrlin './xrlin'

# later, re-attach to see the screens
tmux attach -t xrouter
```

This keeps the live interface, which is genuinely useful for watching traffic and using the
sysop windows. The cost is that `screen`/`tmux` is not a process supervisor: if XRouter
exits, the session ends and nothing automatically brings it back unless you script that
yourself.

## A generic systemd unit

The following is a clean, generic `systemd` unit for daemon-mode operation. Adapt the user,
paths and binary name to your installation — do not run it as `root`. Save it as, for
example, `/etc/systemd/system/xrouter.service`:

```ini
[Unit]
Description=XRouter packet node
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=packet
WorkingDirectory=/home/packet/xrlin
ExecStart=/home/packet/xrlin/xrlin -d
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Then enable and start it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now xrouter.service

# check on it
systemctl status xrouter.service
```

A few points about this unit:

- **`Type=simple`** suits daemon mode: XRouter started with `-d` runs in the foreground of
  the service (it does not fork), so `systemd` tracks it directly.
- **`WorkingDirectory`** must be the directory that holds the binary and `XROUTER.CFG`,
  because XRouter reads and writes its files relative to where it runs.
- **`Restart=always`** with a short `RestartSec` is what gives you automatic recovery: if
  the node crashes or exits, `systemd` brings it back a few seconds later.
- **`User=packet`** runs the node as an unprivileged account, not `root` — see below.

!!! danger "Don't run the service as root"
    Run XRouter as a normal, unprivileged user, here `packet`. To let an unprivileged
    XRouter still bind privileged ports (Telnet on 23, HTTP on 80) and use raw sockets for
    `EXTERNAL`/Ethernet interfaces, grant the binary the two needed Linux capabilities once
    with `setcap` (see the [Linux PC](linux-pc.md#6-run-it-without-root-recommended) and
    [Raspberry Pi](raspberry-pi.md#6-run-it-without-root-recommended) pages):

    ```bash
    sudo setcap cap_net_raw,cap_net_bind_service=ep /home/packet/xrlin/xrlin
    ```

## The trade-off

Neither approach is "correct" — they optimise for different things:

| | `systemd` + `-d` | `screen` / `tmux` |
| --- | --- | --- |
| Starts at boot | Yes, natively | Needs a boot script of your own |
| Auto-restart on crash | Yes (`Restart=always`) | No — you must script it |
| Interactive TUI | No (control via HTTP/Telnet/packet) | Yes, attach any time |
| Best for | A set-and-forget hilltop node | A node you like to watch and tinker with |

In short, `systemd` gives you robust process management and automatic restarts but no live
interface; the `screen`/`tmux` approach keeps the attachable TUI but leaves restart-on-crash
up to you. Plenty of sysops run daemon mode under `systemd` for reliability and simply
connect over HTTP or Telnet when they want to look in.

## After an upgrade

Whichever approach you use, remember that replacing the XRouter binary undoes the Linux
capabilities you granted it:

!!! warning "Re-apply setcap after every upgrade"
    `setcap` is tied to the specific executable file. When you replace the binary with a new
    version, you must **`chmod +x` the new file and re-run `setcap`** before it will start
    correctly as an unprivileged service. Folding both into your upgrade routine avoids a
    confusing "it worked yesterday" failure. See [Upgrading and versions](../admin/upgrading.md).

## Next steps

- Upgrade safely without losing your config: [Upgrading and versions](../admin/upgrading.md).
- Tighten the node down: [Security and hardening](../admin/security-hardening.md).
- Operate it remotely: [The node command prompt](../commands/node-prompt.md).

---

**Sources:** [in-program manual, OARC wiki — installation/running topics (man6)](https://wiki.oarc.uk/packet:xrouter:docs:man6)
· [XRouter support group, groups.io](https://groups.io/g/xrouter) (the `-d` daemon-mode and
`setcap` guidance from the author). The `systemd` unit above is a generic example written for
these docs; adapt it to your own user and paths.
