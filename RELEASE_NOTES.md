# InputLab v1.4.0 — a driver check for your mouse's worst gaps, a fairer mouse score for fast mice, a result that says how the gaps sit, USB timing that judges Windows too, and an interrupt layout suggestion that spreads your devices over the cores

**A feature release: what users and the developer found since 1.3.0, measured and fixed.**

## New

- **Driver check: what ran on the core of your mouse's USB work in its gaps.** System › Interrupts › **Measure** now
  also follows every gap of the run (over two report periods, as the run counts its gaps: 250 µs at 8 kHz) back through
  the report that closed it — the mouse's USB interrupt, Windows' USB work (its DPC), the arrival at InputLab — and
  splits the gap over three stages: no USB interrupt came (the mouse or its receiver sent nothing, before Windows),
  the USB work waited behind another driver on the same core, or Windows passed the report on late. A driver is named
  only when its own time (interrupts nested inside it subtracted) explains the gap or the wait; a wait with nothing
  recorded says so. The card names the driver's device and installed version (NVIDIA's as NVIDIA numbers it), where
  its interrupts are set and who set them, a fix only where the maker or Microsoft documents one, and the worst gap
  gets its own sentence. **Move … off CPU n** opens the layout editor with that device on the quietest free core of
  the same measurement (on an Intel hybrid CPU a P-core for a graphics card and an E-core first for the rest, on a
  dual-CCD X3D the CCD without the V-Cache first); nothing is written before **Write**. The next measurement of the
  same mouse compares the gaps held behind a driver and the worst gap with this one — after a driver update as after
  a layout. Measured as Windows handles the reports, never as an input-lag number: a driver "coincided" with a gap
  and could have delayed each report by at most the time it ran there. Checked against four real recordings: the
  NVIDIA driver on the mouse's core on two PCs, a LAN driver sharing that core but running 18 µs of its own, and a
  677 µs wait that nothing recorded explains.
- **Where each device's interrupt setting came from.** The Interrupts table and the driver check say whether a device
  runs on Windows' default or a setting of its own, and whose: InputLab's (with the date of its change), its
  driver's (Windows' own NVMe driver sets "spread" and high priority itself), or another program's (with the date the
  setting was written). For a network adapter the card adds receive-side scaling and interrupt moderation, which place
  its work on cores the table does not show.
- **Drivers on Home.** A new group, on the right under the cores (the last runs moved under the BIOS card, so both
  columns end about together), lists the last measured driver check — the only rating there: "No driver held up
  your mouse", or the driver that did, with a click to System › Interrupts — then the graphics, network and Wi-Fi
  drivers and the USB controllers' as installed (provider, version — NVIDIA's as NVIDIA numbers it, also on the graphics
  tile — and date), and the network adapters' power saving as their drivers have it set (Energy-Efficient Ethernet,
  selective suspend, the vendors' own switches such as Green Ethernet, whether Windows may turn the adapter off).
  InputLab does not look up whether a newer driver exists — it has no network access — and a version or a power
  setting alone says nothing about the mouse, so these lines carry no colour; the power-saving line says where to
  switch a setting off, and to measure before and after.
- **Your mouse's path.** Home › Drivers and the driver check name every driver your mouse's reports pass through,
  from Windows' mouse driver down to the USB controller (mouclass › mouhid › HidUsb › USBHUB3 › USBXHCI on most
  PCs), read from each device's driver stack. A driver another program put there — a USB capture filter such as
  USBPcap (often installed with Wireshark), a mouse tool, a maker's software — is named with its file's product and
  version and the program it came with: it sees every report, and its time counts inside Windows' USB work, so the
  driver check cannot show it apart. Shown, not rated: keep it if you use it; to see whether it costs anything,
  measure, remove it, restart Windows and measure again.
- **System › Interrupts shows a measurement as pictures.** The result used to be half a page of sentences and
  figures. Now: a meter for the waits over 50 µs against the measurement's own line (0.3 %) with the run's key
  figures; **What this means**, one line per finding in plain words with what you can do (a driver on your mouse's
  core, gaps your mouse sent nothing in, reports Windows passed on late, a driver from another program in the path);
  the USB work's waits as one stacked bar, the run as a wave (the longest gap in each 100 ms, every gap over
  two report periods as a dot in the colour of what happened), what the gaps were as a donut, what ran on your
  mouse's core as bars, the time each CPU spent in interrupts and DPCs, and your mouse's path as chips. A comparison
  before and after a change shows paired bars. Every sentence, the worst gaps as a table and how it was measured are
  one click away under **Details**. The core map sits in a card with a legend: each core shows its devices as pills,
  "free" when it holds none, and after a measurement its time in interrupts and DPCs as a bar. The table names
  every device's driver as installed ("Windows' own", "NVIDIA 617.42", "Realtek 1125.31.50.603"), audio and NVMe too.
