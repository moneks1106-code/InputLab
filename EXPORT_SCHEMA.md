# `_metrics.csv` — export schema

*Current: `schema_version` **`ct-2`** (InputLab). One header line, one data row per file.
Numbers use the invariant culture (`.` decimal separator) regardless of the OS locale. The schema is a
string, never a number, so a controller export can never be mistaken for one of the mouse tester's
numeric schemas (2, 3); the `product` column names the writer explicitly.*

*`ct-2` (WP-12) appends the quality summary block (columns 57–80) to `ct-1`; columns 1–56 are unchanged
in name and position. Each change after the first release is a new `ct-N`.*

## Columns (schema `ct-2`)

| # | column | notes |
|---:|---|---|
| 1 | `schema_version` | `ct-2` |
| 2 | `product` | always `InputLab` |
| 3 | `timing_claim` | always `host_delivery_not_device_polling` |
| 4 | `timestamp_basis` | `Stopwatch host timestamp` |
| 5 | `valid_dt_policy` | `exclude intra-batch and buffered intervals` |
| 6–8 | `protocol,duration_s,target_hz` | test identity; `target_hz` is the report rate the controller is **set** to (firmware / companion app), `0` = not set |
| 9 | `connection` | `UsbCable` / `UsbReceiver` / `Bluetooth` / `Virtual`; **EMPTY = not known** (never `Unknown` as data) |
| 10 | `connection_provenance` | `auto` (classified from the device-node ancestry) / `manual` (picked by the user); empty when `connection` is empty |
| 11–12 | `controller_model,controller_mode` | free text from the setup (quoted when needed, line breaks folded to spaces) |
| 13 | `input_source` | backend of the headline timing metrics (`RawInputHid`; the delivery score only ever comes from it); empty = not recorded |
| 14–19 | `T_nom_ms,f_nom_Hz,median_ms,IQR_ms,p01_ms,p99_ms` | core timing |
| 20–27 | `double_pct,outlier_pct,overall_ev_s,N,valid_dt,buffered_pct,max_batch,timing_valid` | rates / delivery integrity |
| 28–29 | `drain_errors,corrupt_headers` | capture integrity |
| 30–35 | `changed_reports,identical_reports,channel_changes,stick_active_reports,changes_s,changed_pct` | activity: reports with a channel change, reports that changed nothing at all (not even the report bytes), the sum of individual channel changes, reports with either stick beyond 12% of its range, changed reports per second, changed reports as % of `N` |
| 36–45 | `testEnvironment.profile,gameRunning,gameName,fpsCap,overlaysEnabled,powerProfile,usbPortNotes,pollingRateHz,backgroundLoadNotes,claimScope` | environment metadata block (`testEnvironment.profile`: `Unknown` / `HostBaseline` / `DesktopLoad` / `GamingLoadStress`) |
| 46 | `environment_interpretation` | profile hint |
| 47–51 | `p95_ms,p99_9_ms,max_gap_ms,gap_count,burst_count` | tail-latency and gap metrics |
| 52 | `delivery_score` | the 1–10 headline, **EMPTY unless the run has a valid delivery score** — only a Stick Motion run has one (never a sentinel like `1.0`) |
| 53 | `score_status` | `Valid` / `InvalidLowSamples` / `InvalidCaptureIntegrity` / `InvalidMultiDevice` / `NotScored` (a valid capture of a protocol without a delivery headline: Idle Drift, Button Burst, Trigger Sweep, Stick Range, Manual capture); empty when no score report exists |
| 54 | `score_algorithm_version` | the score formula's version (`controller-delivery-score-v1`); scores are comparable only between rows with the same value here |
| 55 | `invalid_reasons` | pipe-joined (`CaptureIntegrity|LowSamples`), empty when valid or not scored |
| 56 | `device_event_counts` | per-stream event counts of the Raw Input HID stream (a stream = input source + device), semicolon-joined, descending (`79000;1026`). **EMPTY = unverified** — never to be read as "1 device". XInput events are never counted here: the analysis splits the capture by source first |
| 57 | `report_mode` | `Streaming` / `ChangeDriven` / `Mixed` / `Unknown` from the quality report (a motion-basis `ChangeDriven` is a hedged verdict); empty = not measured |
| 58–59 | `quality_protocol,quality_algorithm_version` | the quality report's protocol (`StickMotion`, `IdleDrift`, …) and version (`controller-quality-v1-provisional`) |
| 60–62 | `quality_sticks_avg,quality_triggers_avg,quality_buttons_avg` | plain average of each group's rated rows (1–10, **provisional** thresholds); empty when the group has no rated row |
| 63–64 | `quality_rated_rows,quality_not_rated_rows` | how many rows were rated, and how many failed their gate ("not rated") |
| 65 | `stick_coverage_passed` | Stick Motion coverage gate (`true`/`false`); empty for the other protocols |
| 66 | `latency_method` | the method of every `latency_*` value in this row: `polling_estimate` (host-side, lower bound); empty when the row carries no latency value — **a latency value is never exported without its method** |
| 67–69 | `latency_report_wait_mean_ms,latency_report_wait_p99_ms,latency_worst_case_ms` | polling-latency lower bounds from the valid HID intervals (Stick Motion): mean added wait `Σdt²/(2Σdt)`, its 99th percentile, the largest valid interval |
| 70–71 | `latency_button_polling_avg_ms,latency_button_polling_worst_ms` | the polling component of button results (+T_nom/2, +T_nom) with T_nom from this run (streaming pads) or the latest Stick Motion run of the same controller |
| 72 | `xinput_sampling_mode` | the XInput mode the run polled with (`Off` / `Efficient` / `Precise`); empty = not recorded |
| 73–75 | `xinput_events,xinput_f_nom_Hz,xinput_missed_packets` | secondary XInput statistics of the linked slot (change events, modal packet rate, packets the poller missed); display only, never scored |
| 76 | `cross_backend_method` | `cross_backend_relative` whenever `cross_backend_delta_median_ms` has a value |
| 77–79 | `cross_backend_delta_median_ms,cross_backend_matched_edges,cross_backend_leader` | Button Burst on an Xbox-compatible pad with XInput Precise: median first-detection delta (XInput − HID; a relative delivery-path difference, **not input latency**), matched button edges, which path saw them first (`raw_input_hid` / `xinput` / `neither_within_quantization`) |
| 80 | `path_agreement_ratio` | HID changed reports ÷ XInput packet advances (Stick Motion with the cross-check); outside 0.90–1.10 the HID compatibility collection misses states XInput sees |

