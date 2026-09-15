# ArrowKeys

A tiny, always-on-top virtual arrow-key pad for Windows — four keys, nothing else.

![ArrowKeys](ArrowKeys.ico)

## Why this exists

Windows' built-in On-Screen Keyboard works fine, but it's huge — it covers a big chunk of the screen just to give you four arrow keys. ArrowKeys is the opposite: a tiny floating pad with **only** the arrow keys, small enough to tuck into a corner of the screen and forget about.

### When it's actually useful

- **Seeking in a video player** — tap left/right to skip back/forward a few seconds (YouTube, VLC, most players map this to the arrow keys)
- **Volume control** — many media apps map up/down to volume
- **Scrolling web pages or slides** — handy on a touchscreen laptop, a couch/HTPC setup, or any time you're driving the screen with just a mouse or trackpad
- **Games and apps that use arrow-key navigation** — menus, spreadsheets, emulators, etc.
- **A broken, missing, or hard-to-reach physical keyboard** — e.g. controlling a PC connected to a TV from across the room with only a wireless mouse

Basically: any time you want an arrow key but don't want (or don't have) a full keyboard on screen.

## Features

- Only 4 keys visible — no window frame, no border, no background rectangle. The window is clipped to the actual rounded shape of the keys (`SetWindowRgn`), so it floats directly on your desktop.
- Always on top, and **never steals keyboard focus** — whatever app you're using stays active while you click the keys.
- **Left click** a key — sends a real arrow-key press (hold to keep it held down, like a physical key).
- **Right-click + drag** anywhere on a key — move the window.
- **Right-click** (without dragging) — a small menu: `Grow (+)` / `Shrink (−)` / `Close`.
- Opens centered on screen, remembers nothing — no config files, no setup.
- Refuses to open a second copy if it's already running.

## Install

**Option 1 — installer (recommended)**
Download and run `ArrowKeys_Setup.exe` from the [Releases](../../releases) page. It installs the app, adds a Start Menu entry with an uninstaller, and offers to launch ArrowKeys at Windows startup.

**Option 2 — build it yourself**
See below.

## Building from source

Requirements: Windows, Python 3.10+.

```bash
pip install -r requirements.txt
pip install pyinstaller

py -m PyInstaller --onefile --noconsole --icon=ArrowKeys.ico --name ArrowKeys ArrowKeys.py
```

The exe is produced in `dist\ArrowKeys.exe`.

To also build the installer, install [Inno Setup](https://jrsoftware.org/isdl.php), open `ArrowKeysSetup.iss` in the Inno Setup Compiler, and press **F9**.

## How it works (technical notes)

- **Key presses**: simulated with the Win32 `SendInput` API (scan codes, extended-key flag), which works reliably across browsers, media players, and most games.
- **Never steals focus**: the window is given `WS_EX_NOACTIVATE` and `WS_EX_TOOLWINDOW`, so clicking it doesn't change which window is active — the arrow-key signal goes straight to whatever app you were using.
- **Window shape**: instead of color-key ("chroma key") transparency — which is prone to edge fringing — the window is clipped to a real geometric region (`SetWindowRgn`) built from four rounded rectangles, one per key. Only the key shapes are visible or clickable; everything else is truly outside the window.
- **Single instance**: enforced with a named mutex; a second launch shows a message box and exits instead of opening a duplicate window.

## Project structure

```
ArrowKeys.py           Main application (single file, no external assets needed at runtime —
                        the key artwork is embedded as base64 inside the script)
ArrowKeys.ico           App / installer icon
ArrowKeysSetup.iss      Inno Setup installer script
User_Guide.txt          Plain-text user guide, bundled with the installer
key_up.png              Reference copies of the key artwork (not used at runtime — for
key_left.png             editing/regenerating the embedded images only)
key_down.png
key_right.png
requirements.txt        Python dependencies for building from source
```

## License

MIT — see [LICENSE](LICENSE).
