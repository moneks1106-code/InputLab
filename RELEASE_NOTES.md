# InputLab v1.0.0 — mouse, controller, USB map, power and the wireless doctor in one app

**The first InputLab release. InputLab merges AleksMouseTester and AleksControllerTester into one free Windows
tool: it measures how evenly your mouse's and your controller's reports arrive in Windows, shows where every USB
device hangs and what is worth checking, explains the power settings that are said to slow input (and changes
them on a click, with Restore), and helps find why a wireless mouse stutters.**

## What's in it

- **Controller** — everything AleksControllerTester 1.0.3 had: the live view, the quick tests **Stick Motion**,
  **Idle Drift**, **Button Burst**, **Trigger Sweep** and **Stick Range**, Advanced / Lab, Timeline and Compare.
- **Mouse** — a live view, **Scan (move it)**, the quick tests **Mouse Motion** (the delivery score), **Mouse
  Idle** and **Mouse Click Burst**, with Result, Timeline (every interval, a button lane) and Compare.
- **System › USB map** — every USB controller, hub, socket and device with its link speed and polling interval.
  One sentence says how things stand; a list says what is worth checking and why, with its source. No score. Re-plug
  a device and the map reads itself again and names what moved. Tell it where a socket sits and what is next to it,
  and the radio checks can use that.
- **System › Power** — USB selective suspend, USB 3 and PCI Express link power management, processor states, core
  parking, Energy Saver and whether Windows may switch your input devices off: what each does, what is actually
  known, where to change it — or one click, with the original saved first and **Restore**.
- **Wireless** — pick your receiver: its way into the PC, two questions only you can answer, the Wi-Fi band and the
  2.4 GHz crowd around you, active Bluetooth devices, the dropout pattern of your last Mouse Motion run, and a
  **before / after** of two runs, even across a socket change.
- **Import** — "Import from AleksMouseTester" and "Import from AleksControllerTester" on the Compare pages copy your
  old runs in. The old files are only read.
- **♥ Support** at the top right of every level, as before: voluntary, nothing is unlocked by it.

## Honest by design

InputLab reports **Windows host input-delivery timing** — not the device's internal scan rate or USB polling, not
click-to-screen latency. The USB map and the power view give no score: they say what is fine, what is worth
checking and what cannot be told. A change counts when the quick test before and after shows it.

## Tested hardware and limits

One controller (GameSir G7 HE, USB cable, Xbox-compatible HID + XInput) and one wireless mouse on an 8000 Hz
receiver, on one Windows 11 PC with an AMD processor. The Wi-Fi part of the Wireless level has not yet been
verified on a PC with Wi-Fi; Bluetooth devices, more controllers and more mice are not tested yet. Since Windows 11
24H2 the Wi-Fi list needs location access for desktop apps (the page says where to switch it on). The full list is
in the README (**Tested hardware**, **Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- Your data stays in `Documents\InputLab`; AleksMouseTester and AleksControllerTester keep running side by side.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.0.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
