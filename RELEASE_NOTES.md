# InputLab v1.1.0 — USB timing: your mouse at the USB controller and at the app, on one clock

**InputLab 1.1.0 adds USB timing to the Mouse level. On its USB timing tab, press "Start USB Timing" and see every report twice: when
Windows' USB stack received it, and when it reached InputLab — the way a game gets it. Every hiccup is marked where it
started: before Windows (the mouse, its radio or its sensor) or inside your PC.**

## What's new

- **USB timing (Lab).** On the Mouse level's new **USB timing** tab, the start button reads **Start USB Timing**: the
  quick test set in the setup row, and it also records the USB stack's own completion events (Event Tracing for Windows; Windows asks
  for administrator rights for each such test). On the other tabs Start Quick Test stays without it, so your
  score is measured undisturbed. Afterwards every report is paired with its USB completion — on the
  developer's 8000 Hz receiver, every single one.
- **One clock, three lanes.** The time between reports at the USB controller, how long Windows took to hand each report
  to the app, and the time between reports at InputLab. Each lane centres its typical value, the numbers follow the
  zoom, and single reports appear as dots when you zoom in. Zoom buttons (whole run down to 1 ms), wheel zoom down to
  half a millisecond, Shift+drag to zoom to a range, an overview strip, hover for one report's numbers, CSV export.
- **The chain in one picture:** your mouse › USB controller › InputLab, with the numbers on each link
  ("every 125 µs", "hand-over 50 µs typical, 99 % within 95 µs").
- **A measurement check.** The recording makes Windows do extra work on the path InputLab measures. So every run with
  it is compared with your latest runs of the same mouse and test without it, and the tab says plainly whether the
  recording changed anything. Such runs are marked and kept apart in Compare. The trace asks Windows only for the events
  it reads, which halves its extra work.
- **Fixed:** mouse sessions now name their real capture path (Raw Input mouse); 1.0.0 labelled them with the
  controller's model. Older runs show the corrected label; measurements and scores are unchanged.

## Thanks

A big thank-you to [XBAB-Tech](https://github.com/XBAB-Tech) for pointing out how much ETW-based timing adds to a
mouse test. Their [MousePlotter](https://github.com/XBAB-Tech/MousePlotter) pairs the mouse's USB completions from
Event Tracing for Windows with Raw Input, and that is what got InputLab's USB timing started. InputLab's
implementation is its own; no code was taken.

## Honest limits

USB timing is checked with one wireless mouse on an 8000 Hz receiver. Its USB-side times come from Windows' USB stack,
not the USB cable, and it is not a click-to-screen latency. It covers USB devices on Windows' current USB stack (not
Bluetooth, not older EHCI controllers). It never changes the delivery score. Everything from 1.0.0 still applies — see
the README (**Tested hardware**, **Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing needs administrator rights for each recorded test (Windows asks); everything else runs without.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.1.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
