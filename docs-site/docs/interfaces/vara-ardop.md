# VARA and ARDOP

This page covers reaching the **VARA** (FM and HF) and **ARDOP** soundcard modems from
XRouter. It is for a sysop who already has VARA or ARDOP installed and working with a radio,
and wants XRouter to use it as a packet modem. VARA and ARDOP are not configured like a KISS
TNC; XRouter attaches to the running modem over a TCP connection.

VARA and ARDOP are software modems that run as their own applications and expose a TCP
interface for other programs to drive. XRouter connects to that interface rather than to a
serial KISS device, so, as with [Direwolf KISS-over-TCP](soundcard-modems.md), the modem
runs alongside XRouter and the two talk over a socket.

## Attaching XRouter to the modem

You configure a port for the modem, and then attach to the running modem from the node with
the **`TNC <port>`** command. For example, if VARA is set up as port 6 on your node, the
sysop command:

```text
TNC 6
```

attaches XRouter to the VARA modem on that port. This is the mechanism the author uses for her
own VARA port. **`ARDOP` is also supported** by XRouter in the same family of soundcard-modem
interfaces.

!!! note "Specifics here are deliberately thin"
    The community sources behind this page confirm *that* XRouter supports VARA and ARDOP and
    *that* you attach to the modem with `TNC <port>`, but they do not pin down every keyword of
    the interface block, the exact TCP ports, or a full worked configuration. Rather than invent
    those details, this page stops at what is known. For a complete, current VARA setup,
    please ask on the [XRouter support group](https://groups.io/g/xrouter), where the author
    and other VARA users can give you a configuration that matches your build.

## External VARA references

VARA is widely used with other packet software, and those setups are a useful guide to how
the modem itself is installed and connected over TCP. One reference noted by the community is
the **VARA + JNOS** write-up at
[langelaar.net/projects/jnos2/vara](https://www.langelaar.net/projects/jnos2/vara), it is
about JNOS rather than XRouter, but it illustrates the general pattern of driving a VARA modem
over its TCP interface, which carries across.

!!! tip "VARA and ARDOP are still IP/TCP links"
    Because XRouter reaches these modems over TCP, the `IPADDRESS` rule applies: with the
    global `IPADDRESS` left at `0.0.0.0`, all IP activity is disabled and the modem connection
    will not come up. Set a non-zero address (a private dummy such as `10.1.1.1` is fine),
    see [Global settings](../configuration/global-settings.md).

## Where to go next

- For KISS-based soundcard modems (Direwolf, UZ7HO, QtSoundModem), which *are* fully worked
  through here, see [Soundcard modems](soundcard-modems.md).
- For the node command set, including how sysop commands like `TNC` are issued, see
  [The node command prompt](../commands/node-prompt.md).
- For current, build-specific VARA/ARDOP configuration, the
  [XRouter support group (groups.io)](https://groups.io/g/xrouter) is the authoritative place
  to ask.

---

**Sources:** [groups.io: author runs a VARA port, attaches with `TNC 6`](https://groups.io/g/xrouter/message/3407) ·
[groups.io: ARDOP interface also exists in XRouter](https://groups.io/g/xrouter/message/3413) ·
[groups.io: VARA is a soundcard modem reached over TCP](https://groups.io/g/xrouter/message/633) ·
[groups.io: VARA+JNOS reference (external)](https://groups.io/g/xrouter/message/3366).
This page intentionally omits interface-block specifics not confirmed in those sources.
