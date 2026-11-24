# InputLab v1.5.0 — Restore all for the power settings, a layout suggestion that leaves what matters alone, and fixes from your reports

**No change to any score.** A run measured with 1.4.1 and one measured with 1.5.0 are scored the same way.

A tester on our Discord changed the minimum processor state on System › Power and then asked for one undo for every
setting: they could not find the way back for everything that had changed. **Restore** on a card reaches one value,
and only on the power plan and power source in use. 1.5.0 adds **Restore all** at the top of System › Power: it lists
everything InputLab changed on your PC, on every power plan, on AC power and on battery, asks once, and puts it back as
it was before InputLab's first change. Every power change and restore is now in the log, too.

Most of the rest is System › Interrupts ("Suggest a layout", reading a layout back after the restart, the network card,
undo), the mouse on PCs with an AMD Radeon card, USB timing, the USB map and the controller live view.

## Added

- **System › Power: Restore all.** A tester asked for one way back from every setting. **Restore** on a card puts
  back one value, and only on the power plan and power source in use, so a value changed on another plan or on battery
  had no button where the tester looked. **Restore all (n)**, at the top of System › Power while anything is saved,
  lists every value InputLab changed on this PC (on every power plan, on AC power and on battery, and the power-off
  permission of every device), asks once, and puts each back as it was before InputLab's first change. The devices go
  back together, after one administrator prompt; where two devices share a hub, the hub gets back the state it had
  before the first change. A value that cannot be put back keeps its saved original and is named. Another PC's
  originals (Documents synced) are left alone.

## Changed

- **The log names every power setting InputLab changes or restores.** Only a saved-originals file that could not be
  read or written was logged; a change, a restore and a refused change were not, so "which settings did InputLab
  change?" had no answer in the log. Each change and restore is now logged with the setting, the old and the new value,
  the power plan and the power source, and a change or restore that failed with its message.
- **"Suggest a layout" never leaves what matters while gaming on Windows' default or core 0.** In the layout editor
  the button filled in a layout from fixed rules, one press away from **Write**, and a tester who wrote such a layout
  found that it felt worse. When the mouse measurement found a driver holding up your mouse's USB work, it fills in the
  layout by the rules that moves the mouse away from it, as before. Where your graphics card, network card or the USB
  controller of your mouse or keyboard sits on Windows' default or core 0, it fills in the layout by the rules and says
  that it was not measured. Otherwise it changes nothing and says so. Nothing is written until you press **Write**;
  placing devices by hand and writing them stays as it was.
- **After the restart, System › Interrupts says where Windows put your layout.** A written layout or fix was checked
  against the registry values only. Once Windows has restarted, the line under the verdict now also says where
  Windows really has each device's interrupts: "After the restart Windows uses your layout: 3 of 3 devices are where
  they were set", or which device is on another CPU, has no single processor named by Windows, or is not present.
- **System › Interrupts and System › Power read again when you come back to them.** Both were read once and after
  that only on **Refresh** or after a change made in InputLab, so a change made in the meantime by Windows or another
  program was not shown. They now read again whenever the tab comes back into view; Interrupts never does so under an
  open layout editor.
- **The saved original is on the disk before the first value changes.** Writing a fix or a layout always saved the
  original values to a file first. That file is now flushed to the disk and read back before the first registry
  value is written, the registry values are flushed as well, and an existing backup is never written over. If the
  saved copy cannot be read back, nothing is changed.
- **"Undo the change" is offered again for the change before.** After you undid your newest change, an earlier change
  of the same devices was not offered any more, although its values were in place again. It is offered again now,
  when the undo put back exactly what the earlier change had set.
- **The question before writing a layout says to undo it before switching Hyper-Threading or E-cores off.** In the
  BIOS that changes the processors' numbers, and a layout set to one of them would point elsewhere: **Undo the change**
  first.

## Fixed

- **System › Interrupts: a network card with several queues can be moved like any device.** Until now such a card was
  locked in the editor ("Spread across the cores (locked)"), and a card that a driver update had given several
  interrupts after InputLab set it to one CPU could not even go back to Windows' default: InputLab could not take
  back its own setting. Now a network card goes onto any CPU or back to Windows' default, whatever its queues, in the
  editor, in **Suggest a layout** (a core of its own, like any network card) and in the step that writes with
  administrator rights. On one CPU every queue's interrupt lands there; the question before writing says so. Only
  NVMe SSDs and SATA controllers stay locked.
- **System › Interrupts: "Undo the change" right after writing undid the change before.** Between a write and the
  reading that follows it, the button still belonged to the older change and named its date in the question. It now
  looks up the newest change at the moment of the click.
- **System › Interrupts: "Cancel restart" went away while Windows could still restart.** After **Write and
  restart…**, a cancel that failed removed the button, and when `shutdown.exe` did not answer within five seconds
  the page said that no restart was started. The button now stays while the restart may still come, and a cancel
  with no countdown running is no error.
