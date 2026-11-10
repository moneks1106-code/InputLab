# InputLab v1.4.1 — a fix release: layouts that could not be written, Restore on System › Power, a quieter mouse test, and a README that reads like a person wrote it

**No new features, no change to any score.**

What a review of the whole code found after 1.4.0 and what users reported since: a layout that could
not be written on a PC with a process manager, Restore on System › Power after a change of power plan, a mouse test
that ran with the controller side still working, and a README that reads like a person wrote it. Nothing in the
scores changes: a run measured with 1.4.0 and one measured with 1.4.1 are scored the same way.

## Changed

- **The mouse report no longer prints the Raw Input path.** The exported Markdown report of a mouse run carried the
  mouse's device path ("Mouse path"), which names the device instance: a serial number where the mouse has one, and
  a Bluetooth mouse's address. The report is meant to be posted, and the README promised an anonymized id, as the
  controller report always had. It now shows **Mouse VID:PID** (the model, with the interface where the path names
  one) and **Mouse id (anonymized)**, the same 16-character id the raw-event file uses. The run's own files on your
  PC are unchanged.
- **The README is rewritten.** Shorter, and in the order a new user needs: download and run, what you get, what to
  do before you measure, how to read a result (a low score is no verdict on the mouse; why 8 kHz is hard; why other
  testers show "8000" for the same run), then safety, limits and the details of every tab. It says what users' runs
  have taught the tool since the first release. The long list of versions is gone; the release notes carry it.
- **The README lists what InputLab changes on your PC.** A new section names every place the app writes to Windows
  (the power-plan values, a device's power permission, the two registry values of a device's interrupt affinity), when
  it does, where the original is kept and which button puts it back, the files it writes, what it never does, and a
  note for laptops.
- **Home: "Hypervisor on" names everything that keeps a hypervisor running.** The hint under Memory Integrity named
  only Virtual Machine Platform, so with that feature off and "Hypervisor on" still shown the line looked wrong. It is
  right: Hyper-V, Windows Hypervisor Platform, Windows Sandbox and virtualization-based security each keep one running
  too. The hint now lists them, says when this PC has virtualization-based security set to on, and points at msinfo32;
  Microsoft's sentence about Virtual Machine Platform stays.

## Fixed

- **System › Power: Restore after a change of power plan.** The saved original was kept per setting only. After a
  switch to another power plan (or from mains to battery) a click on a change wrote the new plan without saving its
  value first, and Restore then put the old plan back and said "Restored" while the active plan stayed changed. Now
  an original belongs to one setting of one power plan on one power source of one PC: every plan gets its own
  original before anything is written, a card offers Restore only for the plan that governs now, and the summary
  line says when originals of another plan are waiting ("switch back to it to restore"). Originals saved by 1.4.0
  and earlier still restore, on their own plan. With Documents synced between two PCs, one PC no longer offers to
  restore the other's value.
- **System › Power: a saved-originals file that could not be read was replaced.** A damaged or half-written
  `power-restore.json` counted as empty, and the next change saved over it. Now a change first keeps such a file
  under another name (`power-restore.json.unreadable-…`) and says so; a file Windows cannot open stops the change,
  and Restore never acts on a file it cannot read.
- **System › Power: a device id from the saved-originals file could reach the administrator command line.** Restore
  for "Allow the computer to turn off this device" passed the device ids saved in `power-restore.json` to the
  elevated Windows PowerShell with only the plain apostrophe escaped; PowerShell also ends a text at typographic
  quotes. A program already running as you could have edited that file so that your click on Restore, and your yes
  to the UAC prompt, ran its command as administrator. Now only a Windows device id (printable ASCII, at most 200
  characters, no double quote) is accepted, when the file is read for a restore and again when the command is built;
  anything else is refused and nothing starts.
- **System › Power: a refused or cancelled change left a Restore behind.** When Windows refused a value, or the UAC
  prompt was cancelled, the original saved for that click stayed and the card offered to restore what had never
  changed.
- **System › Interrupts: "Refused: this PC has no CPU 12" on a PC that has it.** The layout editor reads this PC's
  processors, but the step that writes with administrator rights counted the processors its own process may use.
  With InputLab limited to some of them (a process manager such as Process Lasso, most of all when InputLab itself
  runs as administrator) it refused a CPU the editor had just offered, and nothing could be written. The step now
  counts the PC's processors.
- **Mouse live view: half of "last 2 s" was empty at 8 kHz.** The live view kept the newest 8,192 reports, one second
  of an 8 kHz mouse, so the left half of its two-second sparkline stayed empty and "median / p99" covered one second.
  It keeps 16,384 now.
- **Mouse live view: "reports / s" counted every mouse.** With one mouse picked, a second mouse, a touchpad or
  injected input moving at the same time added its reports to the number under the picked mouse's name and broke up
  its intervals. The card counts the picked mouse only; "All mice" counts all, as before.
- **A manual capture had no end.** The Lab's free-running capture kept recording until Stop. Past 1,000,000 reports
  (about two minutes at 8 kHz) its buffer grew while the capture thread waited, which put a gap of its own making
  into the recording, again at every doubling, and a capture left running ended in an out-of-memory crash. It now
  stops by itself at 1,000,000 reports and says so. While it runs, the level cannot be changed (on another level
  its Stop button was out of reach).
- **A mouse test ran with the controller side still working.** With a controller connected, a mouse quick test (also
  USB timing and the interrupt measurement) stopped only the Mouse level's own live view: the controller level's
  XInput polling for its live view (up to 2,000 times a second), its Windows.Gaming.Input thread and its 400 ms
  refresh kept running through the measured window. A mouse test now pauses them from the countdown on and brings
  them back when it is over, as a controller test always did for itself.
- **Wireless read the whole history during a test started from it.** The Wireless level's first read (and the one
  after a device was plugged in) loaded every saved run on a worker of its own. A test started with its "Run a quick
  test" button could have that read inside its measured window. The read now keeps out of a quick test like every
  other read of the history: it waits for the test's end, and one already running is stopped and read again.
- **Wireless re-read everything, again and again.** A missing pair of braces (since 1.0.0) told the Wireless page
  that devices had changed on every window message, not only when something was plugged in or out: it read the
  device tree, the hubs, Wi-Fi and the whole run history on every visit, and kept doing so while it was shown.
- **The Target Hz of an old run became the default.** Closing InputLab while a finished or loaded run was on screen
  saved that run's Target Hz instead of the one picked for the next test, so the next start opened with the old
  target (run at 1000, pick 8000, close: 1000 was back).
- **Mouse Compare named a sweet spot with one tested rate.** A run saved with the Target Hz box empty counted as a
  rate of its own, so two clean runs at one rate plus one without a target read "Sweet spot for this cohort". Such a
  run names no rate now, as on the controller level.

## Honest limits

None of the fixes was measured on other PCs than the developer's before this release. The layout fix ("this PC has no
CPU 12") was found on a user's PC and is corrected in the code and in a test; it has not been run on that PC. Whether a
mouse test with a controller connected reads calmer now has not been measured. Everything from 1.4.0 still applies —
see the README (**Known limitations**, **Is it safe?**).

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing, the interrupt measurement, the interrupt fix and writing a layout need administrator rights (Windows asks
  each time); everything else runs without. Start InputLab normally, not as administrator.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.4.1-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
