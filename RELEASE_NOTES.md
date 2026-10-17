# InputLab v1.2.0 — Interrupts: which CPU core holds up your mouse, measured, and a fix you can undo

**InputLab 1.2.0 adds System › Interrupts. It shows which CPU core each device's interrupts use, measures during a
10-second mouse test whether a driver holds up your mouse's USB work there, and — only when it does — moves your mouse's
USB controller to another core. Reversible, and measured before and after.**

## What's new

- **A core map.** A tile per CPU core with the devices whose interrupts are expected there: USB controllers (the one your
  mouse hangs on marked), graphics card, audio, network, NVMe and SATA. Read only, no administrator rights.
- **Measure: move your mouse for 10 s.** A Mouse Motion test while Windows Performance Recorder (part of Windows)
  records the kernel's interrupts and deferred calls (Windows asks for administrator rights). You get: on which CPU your
  mouse's USB work really ran, how long it waited after its interrupt, which driver it waited behind, and whether that
  driver runs in your monitor's refresh beat. On the two PCs it was developed with, the mouse waited behind the NVIDIA
  graphics driver on CPU 0 — on a 500 Hz monitor in its exact 2000 µs beat.
- **The fix, only after a finding.** "Move my mouse's USB controller to CPU n" moves only that controller's interrupts to
  another physical core. You see the change before it happens; the current values are saved first; it takes effect
  after a restart; **Undo the change** puts them back exactly. A setting made by hand or by another tool can be reset
  to Windows' default the same way.
- **Before and after.** After the restart: "Measure after the fix", then both measurements side by side. If nothing
  measurably improved, the tab says so and points to the undo.
- **Fixed:** the mouse report speaks about mouse reports (not controller reports), fills its environment rows (VBS,
  Memory Integrity, Energy Saver, Steam, …) and names the numbers behind its problem text.

## Honest limits

The measurement and the fix are checked on two PCs (Ryzen 9800X3D, NVIDIA graphics). Each measurement and each change
needs administrator rights; a game with a kernel anti-cheat must be closed (it blocks the recording). A desktop
measurement shows less load than a game, and firmware interrupts (SMIs) are invisible to Windows. The fix needs a
restart and changes only one device's interrupt affinity — no MSI or priority settings, no boot settings. It is
offered only when the measurement finds your mouse held up; sharing CPU 0 alone is common and often harmless. Everything
from 1.1 still applies — see the README (**Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing, the interrupt measurement and the interrupt fix need administrator rights (Windows asks each time);
  everything else runs without.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.2.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