- **USB timing judges Windows too.** "Clean all the way" used to mean only that no report came four report periods
  and 1 ms late; a run in which Windows held half of the reports for 0.25 ms or more still got it. Now each report's
  hand-over is compared with the run's fastest. When 10 % of the reports wait more than half a report period longer
  (62.5 µs at 8,000 Hz, 125 µs at 4,000 Hz, 250 µs at 2,000 Hz and slower), the tab says "Windows holds many reports"
  (from 1 %: "some reports"), with the share, the fastest and the longest hand-over, and that this is where they
  waited, not why; the chain shows "fastest … · … held > 62.5 µs" in yellow or red. Slots in which the USB controller
  got nothing at all are named from 0.5 % on ("the mouse left 1.8 % of its slots empty"); such a run is never "clean".
- **USB timing finds a hold in a fixed part of every millisecond.** When Windows passes nothing on during the same
  stretch of every millisecond, the tab says how long ("at least 400 µs"). When the run can tell, it also says which
  clock that rhythm keeps: the PC's own (a timer, driver or program that wakes every millisecond) or one of its own
  (more likely a device such as audio or network, or a changed timer source).
- **USB timing shows the USB reports that never became a mouse move.** The quality line counts them, the chain names
  the mouse's cadence at the USB controller from all of its reports, and the CSV export has a new last column
  `source`: `report` for each report as before, `usb_only` for such a USB report.
- **The mouse result says how the gaps sit.** When a run has gaps, the result says whether the slots they skipped were
  made up for ("the reports came late rather than missing") or went missing, and whether half of the gaps fell within
  one stretch of the run, and when, or were spread over it. Timing spread and bunched reports are named apart, with
  their numbers; bursts that only make up for late reports no longer count as bunching. At 8,000 Hz the result also
  says that bunched reports were held on the PC side: a mouse hands over at most one report per 125 µs slot, and these
  came less than half a slot apart. That is where they waited, not why, and the result says so: if another mouse on the
  same port runs clean, the cause lies with this mouse or its receiver. "What to do" names what limits the run (the gaps alone, the timing spread alone,
  or which of rate, spread and gaps) instead of always asking to check the target rate, the timing spread and the tail
  gaps.

## Changed

- **A measurement never runs on every device at once.** A quick test started with "All mice" or "All controllers" in
  the picker measured every device: a second mouse's or controller's reports then mixed into the run or made it
  invalid. Now the quick tests, USB timing and the interrupt measurement pick the device first: the only one connected,
  or, with more, the one you click or move (mouse) or press a button or move a stick on (controller), asked on the
  measurement overlay ("Which mouse?"). The pick ends before anything of the run starts (the recording and its
  administrator prompt, the system readings, the countdown), so the measured window holds only the measurement. Esc
  cancels; when no mouse, or more than one, moved within 5 s, nothing is measured and the message says what to do. The
  pick stays in the picker for the next run; "All mice" and "All controllers" remain for the live view. A virtual
  controller (Steam Input, DS4Windows and similar tools) is never picked or measured, even when picked in the list;
  when only such a pad reacts, the message says so. With a Steam Controller or Steam Deck connected, which Windows sees
  only through Steam Input's virtual pad, it says that InputLab has no physical pad of it to measure.
- **Mouse score v3 for new mouse runs.** Under v2 an 8 kHz mouse that skipped single 125 µs slots and made up for them
  was capped like a 1 kHz mouse missing whole milliseconds: from 0.5 % of such slots the score sat at 5.0, however good
  the rest of the run was. v3 caps hold-ups — reports late by more than one slot and at least 1 ms — and a mouse that
  fills fewer than 95 % of its rate's slots (the "cadence cap"). The gap cap keeps falling below 5.0, to 3.0 at 2 % of
  the slots, so a bad run no longer looks like a mildly bad one. The rows and their weights stay as they were: skipped
  ticks still cost the Stutters row. At 1,000 Hz and slower v3 scores like v2 up to 0.5 % of the slots. Your runs saved
  with an older version keep their score; Compare marks them "· v2" or "· v1" and never ranks them against new ones.
- **"Suggest a layout" spreads your devices over the cores.** Network, audio and USB4 chips get a core each, the USB
  controllers without a mouse or keyboard share one, and the graphics card's HDMI audio gets its own core when one is
  left. Before, all of them went onto one "quiet" core, often the CPU's last thread, while other cores stayed empty.
  Every device now goes onto the first thread of a core, never its second one. On a Ryzen with two CCDs and 3D
  V-Cache, the graphics card and the rest go onto the CCD without the V-Cache; the mouse stays on the V-Cache CCD. A
  layout that already follows the rules stays as it is.