## Columns (schema `il-mouse-1`, mouse level)

*Written next to every mouse session's JSON by the Mouse level (WP-03 Stage D), same file name pattern
(`<session id>_metrics.csv`), one header line, one data row, invariant culture. A different schema string,
so a reader never mistakes a mouse row for a controller row: the mouse's identity and DPI replace the
controller setup, the six movement columns replace the six activity columns, there is no quality block,
and the run's identity (session id, run id, start time) is in the row itself.*

| # | column | notes |
|---:|---|---|
| 1 | `schema_version` | `il-mouse-1` |
| 2 | `product` | always `InputLab` |
| 3 | `device_level` | always `mouse` (the session's `device_level`) |
| 4–6 | `timing_claim,timestamp_basis,valid_dt_policy` | as the session records them (`host_delivery_not_device_polling`, `Stopwatch host timestamp`, `exclude intra-batch and buffered intervals`) |
| 7–9 | `protocol,duration_s,target_hz` | test identity; `target_hz` is the polling rate the mouse is **set** to (its software or button), `0` = not set |
| 10 | `dpi` | the DPI entered in the setup strip; **EMPTY = not set** |
| 11–12 | `mouse_name,mouse_path` | the measured mouse's friendly name and Raw Input path; both **EMPTY when "All mice"** was measured |
| 13 | `input_source` | always `RawInputMouse` (the Raw Input mouse registration) |
| 14–19 | `T_nom_ms,f_nom_Hz,median_ms,IQR_ms,p01_ms,p99_ms` | core timing (as `ct-2` columns 14–19) |
| 20–27 | `double_pct,outlier_pct,overall_ev_s,N,valid_dt,buffered_pct,max_batch,timing_valid` | rates / delivery integrity (as `ct-2` columns 20–27) |
| 28–29 | `drain_errors,corrupt_headers` | capture integrity |
| 30–35 | `total_dx,total_dy,abs_counts,move_reports,counts_s,move_pct` | movement: the signed sums of dx and dy, the sum of \|dx\|+\|dy\|, reports that carried movement, counts per second, moving reports as % of `N`; **all EMPTY when the saved run carries no movement section** |
| 36–40 | `p95_ms,p99_9_ms,max_gap_ms,gap_count,burst_count` | tail-latency and gap metrics; empty on a run saved without them |
| 41 | `delivery_score` | the 1–10 headline **exactly as Compare shows it** (the projection: stored, recomputed or none), EMPTY unless the run is scorable — only a Mouse Motion run has one |
| 42 | `score_status` | the stored `Valid` / `InvalidLowSamples` / `InvalidCaptureIntegrity` / `InvalidMultiDevice` / `NotScored` (Mouse Idle, Mouse Click Burst) |
| 43 | `score_algorithm_version` | `mouse-delivery-score-v1`; scores are comparable only between rows with the same value here |
| 44 | `invalid_reasons` | pipe-joined, empty when valid or not scored |
| 45 | `device_event_counts` | per-mouse report counts, semicolon-joined, descending; **EMPTY = unverified** |
| 46–48 | `started_utc,session_id,run_id` | the run's identity (ISO 8601 UTC; the ids of the session JSON) |
| 49–50 | `pre_roll_ms,gc_in_window` | the discarded pre-roll and the tester's GCs inside the scored window; empty = not recorded |

## Semantics guarantees

- `delivery_score`, `score_status`, `invalid_reasons` are derived from the **same** central
  validity predicate the saved session JSON uses — the CSV and the saved session can never
  disagree on validity.
- A score value is never emitted for an invalid run; consumers must treat an empty
  `delivery_score` + non-`Valid` `score_status` as "not evaluable", not as zero.
- `drain_errors` retains its legacy column name and, since v0.2.15, counts native read failures
  from both the current `GetRawInputData` event and `GetRawInputBuffer` drains.
  `corrupt_headers` includes malformed current-event records. Successfully read
  events of device types the capture does not measure are intentionally skipped and do not
  increment either counter.
  Either nonzero counter uses the existing capture-integrity validity gate; the
  score formula and schema version remain unchanged.

## Session JSON — `systemContext` inventory fields (added in v0.2.15, `schema_version` stays 4)

The saved session JSON (`systemContext`, captured on a background thread and never while a
measurement runs) gained seven **additive, nullable** members. Absent = not captured, never
"off"; every member carries a `Provenance` entry (`ReadOk` / `Unknown`), and
`AutoCaptureSchemaVersion` stays `1`. They are **inventory** for telling runs apart after the
fact — nothing in the score or its validity rules reads them, and Compare does not use them to
group runs; Energy Saver and battery state appear there only as notes.

| member | source | notes |
|---|---|---|
| `VbsConfigured` | `HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\EnableVirtualizationBasedSecurity` | **configured**, not proven running |
| `HvciConfigured` | `…\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity\Enabled` | Memory Integrity as configured, not proven running |
| *(HvciRunning)* | — | **deliberately not captured**: reading the running state would need a WMI query or a helper process while probing, which the app avoids |
| `EnergySaverActive` | `SYSTEM_POWER_STATUS.SystemStatusFlag` bit 0 | state at probe time; can be on while on AC since 24H2 |
| `OnBattery` | `SYSTEM_POWER_STATUS.ACLineStatus` | `0` → `true`, `1` → `false`, `255` (unknown) → absent |
| `ProcessArchitecture` | `RuntimeInformation.ProcessArchitecture` | e.g. `X64` |
| `OsArchitecture` | `RuntimeInformation.OSArchitecture` | native machine, e.g. `Arm64` |
| `IsEmulated` | `ProcessArchitecture != OsArchitecture` | `true` = the x64 build ran under emulation on an ARM64 host |

The same seven values appear as rows in the reference-bundle Markdown report (empty cell = not
captured). They are **not** in `_metrics.csv`: by this document's own convention an appended column
is a schema bump (2 → 3 did exactly that), so the CSV stays at schema 3, byte-identical.

## Session JSON — run field `preRollMs` (added in v0.2.15, `schema_version` stays 4)

Each quick-test run in the session JSON carries `preRollMs`: the length of the **discarded warm-up**
that ran right before the scored window, in milliseconds (500 since v0.2.15). The capture already
runs during the warm-up, and everything captured in it is thrown away at one atomic boundary; the
run's duration, samples and metrics all start at that boundary. Absent on runs saved before v0.2.15,
which had no warm-up. Provenance only — nothing in the score or its validity rules reads it. It is
**not** a `_metrics.csv` column (no schema bump).

## Session JSON — run field `gcInWindow` (added in v0.2.16, `schema_version` stays 4)

Each quick-test run carries `gcInWindow`: how many garbage collections **the tester's own process** ran
during the scored window. Every collection briefly pauses all of the tester's threads, including the one
that receives the controller reports, so it is an observer effect of the tool, not of your system. Since
v0.2.16 a quick test asks the .NET runtime not to collect at all while it measures, so the expected value
is `0`; a higher value says that request could not be kept for that run. Absent on runs saved before
v0.2.16. Provenance only — nothing in the score or its validity rules reads it. Not a `_metrics.csv`
column (no schema bump).

## Session JSON — run field `wgiFootprint` (WGI activation gate, `schema_version` stays 100)

Each quick-test run carries `wgiFootprint`: whether Windows.Gaming.Input was in **the tester's own process**
while it measured — `"WGI not loaded"` (never activated in this process) or `"WGI loaded (idle)"` (activated
earlier for the Controller page's live view: its poll thread is stopped for every measurement, but
Windows.Gaming.Input.dll cannot be unloaded and keeps a few runtime threads of its own). Read once the live
WGI thread was stopped for the run. The Markdown report prints it as "Windows.Gaming.Input in tester process"
(empty = not recorded). Absent on runs saved before the gate. Provenance only — nothing in the score or its
validity rules reads it. Not a `_metrics.csv` column (no schema bump).

## Session JSON and raw events — controller product (WP-03)

- The session JSON carries `"schema_version": 100` and `"product": "InputLab"`. A session
  file whose `product` is missing (every mouse-tester file) or different is refused on load ("not an
  InputLab session") and never pruned or deleted by this product.
- Run fields: the movement block is replaced in place by `ChangedReports`, `IdenticalReports`,
  `ChannelChanges`, `StickActiveReports`, `ChangesPerSec`, `ChangedFraction`; `MovementScore` is
  `ActivityScore`.
- `graphTrace` is schema 2: `t, dt, v, b` plus `lx, ly, rx, ry` (−32767..32767, +Y = up), `lt, rt`
  (0..65535) and `btn` (button bits); the mouse `dx`/`dy` lists are gone. Up to 8000 points.
- Raw-event JSONL lines are schema 2: the mouse fields (`dx`, `dy`, `wheelDelta`, `buttonFlags`,
  `buttonData`, `rawButtons`) are replaced by `buttons, lx, ly, rx, ry, lt, rt, hat, present, changed,
  reportId, packetNumber, packetDelta, prevObservationTicks, deviceClock`, plus `inputSource`
  (RawInputHid / XInput / GameInput / Synthetic) and `hostTimestampKind`. An XInput event's
  `deviceHandleHash` is derived from `xinput:slotN`.

## Session JSON — controller setup, device and connection (WP-09)

- `Setup` is the controller setup (the mouse DPI, mouse model and mousepad members are gone; an old
  file's leftover keys are ignored on load):

  | member | notes |
  |---|---|
  | `PollingTarget` | the report rate the controller is set to, `0` = not set |
  | `ConnectionType` | `Unknown` / `UsbCable` / `UsbReceiver` / `Bluetooth` / `Virtual` (by name) |
  | `ConnectionProvenance` | `ReadOk` = auto-detected from the device-node ancestry at run start, `Manual` = picked by the user, `Unknown` = neither |
  | `ControllerModel`, `ControllerMode`, `Notes` | free text |

- `device` (nullable; absent = no single HID controller was recorded — never a guessed device) is the
  measured controller's identity and HID capabilities: `vendorId`, `productId`, `versionNumber` (the HID
  release number, not necessarily the firmware), `usagePage`, `usage`, `isXInputCompat` (`IG_` path),
  `linkedXInputSlot` (0..3, absent when not linked), `xinputVendorId` / `xinputProductId` (what
  `XInputGetCapabilitiesEx` reported for the linked slot — on the GIP pads measured so far the same generic
  045E:02FF as the HID path, so it does not name the model), `usbVendorId` / `usbProductId` (WP-11: the
  physical USB device in the device-instance ancestry — the real pad behind a wired GIP pad's generic
  045E:02FF, e.g. 3537:1065; absent for Bluetooth, behind a wireless receiver, or when unknown),
  `mappingProfile` (`Generic` / `XInputCompat` / `Sony` / `Nintendo`), `connectionType`, `deviceId` (the
  same anonymized 16-hex id as the raw-event `deviceHandleHash` of its HID events — never the device path)
  and `axes`: one entry per HID value capability with `usagePage`, `usage`, `logicalMin`, `logicalMax`,
  `bitSize` and the decoder `channel` it feeds (`LX`, `LY`, `RX`, `RY`, `LT`, `RT`, `LT+RT` for the combined
  trigger axis of an `IG_` collection, `Hat`; empty = unmapped). With these, the normalized axis values can
  be mapped back to real steps. Filled at run start from the selected controller (WP-11).
  `buttonAliases` (WP-18; absent = none, and in every older session): the user's Extra-button assignment
  the run was measured with, one `{"button": "Extra2", "target": "L5"}` per assigned Extra (`Extra0`..`Extra7`
  → `L4`, `R4`, `L5`, `R5`, `Share`, `Guide`, `Touchpad` or `Mute`). The run's events carry the target
  button, never the Extra bit; the Markdown report prints it as "Extra-button assignment" ("Extra 3 → L5").
  `wgi` (absent = no Windows.Gaming.Input pad was linked — WGI off, unavailable or no match — and in every
  older session): the WGI gamepad linked to the controller for the live view when the run started, as
  `{"vendorId": 1118, "productId": 2816, "paddles": ["L4", "R4", "L5", "R5"], "share": false,
  "rawButtonCount": 15}` (VID/PID of its `RawGameController`; `paddles` = the positions it reports, literal
  names; `share` = a raw button labelled Share). Reference only: WGI never feeds a run's events, metrics or
  scores. The Markdown report prints it as "Windows.Gaming.Input gamepad" ("045E:0B00 · paddles: L4, R4, L5,
  R5 · Share: no (live view only, not measured)"; empty = none). Raw events never carry `inputSource` `Wgi`
  (a capture never records a WGI event).
- `raw_input_model` is `WM_INPUT(RIM_TYPEHID)/GetRawInputBuffer` (WP-11: game-controller HID reports;
  the mouse tester writes `WM_INPUT/GetRawInputBuffer`, so the two never share a Compare cohort).
- Run field `source`: the input backend of the run's headline timing metrics (`RawInputHid` for every
  scored run); absent = not recorded. Compare keeps runs of different backends apart.
- `systemContext` gains `ConnectionType` (classified from the device-node ancestry: a Bluetooth
  enumerator — `BTHENUM\`, `BTHLEDEVICE\`, `BTHLE\`, `BTH\` — before the first `USB\` node = Bluetooth; a
  USB chain on a root-enumerated bus such as ViGEm = Virtual; a known receiver VID/PID anywhere in the USB
  chain = UsbReceiver; any other USB chain = UsbCable; nothing recognisable before the host controller =
  not captured — the same classifier as the device picker since WP-11), `BluetoothRadioVidPid` (Bluetooth only: the host radio's USB IDs) and
  the logging-only `SteamRunning`, `ViGEmBusPresent`, `HidHidePresent`, `GameInputSvcPresent`. The USB
  port members (`UsbPortLocationInfo`, `UsbLocationPath`, `UsbHubVidPid`) are filled **only for a USB
  connection** — for a Bluetooth pad the first `USB\` ancestor is the radio, never the pad's port. The
  mouse-only `PointerPrecisionEnabled` is gone. All new members are nullable with a `Provenance` entry;
  absent = not captured.
- `testEnvironment.profile` uses `HostBaseline` (was the mouse product's host-baseline member name).
- The Markdown report prints the setup rows (Target Hz, Connection, Connection source, Controller model,
  Controller mode), the device rows (Controller VID:PID, HID version, HID usage, XInput link, XInput
  VID:PID, USB device VID:PID, mapping profile, anonymized device id, HID axes), the Bluetooth radio, the controller-software inventory, and
  per run the session's own `raw_input_model`, `valid_dt_policy` and the run's input backend — empty
  cell = not recorded.

## Session JSON — controller results per run (WP-12)

Each run of a quick test carries the results of its protocol (all members nullable; absent = not measured
or not recorded, never a default value):

| member | notes |
|---|---|
| `ProtocolId` | `StickMotion` / `IdleDrift` / `ButtonBurst` / `TriggerSweep` / `StickRange` / `ManualCapture` (member name; a name this build does not know reads `Unknown` and the run name decides) |
| `ScoreStatus` | gains `NotScored`: a valid capture of a protocol without a delivery headline; such a run stores no `QuickScore`, `QuickScoreRaw`, `QuickScoreDisplay`, `PollingScore` or `ActivityScore` |
| `reportMode` | `Streaming` / `ChangeDriven` / `Mixed` / `Unknown` |
| `quality` | the input-quality report: `AlgorithmVersion` (`controller-quality-v1-provisional`), `Protocol`, `Source`, `Rows` (`Group`, `Metric`, `Channel`, `Name`, `ValueText`, `ValueTextInvariant`, `Value`, `Unit`, `Status` = `scored` / `informational` / `not_rated`, `Score`, `Provisional`, `Note`, `Method`, `Claim`), `GroupAverages`, `Notes` and the detail records (`ReportMode`, `Latency`, `ButtonPolling`, `CrossBackend`, `PathAgreement`, `StickMotion`, `StickIdle`, `StickRange`, `TriggerRest`, `TriggerSweep`, `ButtonBurst`, `ButtonIdle`). The stored `Value`s are the observation; the scores are re-derived under the current provisional thresholds whenever the run is shown or exported. **Trigger source:** a Trigger Sweep of an Xbox-compatible (`IG_`) controller linked to its XInput slot measures the slot's separate 8-bit triggers, because the HID path reports both triggers on one combined axis; such a run has `Source` = `XInput`, every `TriggerSweep[]` entry carries `Source` (`RawInputHid` / `XInput`; absent in older runs = `RawInputHid`) next to `CombinedAxis`, and an informational row `trigger_source` says `via XInput (8-bit)`, `Raw Input HID (combined LT/RT axis)` or `Raw Input HID`. An Idle Drift of such a linked controller reads its trigger rest from the XInput slot the same way: every `TriggerRest[]` entry carries `Source` (absent in older runs = `RawInputHid`) and `CombinedAxis`, and the run has the same `trigger_source` row (its quality `Source` stays `RawInputHid`: sticks and buttons are HID). **Invisible changes:** with the linked XInput stream in the run, `ReportMode` carries `InvisibleChangeReports` and `InvisibleChangeFraction` (HID reports that changed no decoded channel within ±2 ms of an XInput packet advance — changes the HID path cannot show; counted as changes, not in `StaticReports` / `IdenticalReports`) and the informational row `invisible_change_frac`; absent / 0 without an XInput stream and in older runs |
| `xinputMetrics` | the XInput stream's delivery statistics (the same members as `Metrics`); display only |
| `crossBackend` | the cross-backend first-detection delta (`Method` = `cross_backend_relative`) |
| `latency` | the polling-latency lower bounds (`Method` = `polling_estimate`, `Claim` = `host-side, lower bound`) |
| `xinputSamplingMode` | `Off` / `Efficient` / `Precise`: the XInput mode during the run |
| `xinputStatistics` | the linked slot's poll statistics: `slot`, `mode`, `highResolutionTimer`, `pollsPerSecond`, `quantizationUs` (median poll period), `meanCallUs`, `vendorId`/`productId`, `events`, `missedPacketEvents`, `missedPackets`, `stateCompareEvents`, `packetNumberFrozen`, `packetSequenceResets` |

The session's `ScoreAlgorithmVersion` is `controller-delivery-score-v1`. The Markdown report prints, per
run, the input-quality table (`Group | Metric | Value | Status | Score | Method | Note`) and the controller
details (report mode, group averages, coverage gate, the trigger source of a Trigger Sweep or an Idle Drift, latency and
cross-backend method, XInput mode and statistics, notes); its header states the timing claim and that
latency values are host-side lower bounds or relative differences with their method.

## Changelog
- **il-mouse-1 (InputLab unreleased, WP-03 Stage D)** — the mouse level's sidecar: its own schema
  string (50 columns), the mouse's name, path and DPI in place of the controller setup, the six movement
  columns in place of the six activity columns, no quality block, the run's identity in the row.
  `ct-2` is unchanged.
- **ct-2 (InputLab 0.1.0, WP-12)** — the quality summary block appended after
  `device_event_counts` (24 columns, 80 in total): report mode, quality protocol/version, group averages
  and row counts, the Stick Motion coverage gate, the polling-latency lower bounds with
  `latency_method`, the XInput sampling mode and statistics, the cross-backend delta with
  `cross_backend_method`, and the path agreement. `score_status` gains `NotScored`; `delivery_score` is
  filled for valid Stick Motion runs only.
- **ct-1 (InputLab 0.1.0, WP-03 + WP-09)** — new product, new schema string: `product` column
  after `schema_version`; the four movement columns replaced by the six activity columns; the mouse
  `dpi` column replaced by `connection, connection_provenance, controller_model, controller_mode,
  input_source` after `target_hz` (56 columns). Everything else keeps the mouse tester's schema-3
  semantics.
- **3, unchanged (v0.2.16)** — runs record `gcInWindow` in the session JSON (section above).
- **3, unchanged (v0.2.15)** — VBS/HVCI/Energy-Saver/architecture context added to the session JSON
  `systemContext` only (section above); no CSV column appended, no bump. `drain_errors` also counts
  native read failures (see *Semantics guarantees*). Runs record `preRollMs` in the session JSON.
- **3** — appended tail metrics, score + validity + algorithm-version provenance, and the raw
  per-device observation. (Before 3, the score existed only in the session JSON and the CSV carried
  no tail columns.)
- **2** — environment metadata block + interpretation hint.
