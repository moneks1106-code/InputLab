# InputLab v1.1.1 — mice named as they name themselves

**A patch release with two fixes after the first reports from the field. Everything from 1.1.0 (USB timing) is
unchanged.**

## Fixed

- **Mice are named as they name themselves.** The mouse picker and your saved runs now show the name the mouse
  reports itself (its USB product string, e.g. "Endgame Gear OP1 8k v2 Gaming Mouse"). The vendor table behind it is
  only a fallback, now checked against the USB-IF vendor list. 1.1.0 and earlier called an Endgame Gear mouse
  (vendor 3367) a "Finalmouse", a Finalmouse (361D) a "Pulsar" and a Keychron (3434) a "Wooting", and showed chip makers
  such as SinoWealth as the brand. Saved runs with such a label are corrected when they load; measurements and scores
  are unchanged. Thanks to the users who reported it.
- **USB timing says why Windows refused the trace.** The recording always runs with administrator rights, so the old
  message "it needs administrator rights" was wrong. Windows refuses it while a game's kernel anti-cheat is still
  loaded (seen with EA AntiCheat). The tab now says so: close the game, wait a moment and try again. InputLab does not
  try to work around an anti-cheat.

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only. Self-contained: the .NET 10 runtime is
  included — unzip the whole folder and run `InputLab.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected your PC" — click **More info → Run
  anyway**. With Smart App Control switched on, Windows blocks the app (there is no "Run anyway").
- USB timing needs administrator rights for each recorded test (Windows asks); everything else runs without.

## Verify the download

- The zip's **SHA-256** is on the release page. Check it in PowerShell:
  `Get-FileHash .\InputLab-v1.1.1-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool
  version.
