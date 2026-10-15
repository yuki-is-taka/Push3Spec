# Audio

**Scope.** This page records what was measured about the Push 3's USB audio interface as a computer sees it: the audio devices, channels, sample rates, the analogue input type and gain, levels, and the Windows driver. It rests on S03 (macOS, 2026-10-02: ffmpeg capture), S13 (macOS, 2026-10-02: Unreal Engine capture spike), S09 block d (macOS, 2026-10-06: CoreAudio spike), S02 (Windows, 2026-10-02: registry and device-tree queries), S12 (Windows, 2026-10-02: Unreal Engine WASAPI spike), S14 (Windows, 2026-10-06: cached device-tree listing, the unit on the Mac) and S11 block c (Windows, 2026-10-08: level check with `ffmpeg astats`). One row comes from a process list taken in S04. The unit was driven from a computer in every session, which is hardware Control Mode (Inferred for these sessions: no record names the mode); Standalone Mode was not tested. Read these caveats first.

- **No signal was ever played to the Push's outputs on either OS.** The playback direction is Not tested; "16 out" (macOS) and "2 ch" (Windows) are device reports, not measurements of sound.
- **Levels are not sensitivity figures.** The source level, the cable and (except in S11 c) the input gain setting were not logged. The levels show channel mapping, the balanced input and the noise floor.
- **The Windows capture results are prose.** The S12 spike's build trees and raw logs were deleted after the run; its numbers survive in `R119`. The S11 c levels survive in a design decision record of the UE5 test host. The Windows playback endpoint, the rate list and the driver identity come from cached registry and device-tree entries read while the unit was absent (S02, S14), not from opened streams. The S02 values are transcribed in the notes (`R119:164-171`; the query output was not kept); only the S14 device-tree listing is a raw record.
- **Whether Windows capture works with Live not running is not settled.** The S12 spike's record does not state Live's state; S02's "Live not running" (`R001:121`) covers the 10:09-10:22 study, not the spike.
- Firmware: S03, FW label 1.5.4 (from notes), Device Inquiry build 91 (seen the same day, in block e of S03). S02: Device Inquiry build 91, no label. S11: Device Inquiry build 91, read in block b at 13:28:48 on 2026-10-08, no label (block c ran later that day). S09, S12 and S13: firmware not logged. S14: not applicable (the unit was attached to the Mac).
- The UE capture spike of 2026-10-02 is S12 (Windows) and S13 (macOS) in [09-sessions.md](09-sessions.md); both are prose only.
- The bytes of the input-type and gain SysEx commands are in [04-sysex.md](04-sysex.md); this page gives only the measured effect.
- Evidence labels: `Measured` means the result is in a raw record from this unit; `Reported` means it is written down only in notes or prose, with no raw record of the result; a record the host cached while the unit was attached counts as `Measured` and says "cached host record". `Inferred`, `Third-party`, `Official` and `Not tested` are as defined in the README legend.

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| macOS audio device | "Ableton Push 3 Audio", USB, 16 in / 16 out | Measured | S09 | `R002:7-11` |
| macOS sample rate | Nominal 44.1 kHz; 44.1, 48, 88.2 and 96 kHz offered | Measured | S09 | `R002:11` |
| Windows capture | One endpoint, "Line (Ableton Push 3)", opened at 16 ch / 44.1 kHz (prose) | Reported | S12 | `R119:333-334` |
| Windows playback | One endpoint, "Speakers (Ableton Push 3)", 2 ch (cached registry value as transcribed in the notes, unit absent when read; the S14 listing shows the endpoint but not its format); never opened | Reported | S02, S14 | `R119:164-171`; `R015:129` |
| Channels carrying signal | ch1 = input 1; ch5-8 constant DC; ch3-4 and ch9-16 exact zeros (S09 raw record; S03 is notes only) | Measured | S03, S09 | `R120:22-37` |
| Input 1 | Balanced (differential), deduced from two results: a left-only and a right-only tone read the same (S09, raw), and identical left and right cancel (README prose, no file kept) | Inferred | — | `R121:13`, `R122:13`, `R123:126-128` |
| Input type (relative to LINE) | INST +6.0 dB, HIGH +25.7 dB (Windows, input 1) | Reported | S11 | `R099:311-317` |
| Input gain | Settings of +5.5, +6, +7 and +20 dB measured +5.5, +6.0, +7.0 and +20.0 dB | Reported | S11 | `R099:313-314` |
| Frames without Live | Flow on macOS with Live not running (S09 raw record; S03 is notes only) | Measured | S03, S09 | `R001:109`, `R120:39` |
| Windows driver | Driver provider "Ableton" in the cached device-tree entries (cached host record) | Measured | S14 | `R015:188-203` |
| Windows ASIO driver | Driver "Ableton Push" registered under `HKLM\SOFTWARE\ASIO` | Reported | S02 | `R119:14,160` |
| Playback to the outputs | Not tested | Not tested | — | — |

