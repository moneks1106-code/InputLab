# InputLab

**A free Windows tool that shows whether your mouse and your game controller arrive cleanly in Windows, where every USB device hangs, which power settings can get in the way, and why a wireless mouse stutters — and is honest about what that means.**

Many testers show a polling-rate number. InputLab timestamps every Raw Input report the moment Windows hands it over and shows **how steady the intervals are** — gaps, stutters, and reports Windows delivered in batches instead of one by one. For controllers it also checks the **quality of the inputs**: stick drift and noise at rest, stick range and circularity, trigger range and resolution, button chatter. Around the measurement it shows **this PC's USB map**, the **power settings** that are said to slow input, and a **wireless doctor** for receivers.

> **Honest by design.** InputLab reports **Windows host input-delivery timing — not** the device's internal scan rate or its USB polling, **not** click-to-screen latency, **not** a verdict on your hardware. Latency numbers are clearly labelled lower bounds or relative differences between Windows input paths. The USB map and the power view give **no score**: they say what is fine, what is worth checking and what cannot be told, with the reason. Results describe one device, on one PC, under one load. When a measurement isn't trustworthy, the tool says so ("—", "Not enough data") instead of glowing green.

**Version 1.3.0 — Home, your PC at a glance; what else ran on your PC during a mouse run; a mouse score that tells the hand from the mouse; an interrupt layout you can edit yourself, with a suggestion; and a window that fits any display scaling. 1.2.0 added Interrupts: which CPU core holds up your mouse, measured, and a fix you can undo.** InputLab merges AleksMouseTester and AleksControllerTester in one app. Hardware verification is still small: one controller and one wireless mouse. Please read [Tested hardware](#tested-hardware) and [Known limitations](#known-limitations) before you trust a result from another device or connection. Results from other devices are very welcome.

**Free, and it stays free.** If it helps you, you can [support it](#support) with a voluntary donation — nothing is unlocked by it.

## What it does

Five levels, one tab each at the top (Ctrl+1 … Ctrl+5):

- **Home** — the page the app opens on: your PC at a glance. The hardware (CPU, graphics card and driver, memory and its speed, board and BIOS, Windows, displays), the Windows settings that matter for gaming and low latency (power plan, Energy Saver, USB and PCIe power saving, what runs now, Game Mode, Game Bar recording, GPU scheduling, windowed-game optimizations, display rate, mouse acceleration, Memory Integrity, the Windows clock, a timer test and the tweaks found), the BIOS points Windows cannot read, and small views of the USB map, the cores and your last runs. Every rating names its source — Microsoft, Windows, a measurement with InputLab or the developer's pick; Home only reads and changes nothing. **Copy summary** puts the overview as text on the clipboard, without your PC or user name.
- **Controller** — a live view of your controller with every button, both sticks with trails and a live circularity outline, both triggers, the four back-paddle positions, and how many reports per second Windows delivers right now. **Scan (press a button)** picks the controller you touch; with only one controller connected, it is selected for you. Button labels: Auto / Xbox / PlayStation / Nintendo / Generic — the PlayStation layout draws a DualSense-style pad (d-pad top left, symmetric sticks, touchpad, PS / Create / Options / Mute), and Auto picks it for Sony controllers. The live view pauses while a test runs, so it never disturbs a measurement.
  - **Separate LT/RT** — Xbox-style pads report both triggers on one shared axis in Windows' generic input path; one click turns on XInput so both triggers show separately.
  - **Assign buttons…** — a generic / D-input pad with extra buttons ("Extra 1" … "Extra 8") can map them to L4, R4, L5, R5, Share, Guide, Touchpad or Mute. A live dot shows which physical button is which; the mapping is saved per controller model and used in every capture and analysis.
  - **Quick Tests** — guided tests of 10 to 60 seconds, each with its own countdown and instructions:
    - **Stick Motion** (10 / 20 / 30 s) — rotate both sticks; gives the plain-language **delivery score** (report rate, jitter, gaps, batching) and host-side latency lower bounds.
    - **Idle Drift** (10 / 30 / 60 s, default 30) — put the controller down; stick noise, centre offset and drift, trigger rest noise, ghost presses.
    - **Button Burst** (10 s) — tap A as fast as you can; press durations, mash rate and chatter — and, on Xbox-compatible pads, which Windows input path saw each press first (a relative difference, not input lag).
    - **Trigger Sweep** (15 s) — pull the triggers slowly; range reached, resolution, start deadzone.
    - **Stick Range** (20 s) — circle each stick along its edge; circularity, outer range, centre repeatability, resolution.
    - **Manual capture** — start / stop; delivery statistics without a score.
  - **Advanced / Lab** — the full metric breakdown with explanations, graphs of the delivery timing and of your stick, trigger and button inputs (double-click for full screen), host-side latency lower bounds and, for Xbox-style pads, a cross-check against XInput.
  - **Timeline** — the finished run's sticks, triggers and buttons as the raw reported values (no smoothing) over its delivery intervals, so a gap can be read against what you were doing at that moment; **Show longest gap** jumps to it.
- **Mouse** — a live view (reports per second, the last two seconds of intervals, the motion trail), **Scan (move it)** to pick the mouse you move, and three quick tests in the same quiet measurement window as the controller's: **Mouse Motion** (10 / 20 / 30 s, the delivery score), **Mouse Idle** and **Mouse Click Burst** (statistics, no score). Result, Timeline (every interval, button presses on their own lane) and Compare, as for controllers. Your target rate and DPI are noted with each run. The result names the report rate ("8,000 Hz class · 7,995 reports/s") and the gaps by kind (late, slow movement, other); a run in which the mouse moved too slowly for its rate and DPI reads **Limited: moved too slowly** and says how fast to move. Runs saved by an older version keep the score they were saved with. **During this run**, under the result, shows what else ran on your PC at the same time — CPU, DPC and interrupt time, the busiest programs, a program that started during the run (also marked on the interval plot) — and InputLab's own share; it is read right before and right after the run, never during it, and it is context, never a cause or part of the score.
  - **USB timing (Lab)** — on its tab the start button reads **Start USB Timing**: it runs the quick test set above and also records when Windows' USB stack finished each report (Event Tracing for Windows; Windows asks for administrator rights each time). On the other tabs Start Quick Test stays without it, so your score is measured undisturbed. One clock, three lanes: the time between reports at the USB controller, how long Windows took to hand each report over, and the time between reports at InputLab — what a game reads. Every late report is marked where it started: before Windows (the mouse, its radio or sensor) or inside Windows (your PC). A measurement check compares the run with your runs without the recording and says whether the recording disturbed it; such runs are kept apart in Compare. **Export CSV…** saves the report-by-report times where you choose and says where they went.
- **System** — three views:
  - **USB map** — every USB controller (probably in the CPU or the chipset, said as an estimate), every hub, socket and device, with the link speed and how often the bus polls each device. One sentence on top says how things stand ("No problem found in how your devices are connected", "1 thing worth checking"), a list says what to check and why, with its source. Re-plug a device and the map reads itself again and names what moved. Click a device to tell the map where its socket sits and what is plugged in next to it — Windows cannot know that, and the radio checks need it.
  - **Interrupts** — a tile per CPU core with the devices whose interrupts are expected there (USB controllers with your mouse's marked, graphics, audio, network, SSDs). **Measure** runs Mouse Motion 10 s while Windows Performance Recorder records the kernel's interrupts (Windows asks for administrator rights): on which CPU your mouse's USB work really ran, how long it waited, behind which driver, and whether that driver runs in your monitor's beat. Only when it finds the mouse held up, it offers to move the mouse's USB controller to another core — the current values are saved first, it takes effect after a restart, and **Undo the change** puts them back; afterwards **Measure after the fix** shows before and after side by side. A **layout check** under the tiles says, from the settings alone, whether your mouse's USB controller has its core to itself or shares it — with the graphics card, with core 0's defaults, with other devices — and whether it sits on an E-core or on the CCD without the V-Cache; only a measurement judges it. **Edit layout** lets you drag the USB controllers, the graphics card, audio and network onto the cores yourself (NVMe and SATA stay locked), **Suggest a layout** fills it by the same rules with a reason for every device, and **Write and restart…** saves the current values first, writes all of them together and restarts Windows after your confirmation; **Undo the change** puts every device back.
  - **Power** — the settings that are said to slow input (USB selective suspend, USB 3 and PCI Express link power management, processor states, core parking, Energy Saver, whether Windows may switch your input devices off), each with what it does, what is actually known about it, and where to change it yourself — or with one click: the original is saved first and **Restore** puts it back.
- **Wireless** — the wireless doctor: pick your receiver and see its way into the PC (controller › socket › hubs › receiver), answer two questions (where is the socket, what is right next to it), and read what fits: the receiver's USB checks, your PC's own Wi-Fi band and how crowded 2.4 GHz is around you, active Bluetooth devices, and the dropout pattern of your last Mouse Motion run (short gaps in an otherwise steady stream fit radio dropouts; uneven timing without gaps fits the PC side). **Before / after** puts two runs side by side, even when the receiver moved to another socket in between.
- **Compare** — every run is saved locally and ranked only against comparable runs (for controllers: the **same controller, connection, settings, PC and test**; for mice: the same mouse, test and duration), so you can find which setup delivers most cleanly on *your* PC. Compare says how many runs it used and why others were left out. Each level clears only its own history.
- **Measurement regimes** — mark a controller run as Host Baseline, Desktop Load or Gaming Load Stress; runs under different loads are never ranked against each other.
- **Export report** — a Markdown report of a run, ready to share. Every Quick Test is also saved automatically as a session JSON with a `_metrics.csv` next to it, in `Documents\InputLab` (format: `EXPORT_SCHEMA.md` in the GitHub repository).
- **Import** — "Import from AleksMouseTester" and "Import from AleksControllerTester" on the Compare pages copy your old runs in. The old apps' files are only read, never changed.

## The core idea

> It doesn't reward the highest nominal report rate. It rewards the highest rate that stays **stable** under real host delivery.

A "1000 Hz" controller or an "8000 Hz" mouse that delivers raggedly can score *below* a steady lower-rate one — and the tool shows you why, with the numbers. The delivery score comes only from Stick Motion (both sticks moving) and Mouse Motion (the mouse moving). Input-quality results (sticks, triggers, buttons) are reported separately and never move the delivery score; their thresholds are provisional.

## Tested hardware

| Device | Connection | Windows input path | Tested |
|---|---|---|---|
| GameSir G7 HE (controller) | USB cable | Xbox-compatible HID (`IG_`) + XInput | Stick Motion many times (1000 reports/s); Idle Drift, Button Burst, Trigger Sweep and Stick Range on earlier development builds |
| A wireless mouse on an 8000 Hz receiver | 2.4 GHz receiver (USB 2 high speed) | Raw Input mouse | Mouse Motion many times (8000 reports/s), live view; USB map, Power and Wireless levels read it |
| Wooting 60HE+, a USB microphone, a Bluetooth adapter, a motherboard lighting controller | USB | — | USB map and Power view only |

All of it on one Windows 11 PC with an AMD processor. That is the whole hardware verification so far: one controller and one wireless mouse.

## Known limitations

- **One controller tested.** The PlayStation (DualSense / DualShock 4), Nintendo (Switch Pro) and generic D-input profiles are implemented but **not yet verified on hardware** — neither is **Assign buttons…** with a real pad that has extra buttons, nor the one-click **Separate LT/RT** button (switching XInput on by hand was checked).
- **Connections:** Bluetooth controllers and mice, controller receivers / dongles and USB hubs are **not yet tested**. The **Xbox Wireless Adapter** detection, and the XInput opt-in that comes with it, are **not yet verified on hardware**.
- **Xbox Elite back paddles** are read through Windows.Gaming.Input for the live view only and drawn by position (P1 upper right, P2 lower right, P3 upper left, P4 lower left) — **not yet verified on a real Elite controller**. Windows.Gaming.Input delivers only while the tester's window is in front (a Windows rule) and is never part of a measurement. The **Share button of Xbox Series controllers is not available** yet.
- **Mouse level:** one wireless mouse so far. Its quick-test results have not yet been compared side by side with AleksMouseTester 0.2.16 on the same mouse; please report differences. Sensor tests (true DPI, acceleration, smoothing) are not part of 1.0.
- **USB map:** it knows what Windows reports — controller, hub, socket number, link speed, polling interval, open audio / video streams. Whether a controller sits in the CPU or the chipset is an estimate from its PCI ids. It cannot know where a socket sits on your case or what is next to it unless you say so, and a front-panel cable shows up as a hub. It checks the wiring; the quick test measures what arrives.
- **Wireless level:** a receiver's own radio channel and packet loss are invisible to Windows; the doctor reads the timing your runs show, the receiver's path, and the Wi-Fi list Windows keeps. Since Windows 11 24H2 that list needs location access for desktop apps (the page says where to switch it on; InputLab reads no location). The Wi-Fi part has **not yet been verified on a PC with Wi-Fi** — the developer's PC has none.
- **USB timing:** checked with one wireless mouse on an 8000 Hz receiver (every report paired). It needs administrator rights for each recorded test, covers USB devices on Windows' current USB stack (not Bluetooth, not older EHCI controllers), and its USB-side times come from Windows' USB stack, not the USB cable. The recording adds work to the path it watches; the measurement check says when a run looks disturbed. Windows refuses the recording while a game's kernel anti-cheat is still loaded (seen with EA AntiCheat): close the game first.
- **Interrupts:** the measurement and the fix are checked on two PCs (Ryzen 9800X3D with NVIDIA graphics), where the mouse's USB work waited behind the graphics driver on CPU 0. It needs administrator rights for each measurement and each change, Windows Performance Recorder (part of Windows 10 and 11) and no game with a kernel anti-cheat running. A desktop measurement shows less load than a game; firmware interrupts (SMIs) are invisible to Windows and show only as unexplained waits. The fix moves one device's interrupts and needs a restart; it is offered only when the measurement finds the mouse held up. More than 64 logical processors are not supported yet. The layout editor's write and undo are checked on one PC; a full cycle — a measurement before, write, restart, a measurement after — has run once, on a PC whose mouse was not held up before either (no measurable change there).
- **Power view:** changing a device's "allow the computer to turn off this device" permission needs administrator rights (Windows asks). No published measurement shows an input-latency effect for most of these settings; the view says so and suggests a quick test before and after.
- **Import:** checked with test files in both old formats, not yet with large real histories. Raw-event files stay in the old folder.
- **Display scaling:** every page is laid out for 1920 × 1080, 2560 × 1440 and 1366 × 768 at 100–200 % and checked that nothing is cut off (a page that has too little room scrolls); on real hardware it was looked at on one 2560 × 1440 screen at 100 and 150 %. Moving the window between screens with different scaling, or changing the scaling while the app runs, makes Windows stretch it (blurry) until the next start.
- **Keep the tester in front while a test runs.** Whether input keeps arriving while another window has focus is not yet verified.
- **Latency is host-side only:** lower bounds from the measured report intervals, and relative differences between two Windows input paths. Absolute button-to-PC latency needs an external hardware rig, which is not part of this release.
- **Report rate means Windows host delivery** — not the device's internal or USB polling.
- **Thresholds are provisional:** the quality ratings will be tuned once results from many devices exist.
- **Untested so far:** two or more controllers at once, virtual controllers (Steam Input, DS4Windows / ViGEm, HidHide), 8 kHz controllers, Windows 10, Windows on Arm, and Windows display languages other than the developer's.
- **Unsigned:** Windows SmartScreen warns on the first run, and Smart App Control blocks the app (see [Is it safe?](#is-it-safe)). Some overlays or security tools that inject code into other programs can make the app close right after starting (Windows' hardware stack protection, CET) — please open an issue and name the software if that happens.

## Good to know

- **Xbox controllers** (and many Xbox-style pads) reach Windows' generic input path only in a compatibility form: both triggers share one axis there, and the Guide button is missing. The tool reads the separate triggers and Guide through XInput where it can, labels results from the compatibility path, and asks for one trigger at a time when it cannot.
- **Change-driven devices** send nothing while nobody touches them — an idle gap is not a stutter. The Controller page shows a live guess (streaming / change-driven), and the delivery scores are computed only while the sticks or the mouse keep moving.
- **Bluetooth** delivery follows the Bluetooth link, not a USB polling rate — compare Bluetooth runs with Bluetooth runs.
- **A USB 2 socket carries an 8000 Hz receiver fine** — the USB map shows the polling interval it actually got. A hub, a front panel or a USB 3 device right next to a receiver are reasons to *test*, not proven delays.
- **Steam Input, DS4Windows and similar tools** create virtual controllers, marked "not measurable". Measure your real controller, not "All controllers".

## Download & run

1. Grab the latest `InputLab-vX.Y.Z-win-x64.zip` from [**Releases**](../../releases).
2. Unzip (keep the whole folder) and run **`InputLab.exe`**.
3. **Windows 11 or Windows 10, 64-bit (x64).** Self-contained — no .NET install needed (it brings its own .NET 10). Microsoft supports .NET 10 on Windows 11 24H2 and newer; older Windows 11 versions and Windows 10 22H2 are expected to work but are untested. On Windows 11 on Arm the x64 build runs under Windows' x64 emulation — it has **not** been validated there.

Verify your download against the `SHA256` value listed on the release.

> On the first run of each release, Windows SmartScreen may say "Windows protected your PC" — because this is a small, free, **unsigned** tool, not because it's malware. Click **More info → Run anyway**. On a PC where **Smart App Control** is switched on there is no "Run anyway" — see [Is it safe?](#is-it-safe) below.

## Is it safe?

Short version: it's a small, offline tool that listens to your devices — and you don't have to take my word for it.

- **Why the SmartScreen warning?** InputLab is a free tool I don't earn anything from, and it is **not code-signed — by decision, for now**. Unsigned doesn't mean unsafe — it only means Windows has no verified publisher to attach a reputation to. SmartScreen tracks reputation per file for unsigned programs, so **every new release starts again at zero reputation**.
- **Smart App Control (Windows 11).** In its *On* (enforcing) mode, Windows blocks an unsigned program without established reputation outright — there is no "Run anyway". I do **not** recommend turning Smart App Control off for this tool.
- **No network, no telemetry.** It reads your devices' input **locally** — **nothing leaves your PC**, no internet calls, no tracking. Test history is stored only in `Documents\InputLab`, plus a small **local** error log (`%LocalAppData%\InputLab\logs`) so crashes can be diagnosed. The **♥ Support** button changes nothing about that: only your web browser opens its PayPal link, and only when you click **Open PayPal** (see [Support](#support)).
- **Listen-only toward your devices.** It never sends anything to your mouse or controller (no rumble, no settings, no firmware) and installs no drivers. Games and other apps keep receiving your input as usual.
- **Windows settings change only on your click.** The System › Power view changes a setting only when you click that setting's button; the original is saved first (`Documents\InputLab\power-restore.json`) and **Restore** puts it back. A device's power permission is set with administrator rights, so Windows asks first. Nothing else in Windows is changed.
- **Interrupts: measured only when you ask, changed only on your click.** **Measure** starts Windows Performance Recorder for the 10-second test (Windows asks for administrator rights) and writes to `%LocalAppData%\InputLab\dpc-trace`. The fix and the layout editor change exactly two values per device (its interrupt affinity) — one device for the fix, the ones you moved for a layout — only after you confirm the shown change and Windows' administrator prompt; the previous values are saved first in `%ProgramData%\InputLab\affinity-backups`, and **Undo the change** restores them. Windows restarts only with **Write and restart…**, after your confirmation and 10 seconds later (**Cancel restart** stops it). No MSI or priority changes, no boot settings.
- **USB timing runs only when you ask for it.** Only **Start USB Timing** (the start button on the USB timing tab) starts a short Event Tracing for Windows session on the USB stack (Windows asks for administrator rights), writes it to `%LocalAppData%\InputLab\usb-timing` and stops it with the test. It only observes.
- **Runs next to AleksMouseTester and AleksControllerTester.** The tools keep separate settings, history and logs and can run at the same time — for a measurement, leave the others idle. InputLab reads their history only when you click Import, and never changes it.

**Don't trust me — verify:**
- Check the download's **SHA256** matches the release — it confirms you got my exact, untampered file (for what the hash *can't* prove, see [How this was built](#how-this-was-built-transparency)).
- After unzipping, verify **every individual file**: `sha256sum -c SHA256SUMS.txt` (standard format — works in git-bash/WSL out of the box).
- `BUILDINFO.txt` in the folder records the exact source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool version the build used.
- Drag the `.zip` onto [**VirusTotal**](https://www.virustotal.com) to scan it with 70+ antivirus engines yourself.
- Block it in a firewall if you like — it never needs the internet.

## How this was built (transparency)

- **AI-assisted.** I designed and directed InputLab — the measurement methodology, the "honest instrument" rules, what to measure and how to score it — and wrote the code with heavy help from an AI coding assistant. The design and testing decisions are mine; much of the code is AI-written. Saying so plainly because you deserve to know.
- **AleksMouseTester and AleksControllerTester in one app.** The controller tester grew out of the mouse tester's host-delivery core (capture, quiet measurement window, timing statistics, delivery score); InputLab brings the mouse back into it and adds the USB map, the power view and the wireless doctor. The USB checks follow a written, source-backed rule catalogue: every check names its evidence and its limit.
- **Closed-source, by choice.** This repo is binary-only (README, license, notes, download). The source stays private for now — a deliberate choice, but honestly **it limits independent verification**: you can't read or rebuild the code.
- **What the SHA-256 does and doesn't prove.** It only confirms your download matches the file I uploaded — it does **not** prove the program is safe. Treat it as "you got the right, untampered bytes," not "it's been audited."

## Status

1.3.0 brings the mouse score v2 (runs saved before keep their score), a mouse's delivery rate read as its USB rate (8,000 Hz at 8 kHz), a wider plot with named lines, an honest rate message, and an interrupt layout editor with a suggestion; it also carries the fixes prepared for 1.2.1 — a layout check on Interrupts, USB timing's CSV export, the fix's "not active any more" message — which was never published. 1.2.0 added System › Interrupts. 1.1.1 fixed mouse names and explained a refused USB trace. 1.1.0 added USB timing on the Mouse level. 1.0.0 was the first InputLab release: everything AleksControllerTester 1.0.3 had, plus the mouse level, the USB map, the power view, the wireless doctor and the import of your old runs. The measurement pipeline and every feature above are covered by automated tests, but hardware verification is **one controller and one wireless mouse** so far (see [Known limitations](#known-limitations)), and every input-quality threshold is provisional until results from many devices exist.

## Feedback

Found a bug, or measured a device that isn't listed above? Please open an issue with:

- the device model, the connection (USB cable / receiver / Bluetooth) and, for a controller, its mode (XInput / D-input / Switch / PlayStation),
- your **Stick Motion** or **Mouse Motion** result (score and report rate),
- the exported Markdown report (**Export report**) of that run — it lists the device, the results and some PC context (no user or computer name, an anonymized device id); have a quick look before you post it.

## Support

InputLab is free, and it stays free. I build it alone, in my spare time. If it helped you, a **voluntary** donation helps keep it going and pays for testing with more devices:

**[paypal.me/AKrstic43](https://paypal.me/AKrstic43)**

- **Nothing is unlocked by donating** — every feature stays free for everyone.
- In the app, the **♥ Support** button at the top right of every level opens a small window with the same link: **Open PayPal** opens it in your web browser (the tester itself sends nothing), **Copy link** copies it. It never pops up by itself.
- On GitHub, the **Sponsor** button at the top of this repository leads to the same link.

Thank you either way — a bug report or a result from a device that isn't listed above helps just as much.

## License

Free to use. Binary-only release — see the license terms (`LICENSE.txt` in the download). Not open source. Third-party components bundled with the app (the .NET runtime) keep their own licenses — see `THIRD-PARTY-NOTICES.txt` and `DOTNET-RUNTIME-THIRD-PARTY-NOTICES.TXT` in the download.

Xbox, DualSense, DualShock, Nintendo Switch, Steam, Wooting and other product names are trademarks of their respective owners. InputLab is an independent tool, not affiliated with or endorsed by any of them.

---

*InputLab is a diagnostic instrument for input delivery, input quality and the USB path behind it — not a marketing-number generator.*
