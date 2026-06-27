# linux-serial-helper

An interactive helper that walks you through connecting to serial ports and USB serial devices on Linux using `screen`.

## Why

Connecting to serial consoles or USB-UART adapters on Linux usually means:

- Figuring out which `/dev/ttyUSB*` or `/dev/ttyACM*` just appeared
- Remembering the right baud rate
- Dealing with permissions
- Typing the `screen` command correctly

This tool makes it a guided, friendly experience.

## Features

- **Walks you through everything** interactively
- Discovers and describes serial ports with friendly names (VID/PID, USB serial number, stable by-id symlinks)
- Common baud rate presets + custom entry
- Checks that `screen` is installed and that you have permission to open devices
- Shows exact command before launching
- Helpful messages for the most common gotchas (permissions, needing logout/login, kernel messages)

## Installation

Clone and run directly:

```bash
git clone https://github.com/krich11/linux-serial-helper.git
cd linux-serial-helper
chmod +x serial-helper
./serial-helper
```

Or put it somewhere on your PATH:

```bash
sudo cp serial-helper /usr/local/bin/
# or
mkdir -p ~/.local/bin && cp serial-helper ~/.local/bin/
```

## Usage

### Full interactive wizard (recommended)

```bash
./serial-helper
```

It will:

1. Verify prerequisites
2. List all available serial-like devices with descriptions
3. Let you pick one
4. Let you pick baud rate
5. Launch `screen`

### Quick connect (if you already know the device)

```bash
./serial-helper /dev/ttyUSB0 115200
```

### List ports only

```bash
./serial-helper --list
```

## Inside `screen` (cheat sheet)

Once connected:

| Keys            | Action                        |
|-----------------|-------------------------------|
| `Ctrl-A ?`      | Help / all commands           |
| `Ctrl-A D`      | **Detach** (leave session running) |
| `Ctrl-A K`      | Kill the screen session       |
| `Ctrl-A H`      | Start/stop logging to file    |
| `Ctrl-A C`      | Create new window (rarely used for serial) |
| `Ctrl-A [`      | Copy/scrollback mode          |
| `Ctrl-A X`      | Lock screen                   |

To re-attach later: `screen -r`

## Common baud rates

| Device type          | Typical baud |
|----------------------|--------------|
| Many Arduinos        | 9600 or 115200 |
| ESP32 / ESP8266      | 115200       |
| Raspberry Pi console | 115200       |
| Many modems / GPS    | 9600 / 38400 |
| High speed FTDI      | 921600       |

## Permissions (very common issue)

Serial devices are usually owned by `root:dialout` (or `uucp` on some distros).

Add yourself to the group:

```bash
sudo usermod -a -G dialout $USER
# or
sudo usermod -a -G uucp $USER
```

**You must log out and back in** (or reboot) for the group change to take effect.

You can check with:

```bash
groups
ls -l /dev/ttyUSB* /dev/ttyACM*
```

## Finding a newly plugged-in device

If your device doesn't show up:

1. Run the helper — it offers a "Rescan" option
2. Or ask the helper to show recent kernel messages
3. Manually: `dmesg | tail -30` and look for `ttyUSB`, `ttyACM`, `ftdi_sio`, `cdc_acm`, etc.

Stable names live in:

- `/dev/serial/by-id/` (best — contains USB serial number)
- `/dev/serial/by-path/`

## Requirements

- Bash
- `screen` (the script will try to help you install it on common distros)
- `udevadm` (usually present)
- Optional but nice: `lsusb`

## Alternatives to screen

If you prefer something else, the helper can still help you find the right device + settings:

- `picocom -b 115200 /dev/ttyUSB0`
- `minicom -D /dev/ttyUSB0 -b 115200`
- `cu -l /dev/ttyUSB0 -s 115200`
- `python -m serial.tools.miniterm /dev/ttyUSB0 115200`

## Contributing

PRs welcome. Keep the script self-contained and friendly.

## License

MIT