## Devices and formats

### macOS (CoreAudio), S09 block d, Live not running

| Property | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Device name | "Ableton Push 3 Audio" (CoreAudio device id 97 on that day); manufacturer string "Ableton" | Measured | S09 | `R002:7-8` |
| Identifiers | DeviceUID `AppleUSBAudioEngine:Ableton:Ableton Push 3:` followed by the 8-character serial (serial redacted) and `:2,3` (carries the USB serial); ModelUID `Ableton Push 3 Audio:2982:1969` | Measured | S09 | `R002:9-10` |
| Transport and channels | USB, 16 in / 16 out | Measured | S09 | `R002:11` |
| Sample rate | Nominal 44100 Hz; available 44100, 48000, 88200, 96000 Hz | Measured | S09 | `R002:11` |
| Streams | One input and one output stream. Virtual format: float32, interleaved, 16 ch. Physical format: 24-bit integer, interleaved, 16 ch | Measured | S09 | `R002:12-13` |
| Reported input latency terms | Device latency 25 frames, safety offset 68, stream latency 0; with one 512-frame buffer the sum is 13.72 ms at 44.1 kHz | Measured | S09 | `R124:10` |
| Rate when the host does not set it | Nominal rate read 44100 Hz before and after a 10 s capture | Measured | S09 | `R120:38` |
| Capture integrity | 863 callbacks of 512 frames (441,856 frames) in 10.11 s; 0 all-zero callbacks, 0 sample-time discontinuities, 0 processor overloads | Measured | S09 | `R120:21,38` |
| Frames flow without Live | Yes, with Live not running and the Push not power-cycled (the warm case). The cold variant (after a power cycle) was not run | Measured | S09 | `R120:8,39`; `R123:86,135` |
| Earlier recording | 5 s ffmpeg (avfoundation) capture, 16 ch / 44.1 kHz, Live not running | Reported | S03 | `R001:108-110` |

The macOS version and build are not logged.

### Windows, S12 spike and cached entries (S02, S14)

| Property | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Capture stream opened | "Line (Ableton Push 3)" (device 3 of 9), 16 ch at 44.1 kHz, by a WASAPI shared-mode client inside Unreal Engine 5.8.2 (prose). The host library takes the channel count from the endpoint's mix format | Reported | S12 | `R119:333-334,338` |
| Endpoints | Exactly one capture endpoint ("Line") and one render endpoint ("Speakers") for the Push (cached host record, unit absent when read; the S02 notes agree) | Measured | S02, S14 | `R119:164`; `R015:129,147`; `R123:57` |
| Cached capture format | 16 ch, 44100 Hz, 32-bit container with 24 valid bits, channel mask 0 (not split into stereo pairs), PCM; a second cached key reads 16 ch float32. Endpoint state NOTPRESENT when read (the Push was on the Mac), so these are the values Windows stored at the last attach (cached registry values as transcribed in the notes; the query output was not kept) | Reported | S02 | `R119:164-171` |
| Cached playback format | "Speakers (Ableton Push 3)": 2 ch, 44100 Hz, 24 in 32, mask FL/FR (cached registry value as transcribed in the notes, unit absent when read). Never opened | Reported | S02 | `R119:168-171` |
| Default capture device | Windows made the Push the default capture device once it was plugged in | Reported | S12 | `R119:340` |
| Endpoint identity | Endpoint id `{0.0.1.00000000}.<GUID redacted>`, one devnode, in the registry read (S02, notes) and the cached host record (S14). The spike's record gives only the prefix of the id (`{0.0.1.00000000}.<GUID redacted>`, first group only), which matches | Measured | S02, S12, S14 | `R119:164,339`; `R015:147,245` |
| After a replug | Unreal's audio mixer logged sample-rate warnings when the Push's audio device re-arrived (the text of the warnings is not kept) | Reported | S08 | `R011:214` |
| Rate of the opened stream | 44.1 kHz, the endpoint's mix-format rate | Reported | S12 | `R119:164-171,333-334` |
| Rate selection | Whether the driver's control panel or an ASIO client changes the rate was not tested (the note's statement that the driver sets it and WASAPI shared mode cannot is general knowledge, not a test on this unit) | Not tested | — | `R119:174` |

### Windows driver

