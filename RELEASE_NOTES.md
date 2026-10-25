# InputLab v1.3.0 — Home: your PC at a glance, what else ran during a mouse run, a mouse score that tells the hand from the mouse, an interrupt layout you can edit yourself, and a window that fits any display scaling

**A feature release. It also carries the fixes prepared for 1.2.1, which was never published.**

## New

- **Home: your PC at a glance.** The app now opens on a new first tab (Ctrl+1; the other tabs move one up). A ring
  shows how many of the rated settings are fine and what to do before you measure; the hardware sits in six tiles
  (CPU, graphics card and driver, memory and its speed, board and BIOS, Windows, display). The Windows settings that
  matter for gaming, low latency and InputLab's measurements come in four groups — Power, Right now, Games and
  graphics, Mouse, clock and timer — with a status pill each: power plan, Energy Saver, USB and PCIe power saving, what
  runs now, Game Mode, Game Bar recording, GPU scheduling, windowed-game optimizations, display rate, mouse
  acceleration, Memory Integrity, the Windows clock, a timer test of its own and the tweaks it finds. Every rating names
  its source — Microsoft, Windows, a measurement with InputLab or the developer's pick — in its tooltip, and under the
  line when it is to look at or to fix; red only for what InputLab measured (a game like Apex running). The BIOS points
  (C-States, XHCI Hand-off, PCIe ASPM) say plainly that Windows cannot read them. On the right your mouse's way into the
  PC, the cores and your last runs; a click opens the full page. **Copy summary** puts the overview on the clipboard for
  a chat, without your PC or user name. Home only reads — no administrator rights, no network — and stops when you
  leave it.
- **"Before you measure".** Switching to a device tab opens a short guide: first the BIOS (C-States, XHCI Hand-off, the
  iGPU when you use a graphics card and PCIe ASPM off — the developer's recommendation from building and testing about
  50 PCs, with what each costs), then Windows (the Gaming Power plan, linked; close what you do not need, no streaming,
  overlays off, downloads paused) and how to measure on that tab (for the mouse: Scan, Target Hz, DPI — 3200 is a good
  choice at 8,000 Hz). One tick turns it off on every tab; the amber "Before you measure" button opens it any time.
- **System › Interrupts: edit the layout yourself.** "Edit layout" shows every core of your CPU with a field per
  logical processor. Drag your USB controllers, the graphics card, audio and network onto a CPU — or right-click one
  for "Move to" — and see right away whether the layout follows the rules ("✓ Follows the rules: core 0 free, your
  mouse alone on its core, graphics card on another core"). NVMe and SATA stay locked and say why. "Write and
  restart…" saves the current values of every changed device first, writes all of them together and restarts Windows
  after your confirmation (10 seconds, "Cancel restart" stops it); "Write only" leaves the restart to you; "Undo the
  change" puts every device back exactly.
- **Suggest a layout.** Fills the editor by those rules — your mouse's USB controller on a core of its own (a P-core on
  Intel hybrid CPUs, the V-Cache CCD on a dual-CCD X3D), the graphics card on another with its HDMI audio, other input
  devices on their own cores, the rest together on a quiet core, core 0 left to Windows — with a reason for every
  device. What already fits stays. After the restart, a measurement is compared with the one before.
- **Layout check on Interrupts.** Under the core map, whether your mouse's USB controller has its core to itself or
  shares it — with the graphics card, with core 0, with other devices — and whether it sits on an E-core or on the CCD
  without the V-Cache. It reads the settings only; only a measurement judges.
- **The report rate and the gaps, named.** The interval plot's title says "8,000 Hz class · 7,995 reports/s · 99.93 %
  of the slots"; the plot draws the 1st–99th percentile as a band with its median, and gaps and bursts as points by
  kind (late, slow movement, other).
- **Mouse › Result: "During this run".** Under "What to do", what else ran on your PC during the run — CPU, DPC and
  interrupt time (also on the CPU your mouse's USB controller is expected on), the busiest programs, the programs that
  started or ended (a start during the run is marked on the interval plot at its second) — and InputLab's own share:
  its CPU time (counted in the CPU's cycles), the garbage collections in the window, the paused live view, a trace or none. Read right before and
  right after the run, never during it; no administrator rights. It says "at the same time, not the cause" and changes
  no score. A check with and without it found no visible effect on the measurement.

## Changed

- **Mouse score v2 for new mouse runs.** A gap where your mouse had nothing to send — it moved too slowly for its rate
  and DPI — no longer counts against the mouse; every other gap counts by its length. A run with more than 1 % of such
  gaps reads "Limited: moved too slowly" and tells you how fast to move or to raise the DPI. The gap cap now eases in
  instead of dropping from 10 to 7 at once. Your runs saved with an older version keep their score; Compare marks them
  "· v1" and never ranks them against new ones.
- **Mouse result view: more room for the plot.** About 70 % of the width for the plot and the breakdown. The plot's two
  lines are named: the red "target 125 µs" and the dashed green "most common 127 µs".

## Fixed

- **Mouse: "Delivery rate (host)" is the USB rate.** It was 1 ÷ the most common interval, and on some PCs the
  intervals split into two peaks 6 µs either side of 125 µs (8,403 Hz at 8 kHz). It is now the USB rate the mouse's reports arrive on
  (8,000 Hz at 8 kHz), and gaps are counted against that slot: a missing report is always a gap, a slightly late one
  never. Of 291 saved runs checked, 9 would have scored differently. Runs saved before keep their values and scores.
- **Display scaling: nothing is cut off any more.** At 125–200 % (Full HD, 1440p, 1366 × 768 laptops) pages lost parts
  ("What to do", the controller's tiles, the last rows of the score breakdown) and text was cut inside its own box,
  because the fonts grew but the fixed sizes did not. Every size now follows the display's scale, a page with too
  little room scrolls instead of squeezing, dialogs never reach past the screen (the "Got it" button of "Before you
  measure" was below a 1366 × 768 screen's edge), and the plot's lines grow with the scaling. At 100 % nothing changes.
- **A mouse at its target rate no longer "differs" from it.** A clean 8,000 Hz run with most intervals at 127 µs read
  "differs from the selected target" and "Delivery is limited". The text now says "differs" only when the mouse really
  does not run at its target, and otherwise explains the number. Scores are unchanged.
- **USB timing: Export CSV asks where to save** and says where the file went.
- **"The fix is not active any more" says what happened** — removed by a BIOS, chipset or driver update, or set again
  outside InputLab — and names the USB controller by the mouse on it.
- **Interrupts: a keyboard is a keyboard** (an analog keyboard with a mouse function, like a Wooting, was listed as a
  mouse), and **an overwritten change never comes back** on Undo.

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing, the interrupt measurement, the interrupt fix and writing a layout need administrator rights (Windows asks
  each time); everything else runs without.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.3.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