- **System › Interrupts: a setting that could not be put back exactly was changed anyway.** A value of a kind
  InputLab does not write itself (another tool's) was saved as "not set", and **Undo the change** then deleted it;
  a processor mask longer than the 8 bytes InputLab saves left a saved copy it did not accept afterwards. A device
  that was unplugged at the moment of writing (a dock, an external graphics card) could lose its values with no
  saved copy. Such a device is now left alone, with the reason, and nothing is written. **Undo the change** also no
  longer reports success when Windows did not open a device's settings to put them back.
- **System › Interrupts: a mouse that reports a keyboard first was not found.** Some mice report a keyboard on their
  first interface and the mouse on a later one. Where Windows listed no other mouse, the tab found none unless the
  mouse was picked on the Mouse tab. Now the device whose mouse part comes first is taken as the mouse; another one
  can still be picked on the Mouse tab.
- **System › Power and Home: a power plan whose id is all zeros was not read.** Some custom Windows builds give their
  own power plan the id `00000000-0000-0000-0000-000000000000`, and Windows reads it like any other plan. InputLab
  took that id for "no active plan": the power values stayed empty, Home said "not read", saved runs carried no plan
  name, and the one-click changes said "No active power plan found". Such a plan is now read, changed and restored
  like any other; only when Windows names no plan at all does InputLab say so, and then it neither changes nor
  restores anything.
- **Mouse runs on a PC with an AMD Radeon card showed gaps that a game should not have: a second start button, "AMD
  GPU test run" (experimental).** While a Mouse Motion run measured, the graphics card sat idle, and an idle Radeon card held the
  mouse's reports back inside Windows: 2 to 5 ms, three to five times a second, so a mouse that delivered every report
  on time at the USB controller scored 4 to 5. With GPU-Z's render test drawing beside the run, the same mouse scored
  10.0. A game keeps the card busy. On a PC whose desktop an AMD graphics card draws, the Mouse page now has **AMD GPU
  test run** beside Start Quick Test. It runs Mouse Motion as set there and keeps the graphics card busy from before
  the countdown until the run ends: a second copy of InputLab draws one tiny picture a millisecond off every screen
  (its thread below normal priority; on the PC it was tried on, about 960 pictures a second and a twentieth of a
  processor core) and stops with the run. If that run is clean where Start Quick Test shows gaps, the gaps come with
  the idle card and not from the mouse. So far this was tried on an NVIDIA card only; that it does for a Radeon what
  GPU-Z's test did is still to be shown. The result, the report and "During this run" name such a run; Compare, the USB
  timing check, the Wireless page's before and after and System › Interrupts never set it against a run measured with
  the card left alone. Start Quick Test, and with it USB timing and System › Interrupts › Measure, measure as before
  with the card left alone: on the PC it was tried on, a drawing beside every run cost an 8,000 Hz mouse its clean
  runs. For trying it on another card: `IL_GPU_AWAKE=paced` shows the button on any PC, `IL_GPU_AWAKE=busy` also lets
  its run draw without a pause, `IL_GPU_AWAKE=off` hides it.
- **USB timing blamed the mouse for gaps that formed after the USB controller.** When the USB controller finished
  the mouse's reports all through a gap, but some of them never reached InputLab as a mouse move, the tab counted
  only the reports on both sides and said "late reports, all before Windows … the mouse, its radio or its sensor sent
  nothing for a moment". It now says that the reports came through the USB controller on time and never became a
  mouse move: either they carried no movement, or Windows did not pass them on. Such a gap is yellow in the chart and
  on Windows' link, not orange on the mouse's, and the quality line counts those reports even under 1 %.
- **System › USB map: a controller switched off in Device Manager was "no root hub found".** Its sockets don't work,
  and the note now says so: "switched off in Device Manager or not started, so its sockets don't work".
- **Controller live view: D-pad Up was shown as held.** On a controller that sends its D-pad in a report of its own,
  the first report without it was read as "D-pad up", until the D-pad's own report arrived. It is read as centred
  now.
- **Controller: a live reading that had given up could stay off while shown as on.** When Windows.Gaming.Input could
  not start, or the XInput or Windows.Gaming.Input reading ended on an error, and its mode was set in that moment,
  InputLab took the ending reader for a running one: the mode stood as on and nothing was read. The reader is now
  started anew.
- **System › USB map: a device that runs at USB 3 speed is shown at it.** When InputLab asked a hub about its ports,
  it did not say that it understands USB 3, as Microsoft's USBView and the earlier USB Atlas do. A device at 5 Gbit/s
  or more could then read as USB 2, and the rules about USB 3 devices (a receiver next to one, a USB 3 device running
  slower than it can) could not fire. Checked in the test suite, not yet with every chipset.
- **Mouse: a run imported from AleksMouseTester shows no verdict of InputLab's.** Its number read "— / 10", but beside
  it the result said "Clean run", coloured the verdict and showed a delivery quality, all worked out again from a run
  this version does not re-score. It now shows the metrics as saved, as the controller level does, and Home's last
  runs show no number for it either.
- **Settings, the USB port map and the saved power originals are never lost silently.** A settings file that could not
  be read (held for a moment by a sync tool, or damaged) gave the defaults, and they were saved over it when InputLab
  closed. InputLab now reads it again a moment later, then takes the copy of the save before, and otherwise keeps the
  file under another name before anything is saved over it. A port map that cannot be read is kept the same way, and
  the port map and `power-restore.json` are written so that a power cut right after a change cannot leave them empty.

## Honest limits

Restore all was checked in tests against power plans held in memory, not on a real PC's power plan, and has not been
run on the tester's PC before this release. It puts back what InputLab changed: a value that Windows or another program
changed has no saved original and is not touched (Control Panel › Power Options › Change plan settings › Change advanced
power settings › **Restore plan defaults** resets a whole plan). If the power plan an original was saved under has been
deleted, Windows refuses that value; it stays saved and the message names it. Test builds before 1.5 had a benchmark; it
is not in 1.5.0. If one of its runs was cut off, the README (**What InputLab changes on your PC**) says how to put the
devices back. Everything from 1.4.1 still applies — see the README (**Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing, the interrupt measurement, the interrupt fix, writing a layout and changing or restoring a device's
  power-off permission need administrator rights (Windows asks each time); everything else runs without. Start
  InputLab normally, not as administrator.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.5.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