| Property | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Audio function `MI_01` | Setup class `AbletonAudio_sc`, driver provider "Ableton", friendly name "Ableton Push 3" (cached host record; the Push was absent when read on 2026-10-06) | Measured | S14 | `R015:188-191` |
| Child device node | `TUSBAUDIO_ENUM\VID_2982&PID_1969&MI_01&KS\...`, class MEDIA, driver provider "Ableton", parent is the `MI_01` node above (cached host record) | Measured | S14 | `R015:200-203` |
| Same two names on 2026-10-02 | The 2026-10-02 notes name "AbletonAudio" for the audio function (Push attached, SSH study); the audio research reads the `TUSBAUDIO_ENUM` node (whether the Push was attached at that read is not stated; the same note's registry reads found it absent). Taking the note's word "AbletonAudio" to mean the setup class `AbletonAudio_sc` is an interpretation (Inferred) | Reported | S02 | `R001:123`; `R119:156` |
| "Thesycon" | The `TUSBAUDIO` prefix is read as Thesycon's USB audio driver family. That the driver is Thesycon's is inferred from the enumerator name; the registry provider string is "Ableton" | Inferred | — | `R119:156` |
| ASIO | Driver "Ableton Push" registered under `HKLM\SOFTWARE\ASIO` (CLSID `{E42E3576-B7B4-4FDE-AE6C-E5FD34F7AD40}`), next to an "Ableton Move" entry. Never opened | Reported | S02 | `R119:14,160` |
| Control-panel process | A process named `AbletonAudioCpl` was running before Live was started | Measured | S04 | `R125:7` |
| What `AbletonAudioCpl` is | Taken to be the Push driver's control panel (a project note); Ableton's documentation names an "Ableton Push Control Panel" in the taskbar for setting the clock source | Inferred | — | `R126:8`; `R018:98` |
| How the driver was installed | Ableton: the Windows driver is a separate download | Official | — | `R018:101` |
| How the driver was installed | Forum moderator, 2023-05: it arrives with the Live 11.3.2 installer into the `Program Files\Ableton\Push Driver` folder of the Windows system drive | Third-party | — | `R119:160` |
| How it was installed here | Not recorded. The notes call the driver "Live-bundled" without a source | Not tested | — | `R001:123` |

## Channel map and levels

All captures below were made with the Push's input 1 as the only connected analogue input. Channel numbers are 1-16 as the host receives them.

### Channel map

| Channel | Observation | Evidence | Sessions | Source |
|---|---|---|---|---|
| ch1 | Carries input 1: every capture with a source on input 1 shows the signal here and nowhere else (S09 raw record; S03 is notes only) | Measured | S03, S09 | `R001:110`; `R121:13` |
| ch2 | At the noise floor in every capture (input 2 empty) (S09 is a raw record; S03 and S12 are notes only) | Measured | S03, S09, S12 | `R120:23`; `R119:337`; `R001:110` |
| ch2, input 2 | That input 2 appears on ch2 is not shown: no signal into input 2 was recorded per channel | Not tested | — | — |
| ch3-4 | Exact zeros (100.0 % of samples) | Measured | S09 | `R120:24-25` |
| ch5-8 | Constant DC offsets with an AC residue of -77 to -93 dBFS. The levels differ by channel in the raw record (table below) | Measured | S09 | `R120:26-29` |
| ch9-16 | Exact zeros (100.0 %). No ADAT source is recorded as connected | Measured | S09 | `R120:30-37` |
| ch9-16 as ADAT | The optical ADAT In/Out carries channels 9-16 | Official | — | `R018:99` |

### Levels, macOS, S09 block d (CoreAudio, float32 virtual format, Live not running)

Source for the signal rows: a Mac playing a test file through its headphone output (volume 100) into the Push's input 1 through the owner's patch cable. The cable and the Push's input-type and gain settings were not logged.

| Capture | ch1 RMS (peak), dBFS | ch2 RMS (peak), dBFS | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| No source, 10.11 s | -105.5 (-90.7) | -105.8 (-92.0) | Measured | S09 | `R120:22-23` |
| 1 kHz tone at -12 dBFS, left channel only, 5.03 s | -32.4 (-27.1) | -105.8 (-91.9) | Measured | S09 | `R121:13-14` |
| Same tone, right channel only, 5.02 s | -32.4 (-27.1) | -105.8 (-92.4) | Measured | S09 | `R122:13-14` |
| 10 clicks (10 ms, 1 kHz, -6 dBFS, 500 ms apart), left only, 8.02 s | -43.2 (-21.1) | -105.8 (-91.1) | Measured | S09 | `R124:13-14` |
| Identical left and right (no separate file kept) | ch1 stayed at the -105 dBFS noise floor (the signals cancel) | Not recorded | Reported | S09 | `R123:126-128` |

Differently conditioned readings of other days, as recorded:

| Capture | Reading | Evidence | Sessions | Source |
|---|---|---|---|---|
| macOS, ffmpeg, a source on input 1 (source and gain not described) | ch1 peak -26.8 dB; ch2 -90 dB (noise floor) | Reported | S03 | `R001:110` |
| Windows, Unreal WASAPI capture, 5 s, a weak signal on input 1 | ch1 RMS -60 dBFS, peak -42 dBFS; ch2 -106 dBFS | Reported | S12 | `R119:337` |

### ch5-8 DC offsets

| Record | ch5 | ch6 | ch7 | ch8 | Evidence | Source |
|---|---|---|---|---|---|---|
| S09 d, 10.11 s capture, DC (level) | +0.082136 (-21.7 dBFS) | +0.242459 (-12.3 dBFS) | +0.082003 (-21.7 dBFS) | +0.242783 (-12.3 dBFS) | Measured | `R120:26-29` |
| S09 d, 5.03 s capture with playback running | +0.082198 | +0.242480 | +0.082006 | +0.242835 | Measured | `R127:13-16` |
| S03 d, 2026-10-02 (ffmpeg, prose) | "peak = RMS = about -12.3 dB, constant" for all four | | | | Reported | `R001:110` |
| S13 and S12, Unreal capture spikes (prose) | "DC about 0.2426" for ch5-8 | | | | Reported | `R119:331,337` |

The origin of the offsets is unknown. They did not change with playback (S09 d), and the same unexplained DC was recorded on both days.

## Input type and gain

Reported for S11 block c on Windows (the readings survive in section 11 of a design decision record of the UE5 test host; the raw `ffmpeg astats` output is not kept): the owner fed one stereo programme into the Push (left to input 1, right to input 2). Levels were read in half-second windows from the Push's Windows recording device with `ffmpeg astats`. Input 1 was compared with input 2 held at LINE 0 dB, which cancels the programme's own level changes. The number of windows is not stated.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Type LINE | Reference (0 dB) | Reported | S11 | `R099:311-315` |
| Type INST | +6.0 dB relative to LINE (input 1) | Reported | S11 | `R099:314-315` |
| Type HIGH | +25.7 dB relative to LINE (input 1) | Reported | S11 | `R099:314-315` |
| Type, Ableton's figures | Line balanced with 0 dB gain; Instrument unbalanced, Hi-Z, +6 dB; High unbalanced, +26 dB (microphone level) | Official | — | `R018:89` |
| Gain scale | Settings of +5.5, +6, +7 and +20 dB measured +5.5, +6.0, +7.0 and +20.0 dB | Reported | S11 | `R099:313-314` |
| Gain range, 0 to +6.0 dB | 0 to +6.0 dB written in 0.5 dB steps and read back on input 1 (S11 b device check; write-side values in [04-sysex.md](04-sysex.md)) | Measured | S11 | `R063:2863-2986` |
| Gain range, +7 and +20 dB | +7 and +20 dB set and measured in the S11 c walk (decision record only; no raw log). No record covers the other values above +6 dB | Reported | S11 | `R099:313-314` |
| Write and read-back | 18 writes on input 1 (2 type changes, 16 gain steps) each read back equal; 18 of 18 marked DEVICE OK, 0 mismatches (count by grep over the file). User Port, Windows | Measured | S11 | `R063:2777-2986` |
| Input 2 | Held at LINE 0 dB while input 1 changed, so the two inputs are set independently. An input 2 value (type INST, gain +0.5 dB) was written and read back equal in an acquisition run | Reported | S11 | `R099:301-304,311-314` |
| Extra +20 dB digital gain | Ableton lists an additional +20 dB digital gain per input; where it is set is not stated. Whether the measured 0 to +20 dB gain is this stage was not tested | Official | — | `R018:90` |
| State read after the device check | Input 1 type LINE and gain 0 dB, input 2 type LINE and gain 0 dB (read at 13:28:48 on 2026-10-08, after the check's last writes) | Measured | S11 | `R009:10-21` |

HIGH measured 0.3 dB below Ableton's +26 dB; INST agrees. Which type or gain was set during the S03, S09, S12 and S13 captures is not logged.

## Audio without Live

| Claim | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| macOS, Live not running | The input reaches another application (ffmpeg, CoreAudio client); the audio device was present and delivered frames (S09 raw record; S03 is notes only) | Measured | S03, S09 | `R001:109`; `R120:39` |
| Forum claim | The input signal reaches other applications only while Live runs (2023-05, site moderator, tested in Reaper) | Third-party | — | `R119:186` |
| Windows capture, Live's state | The S12 spike opened the stream and read a signal, but its record does not say whether Live was running | Reported | S12 | `R119:333-337` |
| Windows, Live known to be not running | No capture was made with Live confirmed absent | Not tested | — | `R128:30-31` |
| After a cold power cycle (either OS) | Not tested | Not tested | — | `R129:244,259` |

## Host-side notes

These describe host software, not the device.

| Finding | Detail | Evidence | Sessions | Source |
|---|---|---|---|---|
| Unreal WASAPI backend | Opens the endpoint at its mix-format channel count and ignores a requested channel count; shared mode only; the 512 requested frames were not honoured (10 ms period) | Reported | S12 | `R119:335,338` |
| Unreal RtAudio backend on macOS | A requested channel count of -1 opens 8 channels, not 16 (confirmed by a run); opening with an explicit count of 16 at 44.1 kHz works | Reported | S13 | `R119:331-332` |
| Rate left unset on macOS | The library's preferred rate for the Push is 48 kHz and it is said to rewrite the device's nominal rate; from source reading, not shown by a run | Inferred | — | `R119:332` |

## Not yet verified

- Playback: sending sound to the Push's outputs, the 16 out channels on macOS against the 2 ch playback endpoint on Windows, headphone and main-out levels. Not tested on either OS.
- Windows capture with Live known to be not running, and after a cold power cycle on either OS.
- Input 2 on its own channel, with a strong signal; the content of ch5-8 (the offsets differ by channel pair) and the cause of the DC.
- ADAT: no ADAT source was ever connected; behaviour at 88.2 and 96 kHz (channel count) is undocumented.
- Sample-rate changes (48, 88.2, 96 kHz): offered by CoreAudio, never selected in a test; whether the Windows driver's control panel or an ASIO client sets the rate.
- ASIO: the driver is registered but was never opened; ASIO and WASAPI at the same time was not tested.
- How the Windows driver was installed on this machine (separate download or with Live).
- Whether the 0 to +20 dB gain is the "extra +20 dB digital gain" Ableton lists; the number of windows behind the S11 level figures.
- Whether the Volume encoder changes the analogue output level with Live not running.
- Device behaviour across a USB replug on macOS (the device id stability check was on hold).
- Raw records for the results that exist only in notes: the Windows capture (S12) and the macOS Unreal capture (S13), the S02 registry values, and the S11 c level readings.

See [08-gaps.md#audio](08-gaps.md#audio).

## Conflicting records

| Question | Record A | Record B | Which wins and why |
|---|---|---|---|
| Windows driver name: AbletonAudio or TUSBAUDIO? | `R001:123`: the audio function uses "AbletonAudio" | `R119:156`: PnP `TUSBAUDIO_ENUM\...`, Thesycon-style | Not a conflict. Both names exist in the device tree: `AbletonAudio_sc` is the setup class of the `MI_01` node and `TUSBAUDIO_ENUM` is the enumerator of its child node (`R015:188-191,200-203`). Both show provider "Ableton". This rests on reading the 2026-10-02 note's prose word "AbletonAudio" as the setup class name (Inferred); the note does not say which field it quotes. |
| ch5-8 levels | S09 d raw: ch5 and ch7 at -21.7 dBFS (DC +0.082), ch6 and ch8 at -12.3 dBFS (DC +0.2425) | S03 d prose: all four at -12.3 dB; the Unreal spikes: "DC about 0.2426" | The S09 d raw record wins for 2026-10-06. The 2026-10-02 figures have no raw output and name one value for four channels; whether that is a summary or a different state of the unit cannot be told. Unresolved. |
| Was Windows audio ever opened? | `R001:119`: Windows audio input without Live is an open item | `R119:333-337` (S12) and the test host's level check (S11 c, decision record): a capture stream was opened and read | The later, direct runs win: Windows capture was opened. The Live state of the S12 spike is not recorded, so the open item is only partly answered. |
| 16 outputs or 2? | macOS reports 16 out channels (S09) | Windows registry cache shows a 2-channel "Speakers" endpoint | Different layers (CoreAudio device against a cached WASAPI mix format). Neither was played to. Unresolved. |
| Does the input work without Live? | Forum moderator 2023-05: only while Live runs | Mac captures with Live not running (S03, S09) | The measurement wins for macOS, warm case. The claim is not tested on Windows or after a cold power cycle. |
| Driver origin | Ableton: separate download | Forum moderator: with Live 11.3.2 installer; notes: "Live-bundled" | Unresolved for this machine. The Official statement is the better-sourced description of Ableton's distribution; the installation route here was not logged. |
