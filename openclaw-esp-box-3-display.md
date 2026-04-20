# OpenClaw ESP-BOX-3 Display In ESP Launchpad

This guide covers using the prebuilt OpenClaw ESP-BOX-3 display firmware in
ESP Launchpad.

Commands below assume the default OpenClaw install. If you use a named profile,
add `--profile <profile>` to the `openclaw` commands.

## What You Need

- A running OpenClaw gateway host that the board can reach over the LAN
- The `openclaw` CLI on that gateway host
- The Wi-Fi SSID and passphrase you want the board to use

## After Flashing

1. Click `Connect Your Device`.
2. Choose the serial port for the board.
3. ESP Launchpad erases flash automatically, then flashes the selected
   firmware.
4. After flashing completes, the app guide opens and the serial console starts
   automatically.
5. Wait for the `openclaw>` prompt. If the console disconnects, click
   `Restart Device`.
6. Use the command input box below the console to send REPL commands to the
   board.

On the ESP-BOX-3, the REPL is exposed over the native USB Serial/JTAG console.

## Gateway Setup And Pairing

On the gateway host, bind the gateway to the LAN and allow the display example
commands:

```bash
openclaw config set gateway.bind lan
openclaw config set gateway.nodes.allowCommands '[
  "device.info",
  "device.status",
  "wifi.status",
  "display.show",
  "display.status"
]' --strict-json

openclaw gateway restart
```

Generate a setup code:

```bash
openclaw qr \
  --url ws://<gateway-host-ip>:<gateway-port> \
  --setup-code-only
```

Then bring the board online from the Launchpad console:

```text
status
wifi set <ssid> <passphrase>
gateway setup-code <setup-code>
status
```

Once Wi-Fi connects, the node transport starts automatically. The setup code
contains a short-lived `bootstrapToken`, not the gateway's shared token.

## Check The Node From The Gateway

```bash
openclaw nodes status --json
openclaw nodes invoke --node <node-id> --command display.status --json
```

## Drive The Display

```bash
openclaw nodes invoke \
  --node <node-id> \
  --command display.show \
  --params '{"heading":"Hello","text":"OpenClaw is driving the ESP-BOX-3 display."}' \
  --json
```

## Useful Follow-Up Commands

- `wifi clear`
- `wifi connect`
- `wifi disconnect`
- `reboot`
- `gateway no-auth <ws://host:port>`
- `gateway token <ws://host:port> <token>`
- `gateway password <ws://host:port> <password>`
- `gateway connect`
- `gateway disconnect`

## Troubleshooting

### Display commands fail with `unsupported command`

Update the gateway allowlist, then reconnect or reboot the board.

### Setup code expired or pairing did not complete

Generate a fresh setup code and try the pairing flow again. If you want a clean
reset of saved Wi-Fi credentials or stored gateway session state first, reflash
the board from the picker page.