- **The history keeps 1,000 runs instead of 300** (mouse and controller together), is read about 40 % faster, and
  Compare and Wireless read it in the background: the window no longer freezes, the table keeps the last reading until
  the new one is ready, and no reading runs while a quick test measures.

## Fixed

- **A device switched off in Windows stood on its core.** System › Interrupts read every device's interrupt setting,
  also one switched off in Device Manager, and placed it on the core that setting names, although it has no
  interrupts. Now InputLab asks Windows whether a device runs: one switched off stands on no core, the table says
  "off: switched off in Windows", the core map lists it under "Switched off in Windows", and the layout editor
  never moves it.
- **Home rated a second display kept at a lower rate on purpose.** With a 360 Hz display and a second one at 60 of
  75 Hz, the Display rate line turned yellow ("60 of 75 Hz"), as if the PC had a 60 Hz display. Now only the fastest
  display is rated, the one games run on; every display is listed by its own name with its rate ("AORUS FO27Q3:
  360 Hz, its highest, your fastest · 27B2G5: 60 Hz (offers 75 Hz)"), the others without colour.
- **Home said "Memory Integrity is on" when only its switch was set.** Windows can keep Memory Integrity off although
  its switch is on (a restart still to come, virtualization off in the BIOS, a driver that blocks it); msinfo then
  lists it as not running. Home now asks Windows' Code Integrity whether it runs: "On" only then, otherwise "Set to on,
  not running" with where to see why.
- **Home said "Not set" for GPU scheduling (HAGS) that Windows runs.** Windows writes the HAGS switch only once someone
  flips it; until then the graphics driver's default decides, so Windows › Display › Graphics showed "On" where Home
  said "Not set". Home now asks the graphics adapters whether HAGS runs: "On (the driver's default)", "Off", "Set to
  on, active after a restart" or "Not offered by the graphics driver". Windowed-game optimizations without a choice of
  yours now say that Windows' default applies and where to see it.
- **USB timing could pair every report with the USB report before its own.** When Windows held every other report, the
  shown hand-over grew by one report period (a 2 kHz run showed 516 µs where Windows took 16 µs). The pairing now
  weighs the hand-over itself as much as its smoothness.
- **Core parking on Intel hybrid CPUs** has two lines, "· E-cores" and "· P-cores", each with its own button and
  Restore; before, a hybrid CPU could show parking for the wrong cores. **On a Ryzen X3D with two CCDs** the line says
  that AMD's chipset driver parks the CCD without the V-Cache while a game runs, and no longer offers to turn it off.
- **Ryzen AI 300 laptops were taken for a dual-CCD X3D** and were told about a V-Cache CCD they do not have.
- **The USB map showed a Nollie lighting controller as a keyboard**, because it also reports a keyboard function; it is
  now a lighting controller by its name. PCI locations read in English whatever language Windows writes them in, and
  controllers sort by their numbers.
- **The XInput cross-check read like InputLab had missed reports** ("Missed packets 5488" where Steam Input's packet
  counter ran ahead of the changes seen). It now names packet numbers and changes seen, and the row is "Packet numbers
  not seen". No "XInput f_nom 0.0 Hz" any more.
- **Wireless said "No dropouts" for a receiver that left 1.8 % of its slots empty.** A gap now counts from four
  report periods and 1 ms (under 3 ms only when nothing is made up for right after), and slots left empty, neither made
  up for nor at slow movement, count from 0.5 %.
- **Texts that pointed the wrong way:** "What to do" for a run limited by spread and gaps asks for the cross-check
  with another mouse on the same port; USB timing names a mouse that sent slower than the set rate and says "most
  likely not Windows' timer"; its measurement check names a clearly wider middle spread; Home's timer tooltip says a
  program may still hold a finer timer for itself.
- **Stick Motion said the sticks covered too little of their range when they had:** when only too few reports
  changed, it now says to move the sticks faster.
- **Smaller ones:** the "limited by …" line sits under the Delivery quality bar; the interval plot's legend gives every
  share two decimals; the "On-target share" tooltip says intervals and that the row is not part of the score; the mouse
  live view turns amber only above 1 % buffered reports; the controller's status line names the right buttons.

## Honest limits

Mouse score v3 was checked against the developer's history of 294 saved runs: 190 unchanged, 91 higher (mostly 8 kHz
runs that sat at 5.0 for single skipped ticks), 10 lower (mice that filled 82–94 % of their slots, and one disturbed
run). USB timing's Windows judgement comes from one user's 2 and 4 kHz runs and the developer's 8 kHz runs. Home's
Memory Integrity line was checked against Windows' own reading on one PC, where it is off; its GPU scheduling line on
one PC, where HAGS is switched on (a driver's default was seen on a user's PC, through Windows' own page). Everything
from 1.3 still applies — see the README (**Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing, the interrupt measurement, the interrupt fix and writing a layout need administrator rights (Windows asks
  each time); everything else runs without.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.4.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
