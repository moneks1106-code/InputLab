# InputLab

InputLab is a free Windows tool that shows how evenly your mouse and your game controller arrive in Windows, and helps you find out why when they don't.

Most testers print one number, the polling rate. That number usually looks fine even when a mouse feels off. InputLab timestamps every report the moment Windows hands it over and looks at the time between them: gaps, stutters, reports that come in bunches instead of one by one. Around that it shows where your devices are plugged in, which settings can get in the way and, when you ask for it, which driver was busy on your mouse's CPU core in its worst moments.

I build it alone, in my spare time. It started as a mouse tester, then came a controller tester, and since October 2026 both are one app. A lot of what is in it today exists because people ran it on their own PCs and told me what they saw, what confused them and where it was wrong.

**Version 1.4.1.** What changed in each version is on the [Releases](../../releases) page and in `RELEASE_NOTES.md` inside the download.

It is free and it stays free. If it helps you, you can [support it](#support); nothing is unlocked by that. Questions, results and quick help: the [InputLab Discord](https://discord.gg/3kEktvD4zm).

## Download and run

1. Get the latest `InputLab-vX.Y.Z-win-x64.zip` from [Releases](../../releases).
2. Unzip it, keep the whole folder together, and run `InputLab.exe`. There is no installer.
3. You need Windows 11 or Windows 10, 64-bit. Nothing else to install: it brings its own .NET 10.

Windows SmartScreen will probably say "Windows protected your PC" the first time. That is because the app is unsigned, not because something was found in it: click **More info**, then **Run anyway**. If Smart App Control is switched on, there is no "Run anyway"; see [Is it safe?](#is-it-safe).

## What you get

Five tabs across the top:

- **Home** is the page it opens on: your PC at a glance. Hardware, the Windows settings that matter for games and low latency, your drivers, and a small view of your USB devices, your cores and your last runs. Every rating says where it comes from (Microsoft, Windows, a measurement, or my own pick). Home only reads.
- **Mouse** has a live view and the quick tests. **Mouse Motion** gives you the delivery score from 1 to 10, with the reasons under it. Optional extras record more: **USB timing** shows whether a late report was already late at the USB controller or got held up inside Windows.
- **Controller** shows every button, both sticks and triggers live, and has five guided tests for delivery, stick drift, range and circularity, triggers and buttons.
- **System** has three views. The **USB map** draws every controller, hub, socket and device. **Interrupts** shows which CPU core handles which device, measures whether your mouse's USB work has to wait behind another driver, and lets you move devices to other cores, with an undo. **Power** lists the power-saving settings people blame for input problems, says what is actually known about each, and can switch them with one click and put them back.
- **Wireless** is for a receiver that stutters: its path into the PC, the Wi-Fi around you, Bluetooth, and what the dropout pattern of your last run fits.

Every run is saved on your PC. **Compare** ranks your runs only against runs that are comparable (same device, same test, same setup), so you can try a port, a rate or a setting and see whether it changed anything on your PC.

The full list of what each tab can do is further down, under [Every tab in detail](#every-tab-in-detail).

## Before you measure

These are the things that went wrong most often in the runs people sent me:

- Close games and their anti-cheat, launchers and browsers first. A loaded kernel anti-cheat also makes Windows refuse the USB timing and interrupt recordings.
- Pick the device. **Scan (move it)** on the Mouse tab, **Scan (press a button)** on the Controller tab. A test never measures "All mice" at once; if you did not pick one, it asks.
- Move the mouse fast enough. At 8,000 Hz and a low DPI a slowly moving mouse has nothing to send in many of its slots, and that looks like gaps. 3200 DPI is a good choice for the test. If you were too slow, the result says **Limited: moved too slowly** and how fast to move.
- Keep InputLab in front and leave the PC alone until the test is over.
- Steam Input, DS4Windows and similar tools create a virtual controller. InputLab marks those "not measurable" and does not test them, because the numbers would describe the tool, not your pad.

The yellow **Before you measure** button in the app has the longer version, including the BIOS settings I recommend from my own PC builds. Those are my experience, not something InputLab measured, and the notes say so.

## Reading your result

The score rewards the highest rate that stays steady, not the biggest number on the box. An "8000 Hz" mouse that delivers raggedly can score below a steady 1000 Hz one, and the result tells you which part cost the points.

Things worth knowing before you draw a conclusion:

- **A low score is not a verdict on your mouse.** It describes one device, on one PC, on one port, at one moment. Another port, another rate or another PC can give a different number.
- **8 kHz is hard.** At 8,000 Hz one report slot is 125 microseconds, and the limits are measured against that slot. A hiccup that disappears at 1,000 Hz shows at 8,000. Several people saw a 5 at 8 kHz and a clean result at a lower rate with the same mouse. That is useful: now you know where the limit is, and you can look for it.
- **Other testers can show "8000" for the same run.** They show the average rate. The average is still right when one report in a hundred is missing.
- **Find out which side it is.** Try another port (the USB map shows which controller it belongs to), a lower rate, cable instead of receiver. USB timing tells you whether reports were already late when they reached the PC. The interrupt measurement tells you whether a driver on your PC held them up.
- **Change one thing, then measure again.** Two changes at once tell you nothing. Compare is built for this.

And what the numbers are not: InputLab measures when reports arrive in Windows. It does not see inside your device, it does not see the USB cable, and it is not click-to-screen latency. Where it shows latency, those are lower bounds or differences between two Windows input paths, and they are labelled that way. When a run cannot be trusted, it says so instead of showing a green number.

## Is it safe?

Yes. InputLab is not a virus, not spyware and not a miner. It is a measurement tool. I build it, I run it every day on my own gaming PC, and a lot of people run it on theirs.

Here is what stands behind that:

- **No network, no telemetry.** There is no network code in InputLab: nothing leaves your PC, no tracking, no account, no update check. Your runs are stored in `Documents\InputLab`, a small error log in `%LocalAppData%\InputLab\logs`. The **♥ Support** button does not change that: only your web browser opens the PayPal link, and only when you click **Open PayPal**.
- **It only listens to your devices.** It sends nothing to your mouse or controller: no settings, no firmware, no rumble. It installs no driver and no service. Games and other programs get your input as usual.
- **It changes Windows only when you click.** There are exactly three settings it can write, each one saves the original first and has a button that puts it back. All of them are listed in [the next section](#what-inputlab-changes-on-your-pc).
- **Administrator rights are the exception.** Windows itself asks, each time, and only for four things: the USB timing recording, the interrupt recording, writing or undoing an interrupt layout, and changing whether Windows may switch a device off. Those steps do one thing each and refuse anything that is not exactly what the app asked for. Everything else runs without them, so start InputLab normally, not as administrator.
- **It is tested.** Every release is built from a recorded commit and goes through more than two thousand automated tests before it leaves my PC.

Why Windows still warns: InputLab is unsigned, by decision, for now. SmartScreen's message is about a missing certificate, not about anything found in the file, and it starts at zero again with every new release. If Smart App Control is in its enforcing mode on Windows 11, it blocks unsigned programs without a "Run anyway"; I do not recommend switching it off for this tool.

And you can check all of it:

- Compare the download's SHA256 with the one on the release page. That is the exact file I uploaded.
- After unzipping, `sha256sum -c SHA256SUMS.txt` checks every single file (works in Git Bash or WSL).
- `BUILDINFO.txt` in the folder names the source commit, the .NET SDK and the tools the build used.
- Drop the zip on [VirusTotal](https://www.virustotal.com) and let 70+ scanners look at it.
- Block it in your firewall: it never needs the internet.

## What InputLab changes on your PC

By itself: nothing. InputLab reads and measures. It writes to Windows in the three places below, each only after your click, each with the original saved first and a button that puts it back.

| What | Where exactly | When | The saved original | The way back |
|---|---|---|---|---|
| A power-plan value: USB selective suspend, USB 3 link power management, PCI Express link state power management, minimum / maximum processor state, CPU core parking | The active power plan, for the power source in use. These are the values of Control Panel › Power Options › Change advanced power settings, written with Windows' own power API. No administrator rights. | System › Power, the button on that setting's card | `Documents\InputLab\power-restore.json`, one original per power plan and power source | **Restore** on the same card, while that power plan is active |
| "Allow the computer to turn off this device to save power" for an input device and the hubs it hangs on | The checkbox of Device Manager › Power Management, set through Windows' `MSPower_DeviceEnable`. Windows asks for administrator rights. | System › Power, **Don't allow** on the device's card | the same file | **Restore** on the same card |
| The interrupt affinity of a device (which CPU handles its interrupts) | Registry: `HKLM\SYSTEM\CurrentControlSet\Enum\<device>\Device Parameters\Interrupt Management\Affinity Policy`, the two documented values `DevicePolicy` and `AssignmentSetOverride`. Windows asks for administrator rights; it takes effect after a restart. | System › Interrupts: the fix after a measurement, or **Write** in the layout editor, after you confirmed the shown change | `%ProgramData%\InputLab\affinity-backups` | **Undo the change** on the Interrupts tab |

While a test you started runs, three more things happen:

- **USB timing** and **Interrupts › Measure** record with Windows' own tracing (Event Tracing for Windows, Windows Performance Recorder) for the length of the test and stop with it. They observe and change no setting.
- **Write and restart…** restarts Windows, after your confirmation and a 10-second countdown that **Cancel restart** stops.
- Home's timer test asks Windows for a 1 ms timer for InputLab's own process for a moment and releases it.

Files it writes: `Documents\InputLab` (your runs, settings, the port map, the saved originals above), `%LocalAppData%\InputLab` (the log and the last recording of USB timing and of the interrupt measurement), `%ProgramData%\InputLab\affinity-backups`, and an export wherever you choose to save it. To remove InputLab, use **Restore** / **Undo the change** where you changed something, then delete its folder and these three.

What it never does: install a driver or a service, add an autostart entry or a scheduled task, change a boot setting, an interrupt priority or a device's MSI mode, touch the BIOS, open a game or an anti-cheat, send anything to your mouse or controller, or connect to the internet.

On a laptop: the power settings above exist to save battery. Switching them off costs battery life and heat. A change made on battery is saved and restored for the battery values, one made on mains power for the mains values.

## Tested hardware

What I have on my own desk and test every release with:

| Device | Connection | Tested |
|---|---|---|
| GameSir G7 HE (controller) | USB cable, Xbox-compatible (`IG_`) + XInput | Stick Motion many times at 1000 reports/s; the other four controller tests on earlier builds |
| My own 8000 Hz mice | USB cable and a 2.4 GHz receiver | Mouse Motion many times at 8000 reports/s, USB timing, the interrupt measurement and layouts, Wireless |
| A Wooting 60HE+, a USB microphone, a Bluetooth adapter, a lighting controller | USB | USB map and Power view only |

All of that on one Windows 11 PC with an AMD processor and an NVIDIA card. So my own verification is still one controller and a handful of mice.

What changed since the first release is everybody else. People have run InputLab on mice from Logitech, Razer, Endgame Gear, G-Wolves and others, between 1,000 and 8,000 Hz, on Ryzen X3D and Intel hybrid CPUs, and sent me reports, screenshots and recordings. Those runs found real bugs (Memory Integrity shown as on when it was not, a score that was too hard on fast mice, a layout suggestion that stacked everything on one core, a Steam Controller measured through Steam's virtual pad) and each of them is fixed or handled now. That is experience, and I am grateful for it. It is not the same as me having tested your device.

## Known limitations

Controllers

- The PlayStation (DualSense / DualShock 4), Nintendo (Switch Pro) and generic D-input profiles are built but not yet verified on hardware. The same goes for **Assign buttons…** with a real pad that has extra buttons.
- Bluetooth controllers, controller receivers and the Xbox Wireless Adapter are not yet tested by me.
- Xbox Elite back paddles are read through Windows.Gaming.Input for the live view only, not yet verified on a real Elite controller. Windows.Gaming.Input delivers only while the tester's window is in front, and it is never part of a measurement. The Share button of Xbox Series controllers is not available yet.
- A controller that reaches Windows only through Steam Input or a similar tool cannot be measured yet. Measuring the XInput side of such a virtual pad is on my list.
- The interrupt measurement runs during a mouse test only. If you only test a controller, you get no measured core recommendation yet.

Mice

- Sensor tests (true DPI, acceleration, smoothing) are not part of InputLab yet.
- Bluetooth mice follow the Bluetooth link, not a USB rate. Compare Bluetooth runs with Bluetooth runs.

System

- The USB map knows what Windows reports. Whether a USB controller sits in the CPU or the chipset is an estimate. It cannot know where a socket sits on your case unless you tell it, and a front-panel cable shows up as a hub.
- USB timing needs administrator rights for each recorded test and covers USB devices on Windows' current USB stack (not Bluetooth, not very old USB 2 controllers). Its USB-side times come from Windows' USB stack, not from the cable. Recording adds a little work to the path it watches; the tab tells you when a run looks disturbed by it.
- Interrupts: a desktop measurement shows less load than a game. Firmware interrupts are invisible to Windows and show up only as waits nobody can explain. A layout takes effect after a restart. PCs with more than 64 logical processors are not supported.
- For most power settings no published measurement shows an effect on input latency. The Power view says so, and suggests a test before and after.
- Wireless: a receiver's own radio channel and its packet loss are invisible to Windows. The Wi-Fi list needs location access for desktop apps since Windows 11 24H2; InputLab reads no location.

General

- Latency is host-side only: lower bounds from the measured intervals and differences between two Windows input paths. Absolute button-to-PC latency needs a hardware rig, which is not part of this.
- The thresholds for stick, trigger and button quality are provisional.
- Moving the window between screens with different scaling makes Windows stretch it (blurry) until the next start.
- Not tried by me: Windows 10, Windows on Arm, two controllers at once, 8 kHz controllers, Windows in languages other than my own.
- It is unsigned (see [Is it safe?](#is-it-safe)). A few overlays and security tools that inject code into other programs can make it close right after starting. If that happens, please tell me which software it was.

## Every tab in detail

**Home.** Hardware (CPU, graphics card and driver, memory, board and BIOS, Windows, displays), the Windows settings that matter for games (power plan, Energy Saver, Game Mode, Game Bar recording, GPU scheduling, display rate, mouse acceleration, Memory Integrity, the Windows timer and more), the drivers your mouse's reports pass through, the BIOS points Windows cannot read, and small views of the USB map, the cores and your last runs. **Copy summary** puts it on the clipboard as text, without your PC or user name.

**Mouse.**

- Live view: reports per second, the intervals of the last two seconds, the motion trail.
- Quick tests: **Mouse Motion** (10 / 20 / 30 s, the delivery score), **Mouse Idle** and **Mouse Click Burst** (statistics, no score).
- Result: the rate, the gaps by kind (late, slow movement, other) and where they sit in the run, plus **During this run**: what else was busy on your PC at the same time. That part is context, never a cause and never part of the score.
- Timeline: every interval of the run; **Show longest gap** jumps to the worst one.
- **USB timing**: on its tab the start button records Windows' USB stack next to the test. One clock, three lanes: reports at the USB controller, how long Windows took to hand each one over, reports at InputLab. Each late report is marked with where it started. **Export CSV…** saves the report-by-report times.

**Controller.**

- Live view with button labels Auto / Xbox / PlayStation / Nintendo / Generic.
- **Separate LT/RT**: Xbox-style pads report both triggers on one shared axis in Windows' generic input path; one click turns on XInput so both show separately.
- **Assign buttons…**: map a generic pad's extra buttons to L4, R4, L5, R5, Share, Guide, Touchpad or Mute.
- Quick tests, each with a countdown and instructions:
  - **Stick Motion** (10 / 20 / 30 s): rotate both sticks. Gives the delivery score.
  - **Idle Drift** (10 / 30 / 60 s): put the controller down. Stick noise, centre offset, drift, ghost presses.
  - **Button Burst** (10 s): tap A as fast as you can. Press durations and chatter.
  - **Trigger Sweep** (15 s): pull the triggers slowly. Range, resolution, start deadzone.
  - **Stick Range** (20 s): circle each stick along its edge. Circularity, outer range, centre repeatability.
  - Manual capture: start and stop yourself, statistics without a score.
- Advanced / Lab: the full breakdown with explanations and graphs.
- Measurement regimes: mark a run as Host Baseline, Desktop Load or Gaming Load Stress. Runs under different loads are never ranked against each other.

**System.**

- **USB map**: every USB controller, hub, socket and device, with link speed and how often each device is polled. One sentence on top says how things stand, a list says what to check and why, with its source. No score. Re-plug a device and the map reads itself again.
- **Interrupts**: a tile per CPU core with the devices expected there. **Measure** runs Mouse Motion for 10 seconds while Windows records its interrupts, then shows on which core your mouse's USB work ran, how long it waited and behind which driver. The driver check under it goes through the run's gaps one by one. **Edit layout** lets you drag devices onto cores, **Suggest a layout** fills it in with a reason for every device, **Write and restart…** applies it, and **Undo the change** puts everything back.
- **Power**: USB selective suspend, USB 3 and PCI Express link power management, processor states, core parking, Energy Saver and whether Windows may switch your input devices off. Each with what it does, what is known about it, and a button with **Restore**.

**Wireless.** Pick your receiver, answer two questions (where is the socket, what is plugged in next to it), and read what fits. **Before / after** puts two runs side by side.

**Compare, export, import.** Compare says how many runs it used and why it left others out. **Export report** writes a Markdown report of a run that you can post. Every quick test is also saved as a session file with a `_metrics.csv` next to it in `Documents\InputLab`. If you used AleksMouseTester or AleksControllerTester before, **Import** copies your old runs in; the old files are only read.

## How this was built

- InputLab is my design: what it measures, how it scores, and the rule that it never shows a number it cannot stand behind. I write the code together with Claude Code, Anthropic's coding assistant. The decisions and the testing are mine, much of the code is AI-written, and I think you should know that.
- The code that changes Windows or runs with administrator rights is the part that gets reviewed hardest. Every write saves the original first. Nothing in the app talks to the internet.
- It is closed-source, by choice. The public repository holds the download, this README, the license and the release notes. What you can check yourself is in [Is it safe?](#is-it-safe) and in the list of [what it changes](#what-inputlab-changes-on-your-pc).
- The USB checks follow a written catalogue of rules, each with its source. Where there is no published evidence for a common piece of advice, the app says so instead of repeating it.

## Feedback

Feedback is what moved this tool the most. If something looks wrong, or you measured a device and want a second opinion, send me:

- the device, the connection (cable, receiver, Bluetooth) and, for a controller, its mode,
- the result of your **Mouse Motion** or **Stick Motion** run,
- the report from **Export report**. It lists the device, the results and some PC context. It contains no user or computer name and only an anonymized device id. Have a quick look before you post it anyway.

Where: an issue here on GitHub, or the [InputLab Discord](https://discord.gg/3kEktvD4zm). I read both.

## Support

InputLab is free, and it stays free. If it helped you, a voluntary donation helps me keep going and buy more devices to test with:

**[paypal.me/AKrstic43](https://paypal.me/AKrstic43)**

- Nothing is unlocked by donating. Every feature is free for everyone.
- In the app, the **♥ Support** button at the top right opens a small window with the same link. **Open PayPal** opens it in your web browser, **Copy link** copies it. It never pops up by itself.
- On GitHub, the **Sponsor** button at the top of this repository leads to the same link.

A bug report or a run from a device I do not own helps just as much.

## License

Free to use. Binary-only release, see `LICENSE.txt` in the download. Not open source. The .NET runtime bundled with the app keeps its own licenses: `THIRD-PARTY-NOTICES.txt` and `DOTNET-RUNTIME-THIRD-PARTY-NOTICES.TXT` in the download.

Xbox, DualSense, DualShock, Nintendo Switch, Steam, Wooting and other product names are trademarks of their respective owners. InputLab is an independent tool, not affiliated with or endorsed by any of them.
