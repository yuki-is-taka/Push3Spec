# Connection

**Scope.** This page records how the one Push 3 unit presents itself to a computer over USB and MIDI, which hardware mode and MIDI mode it was run in, how modes are switched, and how the unit shares its USB display interface and its MIDI ports with Ableton Live. It rests on sessions S01-S04 and S06-S11 (S05 is a single prose line), plus S14, a cached Windows device-tree listing read while the unit was on the Mac; host, OS, firmware and Live state of each session are in [09-sessions.md](09-sessions.md), and record-ID citations (`R012:45`) are explained in [10-sources.md](10-sources.md). Read these caveats first.

- Every session ran the unit tethered to a computer, which is hardware **Control Mode**; the notes name the mode for S01 only, so the other sessions are Inferred. **Standalone Mode was never used in any session**, and no record says whether the unit is a Standalone-capable model.
- All MIDI-mode behaviour was measured in Control Mode, with small samples: most results below are n = 1 to n = 8 per condition, and the sample count is given in each row.
- The Windows display wait with Live started first (520.7 s) is a **single observation**.
- MIDI channels, where they appear, are numbered 1-16. This page names a SysEx command by its command byte and value (for example `0A 01`); the full bytes are in [04-sysex.md](04-sysex.md).
- Firmware: "FW label 1.5.4" (from the notes) applies to S01 and S03 only. The Device Inquiry reply (build 91) was read in S01, S02, S03, S04 and S11. For every other session firmware was not logged.
- Evidence labels: `Measured` means the result is in a raw record from this unit; `Reported` means it is written down only in notes or prose, with no raw record of the result; a record the host cached while the unit was attached counts as `Measured` and says "cached host record". `Inferred`, `Third-party`, `Official` and `Not tested` are as defined in the README legend.

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| USB IDs | VID 0x2982, PID 0x1969; strings "Ableton" / "Ableton Push 3" | Measured | S01, S04, S09 | `R001:12`; `R002:46`; `R003:1859` |
| USB interfaces | 7 on macOS: IF#0 display, IF#1-3 audio, IF#4-5 MIDI, IF#6 "xPort" | Measured | S03 | `R004:6-13` |
| MIDI ports | 3 in + 3 out: Live Port, User Port, External Port (cables 1, 2, 3) | Measured | S01, S02, S04, S09 | `R005:1-2`; `R006:1-2` |
| Hardware mode, S01 | Control Mode (named in the notes: USB-C, power adapter connected) | Reported | S01 | `R001:4`; `R007:1` |
| Hardware mode, S02-S04 and S06-S11 | Control Mode in each (no record names it; deduced from the unit being driven from a computer over USB) | Inferred | — | [09-sessions.md](09-sessions.md) session table |
| Standalone Mode | Never used in any session | Not tested | — | — |
| MIDI modes | Live (`0A 00`), User (`0A 01`), Dual (`0A 02`); the host sets them; the Push answers every request sent on the Live or User Port with `0A <m>` on both Live and User ports (a request on the External Port gets no answer) | Measured | S03, S07, S09, S11 | `R008:4-25`; `R009:3-5,25-28` |
| Power-on MIDI mode | Dual (input on both ports right after power-up); no raw log | Reported | S03 | `R001:97` |
| User button | CC 59 on both Live and User ports in Live and User modes (Dual mode not tested for the button); the Push does not change mode by itself, Live does | Measured | S03, S04, S08 | `R010:17-18,150-152`; `R011:85`; `R012:38-43` |
| Display interface | One host process holds USB IF#0; the second claimant is refused (macOS `LIBUSB_ERROR_ACCESS`, Windows Win32 5) | Measured | S03, S04 | `R013:4`; `R003:1859` |
| MIDI port sharing | Several clients opened the same Push ports at once on both OSes; no exclusive open recorded | Measured | S02, S03, S04 | `R014:1-3` |

## USB identity and interfaces

### Identity and descriptor facts

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Vendor / product ID | 0x2982 / 0x1969 (same IDs on macOS and Windows) | Measured | S01, S04, S09 | `R001:12`; `R002:46`; `R003:1859` |
| Strings | vendor "Ableton", product "Ableton Push 3" (IOKit) | Measured | S09 | `R002:46` |
| USB serial | 8 characters, serial redacted (embedded in the CoreAudio DeviceUID) | Measured | S09 | `R002:9,46` |
| Same serial on the Windows PC | Windows PnP instance `USB\VID_2982&PID_1969\` followed by the 8-character serial (serial redacted) (cached host record, unit absent when read), the same serial as on macOS | Measured | S09, S14 | `R002:9`; `R015:184-187` |
| Same unit on both hosts | The same serial in the Mac's listing and in the Windows device tree means the one unit has been attached to both hosts | Inferred | — | `R002:9`; `R015:184-187` |
| bcdDevice | 0x0100, read from the Windows hardware ID suffix `REV_0100` (cached host record); no descriptor dump was ever taken | Measured | S14 | `R015:187` |
| Display interface IF#0 | name "Ableton Push 3 Display", vendor-specific class 255, bulk OUT endpoint address 0x01, wMaxPacketSize 512 | Measured | S01, S02 | `R016:1`; `R017:1`; `R001:12` |
| Endpoints of IF#1-IF#6 | Not recorded | Not tested | — | — |
| Bus speed | High-speed USB 2.0 implied by the 512-byte bulk packet size; speed was never read from a descriptor | Inferred | — | `R016:1` |
| Parent hub | A hub VID 0x0424 / PID 0x2534 is the parent of the Push on both hosts (macOS: by IOKit location ID, 0x02140000 under 0x02100000; Windows: the cached PnP parent; a cached host record) | Measured | S09, S14 | `R002:45`; `R015:186` |
| Hub inside the Push or in the cabling | Not examined: no record says whether the hub is inside the Push or part of the user's cabling | Not tested | — | — |
| Computer link and power | USB-C, USB 2.0, USB-PD; 5 V / 3 A needed to power the Push | Official | — | `R018:109` |
| Unit without the computer link | With the USB cable pulled the unit stayed on (own power) and showed its built-in "connect to Live" screen | Reported | S06 | `R001:253` |
| Device Inquiry identity | family 0x1969 (equals the PID), member 3, software 1.0, build 91; byte-identical in every read (bytes in [04-sysex.md](04-sysex.md)) | Measured | S01, S02, S03, S04, S11 | [1] |

Sources for this table:

1. `R005:4` (S01); `R006:3` (S02); `R019:10-11` (S04); `R009:7-8` (S11); `R020:200` (S03, Live's own start-up request).

The notes' firmware label "1.5.4", the Device Inquiry "sw 1.0 build 91" and Ableton's published "Push 2.4.6" are three numbering schemes; no record maps one to another (see [09-sessions.md](09-sessions.md), "Firmware").

### Interface map on macOS (Live running, then quit)

Taken in S03 block a, 07:53:53 with Live running and 07:57:29 after Live quit. "Owner" is the process or driver that holds the interface.

| Interface | Name | Owner, Live running | Owner, Live quit | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| IF#0 | Ableton Push 3 Display | Live's helper `Push3` (pid 48415) | none | Measured | S03 | `R004:7`; `R021:3` |
| IF#1 | Ableton Push 3 Audio | `usbaudiod` | `usbaudiod` | Measured | S03 | `R004:8`; `R021:4` |
| IF#2 | Ableton Push 3 Audio Out | `usbaudiod` | `usbaudiod` | Measured | S03 | `R004:9`; `R021:5` |
| IF#3 | Ableton Push 3 Audio In | `usbaudiod` | `usbaudiod` | Measured | S03 | `R004:10`; `R021:6` |
| IF#4 | Ableton Push 3 MIDI | none (audio-control nub) | none | Measured | S03 | `R004:11`; `R021:7` |
| IF#5 | Ableton Push 3 MIDI | `MIDIServer` | none | Measured | S03 | `R004:12`; `R021:8` |
| IF#6 | Ableton Push 3 xPort | none, no driver | none | Measured | S03 | `R004:13`; `R021:9` |

The purpose of IF#6 (xPort) is unknown; it was never used, with or without Live. The audio function is IF#1-3 and the MIDI function IF#4-5 (Inferred from the Windows interface numbers MI_01 and MI_04 below; `R015:172-201`).

### Driver binding on Windows

| Interface | Binding | Evidence | Sessions | Source |
|---|---|---|---|---|
| Composite device | Microsoft composite driver (`usbccgp`) | Reported | S02 | `R001:123` |
| IF#0 display | Microsoft WinUSB, bound automatically; opens from user space with no administrator rights through SetupAPI and WinUSB, or through the libusb-package DLL (the WinUSB open is the S06 log line cited in the Source column; S02 and S04 are notes only) | Measured | S02, S04, S06 | `R001:17,123,190-191`; `R022:3` |
| MIDI function | Microsoft class driver (`usbaudio`) | Reported | S02 | `R001:123` |
| Audio function | An Ableton-supplied audio driver (naming and the notes' conflict are resolved in [06-audio.md](06-audio.md)) | Reported | S02 | `R001:123` |
| IF#6 xPort | No driver (device in Error state) | Reported | S02 | `R001:123` |

The S02 rows above rest on the notes' transcription of the PnP query; the query output itself was not kept. A cached Windows PnP listing read on 2026-10-06 (the Push was attached to the Mac at that moment, so every entry reads "not present") shows the same four device nodes, MI_00 display, MI_01 audio, MI_04 MIDI and MI_06 xPort (Measured, cached host record, S14; `R015:172-201`).

## MIDI port names

The Push exposes one MIDI function with **three cables**. Cable 1 is the Live Port, cable 2 the User Port, cable 3 the External Port; this holds on both OSes.

### macOS (CoreMIDI)

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Port names | In and out, three each: "Ableton Push 3 Live Port", "Ableton Push 3 User Port", "Ableton Push 3 External Port" | Measured | S01, S09 | `R005:1-2`; `R023:12-14` |
| External Port traffic | Nothing arrived on the External Port in the 513 s capture, not even Active Sensing (the capture log's final counts have no External entry); the USB-A / DIN side was never exercised | Measured | S09 | `R024:24`; `R023:14-16,40` |

### Windows (WinMM names, served by Windows MIDI Services)

On the Windows PC the WinMM devices are published by the Windows MIDI Services service (`midisrv`), and "Default App Loopback" ports are present beside the Push's (Reported, S02; `R001:125`). The Push's Windows MIDI Services endpoint node is `SWD\MIDISRV\MIDIU_KSA_<id redacted>`: seen live in S04 (`R003:1862`) and as a cached entry read on 2026-10-06 with the Push on the Mac (S14; `R015:252-255`) (Measured; the S14 entry is a cached host record).

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| WinMM input names | "Ableton Push 3 MIDI", "MIDIIN2 (Ableton Push 3 MIDI)", "MIDIIN3 (Ableton Push 3 MIDI)" | Measured | S02, S04, S06, S07, S11 | [1] |
| WinMM output names | "Ableton Push 3 MIDI", "MIDIOUT2 (Ableton Push 3 MIDI)", "MIDIOUT3 (Ableton Push 3 MIDI)" | Measured | S02, S04, S06, S07, S11 | `R006:2`; `R022:5` |
| Role of each WinMM port | Order = cable order: first = Live, second = User, third = External. Confirmed by behaviour (Device Inquiry, palette and mode requests answered on the first two, never on the third) and by the endpoint's own block names | Measured | S04 | `R019:2-7,10-33`; `R025:21-30` |
| Endpoint as Windows MIDI Services sees it | one endpoint "Ableton Push 3 MIDI", KSA transport, native format MIDI 1.0 byte stream, VID 2982 PID 1969, 0 function blocks, 6 group terminal blocks (outputs #1-3, inputs #4-6), 1 group each. The block names are "Ableton Push 3 MIDI Live Port", "Ableton Push 3 MIDI User Port", "External Port" | Measured | S04 | `R025:21-27` |
| Cable to group | cable 1 = group 0 = Live Port; cable 2 = group 1 = User Port; cable 3 = group 2 = External Port | Measured | S04 | `R025:22-30` |
| Port names as libremidi's Windows MIDI Services backend lists them (host library naming) | inputs "Ableton Push 3 MIDI Live Port 4", "...User Port 5", "External Port 6"; outputs "...Live Port 1", "...User Port 2", "External Port 3". libremidi builds each name from the block name and the group terminal block number, and lists each block against its own direction (see Host-side notes); "External Port" carries no product prefix because the block name has none | Measured | S04, S08 | `R025:8-18`; `R011:38-40` |
| Port IDs after unplug and replug | The host-side port identity (the port id given by the test host's MIDI layer, a host-library value) was the same before and after one replug (S08) | Reported | S08 | `R011:141` |

Sources for this table:

1. `R006:1` (S02); `R014:1-3` (S04); `R022:4` (S06); `R026:20-21` (S07); `R009:1` (S11).

### Differences between the two OSes

| Aspect | macOS | Windows |
|---|---|---|
| Names | Device-supplied cable names: "... Live Port", "... User Port", "... External Port" | Legacy WinMM names built from the interface name and cable index ("MIDIIN2 (...)"); the cable names "Live Port" and so on appear only in the Windows MIDI Services (UMP) view |
| Order | Live, User, External | Live, User, External (same) |
| Library-added numbers | none | python-rtmidi appends its own list position to a WinMM name (for example "Ableton Push 3 MIDI 5"); the number changes with the other MIDI devices present and is not a device property |
| External Port | listed, no traffic | listed, no traffic; a request sent to cable 3 got no reply (n = 14 Device Inquiry, n = 1 palette read, n = 1 mode request) |

Evidence for this table: Measured, S02, S04, S09; sources as in the tables above plus `R027` (recount over the 43 merged logs, the same recount as in [07-timing.md](07-timing.md)).

## Hardware modes

| Mode | Observation | Evidence | Sessions | Source |
|---|---|---|---|---|
| Control Mode, S01 | The mode of S01 as the notes name it: the Push driven from a computer over USB-C with the power adapter connected | Reported | S01 | `R001:4`; `R007:1` |
| Control Mode, S02-S04 and S06-S11 | Assumed to be the same mode: in each the Push was driven from a computer over USB, and its three MIDI ports, the display interface and (S09) the audio interface were reached. No record names the mode | Inferred | — | [09-sessions.md](09-sessions.md) session table |
| Standalone Mode | Never used. Nothing is recorded about its USB identity, ports or enumeration | Not tested | — | `R001:1-258`; `R007:1-102` |
| How the mode is chosen | A display button picks Standalone or Control Mode; Live detects the Push automatically | Official | — | `R018:73` |
| Unit variant | No record states whether this is a Standalone-capable unit or a controller-only unit | Not tested | — | — |
| Live Setup without a Status tab | A note says Live's Setup showed no Status tab with the Push in Control Mode (Live running); this fits a controller-only unit but proves nothing | Reported | S03 | `R001:80` |
| Status tab and unit variant | Ableton says the Status tab exists only on Standalone-capable units | Official | — | `R018:33` |
| No frames sent in Control Mode | The display goes dark about 2 s after the last frame and shows the built-in "Connect Push to a computer..." guide about 12 s after (details in [05-display.md](05-display.md)) | Reported | S01 | `R001:22` |

## MIDI modes

### What each mode does

The MIDI mode decides which port carries the Push's own output. It is separate from the hardware mode and from the display. Sample: one Play-button press (CC 85) per phase in a single run (S03, Mac, request sent on the Live Port output).

| MIDI mode | Command | Play (CC 85) arrived on | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| (initial, no request sent) | none | Live Port only (Live had run earlier that morning) | Measured | S03 | `R008:1-3` |
| Live | `0A 00` | Live Port only (n = 1 after the first request, n = 1 after the last) | Measured | S03 | `R008:4-8,21-25` |
| Dual | `0A 02` | both Live and User Port (n = 1) | Measured | S03 | `R008:9-15` |
| User | `0A 01` | User Port only (n = 1) | Measured | S03 | `R008:16-20` |

Further observations by mode:

| Observation | Evidence | Sessions | Source |
|---|---|---|---|
| In User mode all input (pads, Play, encoder touch and turn, touch strip) arrived on the User Port only; the Live Port received nothing (n = 236 messages in one listen, all on the User Port) | Measured | S03 | `R028:1-237`; `R001:157` |
| In User mode on Windows all input again arrived on the User Port only; the Live Port carried only Active Sensing and the mode report | Measured | S07 | `R029:12`; `R026:34-35` |
| The User button is the exception: CC 59 reaches both ports in Live and User modes (the notes say every mode, from the Push 2 specification; Dual mode was not tested for the button) | Measured | S03, S04 | `R001:158`; `R014:4-7` |
| Entering User mode darkens all LEDs; the display is not affected. LED writes on the Live Port are ignored in User mode; the User Port drives all LEDs (prose record, no raw log) | Reported | S03 | `R001:152-156,173` |
| On Windows the User Port drove the LEDs in User mode (64 pads, 76 button CCs); the log records the sends, the owner watched the LEDs | Reported | S04 | `R030:1916-1918,2278-2280`; `R001:216-224` |
| After a power cycle the Play button reached both ports (Dual); on Windows pads, Play and an encoder reached both ports with Live not running. Mode is not stored. No raw log of either power cycle | Reported | S02, S03 | `R001:97,127` |
| On a Mac with no Live running and no mode request in the run, 15 of 15 one-second summaries showed identical traffic on both ports: pads, and in 2 of them also pitch bend (touch strip) (mode not queried) | Measured | S03 | `R031:29-167` |
| In the S01 guided capture (2026-10-01, mode not queried) only the User button also appeared on the User Port; the other 86 of 87 controls appeared on the Live Port only. The pad captures of the next morning (S03 block a, mode not queried) showed every pad message on both ports. The two records come from different sessions, so a different MIDI mode may explain them; that was not tested (see Conflicting records) | Measured | S01, S03 | [1] |

Sources for this table:

1. `R032` (and the other 11 S01 block files); `R033`; `R034` (S03 block a); `R035:147-152`.

### Setting the mode and the report

| Observation | n | Evidence | Sessions | Source |
|---|---|---|---|---|
| A request is accepted when sent on the Live Port or the User Port; the Push then sends `0A <m>` on **both** Live and User ports. Windows ground-truth client: every one of 71 reports appeared on cable 1 and on cable 2, none on cable 3 | 71 reports per cable (recount over 43 logs) | Measured | S04 | `R027` |
| macOS: four requests (`0A 00`, `0A 02`, `0A 01`, `0A 00`) each reported on both ports | 4 requests, 8 reports | Measured | S03 | `R008:5-6,10-11,17-18,22-23` |
| A request sent on the User Port (Windows) is reported on both ports | 2 (S11), 1 (S04 ground truth); in S06 block a the script logged only the User-port report (1) | Measured | S04, S06, S11 | [1] |
| A request sent on the External cable gets no report (the one report logged 748.8 ms later, by log-line stamps, was the answer to the next request, sent on the Live cable 699 ms after) | 1 | Measured | S04 | `R019:22-25,33-34` |
| Re-sending the mode already active is reported on both ports and changes nothing else (the blinking LED kept blinking) | 2 re-sends | Measured | S07 | `R036:48-51`; `R037:11-16`; `R026:153-155` |
| `0A 02` sent on exit puts the unit back into Dual and is reported on both ports | 1 (S11 probe), 1 (S08); in S06 block a it was sent but no report was logged | Measured | S06, S08, S11 | [2] |
| Report timing (see [07-timing.md](07-timing.md)): median 0.4 ms after the request on Windows (n = 41, harness stamps); 0.6 to 1.3 ms on macOS (n = 3); the S03 macOS log has only 10 ms resolution | n as listed there | Measured | S03, S04, S09 | [3] |
| The mode request is the Push 2 "Set MIDI Mode" command and works unchanged on Push 3 | — | Third-party | — | `R001:96` |

Sources for this table:

1. `R009:3-5,25-27` (S11); `R019:19-21` (S04); `R022:9-10` (S06).
2. `R009:25-27` (S11); `R012:61-62` and `R011:149-150` (S08); `R022:578-582` (S06).
3. `R008:4-6` (S03); `R027` (S04); `R024:7-9,31-33` and `R038:91-93` (S09).

The Push has no known command that reads the current mode; the report appears only as the answer to a set (Inferred from `R039` row 2 and the absence of any other mode report in a 513 s capture, `R023:33`).

## User button and hand-back

| Condition | Observation | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Message | CC 59, value 127 on press and 0 on release, on the Live Port and the User Port in the same millisecond | all presses below | Measured | S01, S03, S04, S08 | `R032`; `R010:17-18,155-156`; `R014:4-7` |
| Live running (Mac), Live backed off in User mode | Press 1: CC 59 at 15:46:09.383; Live sent `0A 01` to the Live Port 152 ms later (09.535); the Push reported `0A 01` on both ports in the same millisecond. Press 2: CC 59 at 10.953; Live sent `0A 00` 44 ms later (10.997); the Push reported it 3 ms after that (11.000). The Push never changed mode by itself. The order (Live's command, then the Push's report) is the order the MIDI Monitor listed the messages in a burst of about 460 LED messages from Live to the Push, and may not be the wire order | 2 presses | Measured | S03 | `R010:17-18,150-152,168-172,639-640` |
| Physical press while Live showed "User Mode Active" (Mac) | The Push's `0A 00` report (the answer to Live's command; the log shows only the Push's output) followed in the same second; Live resumed its polling and redrew every LED to its own state. Log: press and report in the same second; the notes count 86 poll replies, a recount of the file gives 91 `3A` replies in 109 s | 1 | Measured | S03 | `R040:2-7`; `R001:171` |
| Our own `0A 00` while Live sat backed off (Mac) | The Push reported `0A 00` but Live did not resume (no polls for more than 4 s; LEDs stayed as set). So a host-sent `0A 00` does not hand control back to Live. The raw log of this step was not kept | 1 | Reported | S03 | `R001:170` |
| Live backs off on `0A 01` (Mac) | After the report of a `0A 01` Live's polling stopped: last poll reply 0.5 s before the report, none in the next 36 s of the log | 1 | Measured | S03 | `R041:541-542,619-624` |
| No Live running (Windows) | A press changes nothing and no mode report follows: 3 presses, 2 messages each (press and release) on the User Port | 3 | Measured | S06 | `R001:227`; `R042:1953-1964` |
| No Live running (Windows, hosted controller bound) | CC 59 127 then 0 on Live and User ports in the same millisecond; no mode report | 1 | Measured | S08 | `R011:85`; `R012:38-43` |
| No Live running (Mac) | One press in the S01 guided capture (MIDI mode not queried) sent CC 59 on both ports, and no mode report was recorded; no run set the mode first, and the notes still call the use without Live "not yet tested" | 1 | Measured | S01 | `R032:349`; `R001:177` |
| Live running (Windows), another process holds the display | Press to Live's `0A 01`: 567-938 ms (8 presses); 4 of 8 flapped; return to Live (`0A 00`): 95-500 ms (4 presses) | 8 + 4 | Measured | S04 | `R014:4-177` |
| Live running (Windows), Live's own helper holds the display | Press to `0A 01`: 133-200 ms (6 presses); 0 flaps; return to Live: 26-34 ms (6 presses) | 6 + 6 | Measured | S04 | `R014:968-1689` |
| Live running (Windows), a bound host holds the display | 8 presses, 10 mode reports: 3 clean returns to User, 3 intended returns to Live, presses 7 and 8 flapped (2 of 5 attempts to reach User). The host sent nothing in response. Press times were not logged | 8 | Measured | S06 | `R042:2108-2132` |
| Live running (Mac), UE holds the display | No flap | 2 trials | Reported | S05 | `R001:207` |

A flap is `0A 01` followed by `0A 00` within 1 ms (0.18 to 0.32 ms apart in the 4 S04 flaps on the logger's clock; the order was always `0A 01` first in all 6 flaps): the LEDs blank for an instant and Live takes the MIDI side back. The cause is unverified; the notes suspect the contended Live helper retrying (Inferred, `R001:208`). Window focus was ruled out as the factor (`R001:205`). The arrival order of the two copies of a report (Live copy or User copy first) varies from event to event (see [07-timing.md](07-timing.md)).

Recount note: the press-to-report values for S04 are recomputed from the raw log (24 presses); they differ by a few ms from the notes (570-940, 133-199, 94-501, 25-33).

## Port ownership and coexistence with Ableton Live

### Display interface (USB IF#0)

| Observation | n | Evidence | Sessions | Source |
|---|---|---|---|---|
| First claimant wins (macOS). A script held IF#0 and wrote 30 fps; Live was started during the run, its helper appeared 152.3 s (run 1) and 31.5 s (run 2) after the script started, and never got the display. Our writes continued: 35,430 of 35,430 frames and 4,807 of 4,807 frames ok, 0 errors | 2 runs | Measured | S03 | `R020:196,4283`; `R041:53,611` |
| While Live's helper holds IF#0 (macOS), a second claim fails every time with `LIBUSB_ERROR_ACCESS` (-3) | 13 attempts in 3 runs, 0 ok | Measured | S03 | `R013:4-9`; `R043:4-8`; `R044:4-6` |
| Same result while Live shows "User Mode Active": the display is independent of the MIDI mode | 1 | Reported | S03 | `R001:173` |
| When our process released IF#0 the still-running helper took the display at once (owner saw Live's screen; the log records the release, not the screen); the helper keeps retrying | 1 | Reported | S03 | `R001:143`; `R041:590` |
| Windows, UE holds the display first, Live started afterwards: the display stays with UE at 60 fps, 0 errors; Live takes the whole MIDI side | 1 run | Reported | S04 | `R001:194` |
| **Windows, display requested while Live's helper is the only other candidate holder: refused for 520.7 s.** The UE spike's open returned Win32 5 "Access is denied" at 03:31:56.035 UTC, retried every 1000 ms, and succeeded at 03:40:36.739 UTC, 520.704 s after the first failure and after 524 failed attempts. **Single observation.** The log does not record what ended the wait. That the holder was Live's `Push3.exe` helper is an inference from the error signature (no process list was taken in that run). Writes after the open: first frame 11 ms, 0 write errors | 1 | Measured | S04 | `R003:1859,2357-2358`; `R045:85` |

### MIDI ports

| Observation | n | Evidence | Sessions | Source |
|---|---|---|---|---|
| macOS: CoreMIDI delivers the Push's input to every client, so a script on the Live Port sees Live's polling replies as well | 91 `3A` and 363 poll replies in one 109 s log | Measured | S03 | `R040:8-461`; `R001:149` |
| Windows: a Python logger had all three inputs open while Live was running; it received Live's poll replies (3,430 on the Live port in 900 s) and every mode report on both ports | 1 run, 28 mode events | Measured | S04 | `R014:1-3,9-12,3585-3586` |
| Windows: the same process opened the same input and the same output port twice; Live running did not block it | 1 | Reported | S02, S04 | `R001:125,195` |
| Windows MIDI Services is multi-client; no refusal or exclusive-open failure for the Push MIDI ports was ever recorded (absence of failure reports only) | — | Inferred | — | `R001:125,195` |
| External Port: no message on either OS; no reply to Device Inquiry, palette read or mode request on Windows | 14 + 1 + 1 requests (Windows) | Measured | S04, S09 | `R019:12-13,17-18,22-24`; `R024:24`; `R023:15-16` |
| External Port: requests on macOS (no Device Inquiry, palette read or mode request was sent to it) | 0 | Not tested | — | — |

### What Live does to the unit

| Observation | n | Evidence | Sessions | Source |
|---|---|---|---|---|
| Live takes the MIDI side at its start even if the host already holds the display and User mode: its own `0A 00` followed 2.56 s after its helper appeared; it overwrote palette entry 100; pads and buttons followed Live's state | 2 runs (2.562 s and 2.557 s) | Measured | S03 | `R020:196,202-203`; `R041:53,59-60,63` |
| Live's start-up burst: 336 SysEx messages to the Push in 2.18 s (brightness fade, strip setup, parameters, Device Inquiry, 128 palette writes, `0A 00`, pad mode, glide groups) | 1 capture | Measured | S03 | `R046:1-336` |
| Live's quit sends six pad-parameter SysEx writes and no other non-poll SysEx (the capture recorded SysEx only, so note and CC traffic at quit is not known) | 1 capture | Measured | S03 | `R047:1-6` |
| After Live quits, the MIDI mode, the pad mode (`1E 02`, MPE) and the palette stay until power-off | 1 | Reported | S03 | `R001:99-100` |
| Windows: Live started while a bound host held User mode: the Push reported `0A 00`, the display stayed with the host | 1 | Measured | S06 | `R042:2062` |
| Windows: an orderly release (`0A 02`, black frame, IF#0 released) while Live runs hands the whole Push to Live at once; Live starting after an orderly release takes display and controls normally | 1 + 1 | Reported | S06 | `R001:248,258` |
| Live's polling (`38` and `3A` requests, replies on the Live Port): the two `38` requests alternate, so one `38` reply about every 0.3 s, and a `3A` about every 1.2 s, on both OSes (Windows medians over a 900 s log: each `38` type every 0.60 s, `3A` every 1.20 s); polling stops while Live is backed off in User mode; meanings undocumented | see [07-timing.md](07-timing.md) | Measured | S03, S04 | `R041:619-624`; `R014:1-3586` |

## Host-side notes

These are findings about host software (libremidi, python-rtmidi, Windows MIDI Services, CoreMIDI). They are **not device behaviour**, but they change what a host sees from the device, so they are recorded here. Library versions are in [09-sessions.md](09-sessions.md) (S04 block b).

| Finding | Detail | Evidence | Sessions | Source |
|---|---|---|---|---|
| libremidi: output group ignored | A libremidi Windows MIDI Services send to "User Port 2" or "External Port 3" landed on cable 1; only a caller-encoded group 1 reached cable 2 (also through the MIDI 1.0 byte API) | Measured | S04 | `R048:21,90`; `R049` |
| libremidi: SysEx replies lost | The per-message input path completed 0 of 8 SysEx replies; the raw callback and the WinMM ground truth received 8 of 8 (recount: 8 `SYSEX-COMPLETE` lines on the raw path, none on the per-message path, 8 `GT RX` lines from the ground-truth client). So "mode reports=0" in the S04 block a spike logs (pre-fix library) is host loss, not silence from the device. Later runs used a patched libremidi fork (checkpoint A; commit hash omitted) | Measured | S04 | [1] |
| libremidi: stack overrun on large batches | AddressSanitizer reports a `stack-buffer-overflow` (a write past a stack buffer; the notes tie it to batches over 6 words) | Measured | S04 | `R050:2,47`; `R048:23,27` |
| libremidi: input stalls and crashes on large batches | After an 8-word batch, release builds stopped delivering input on every Push port for 2.3-7.6 s and then died with `0xC0000409` (notes only: no log in R051 carries the stall time or the exit code) | Reported | S04 | `R048:23,27` |
| libremidi: block direction inverted | The library lists the Push's ports against the blocks' own direction; masked on the Push because its groups are numbered symmetrically | Measured | S04 | `R048:20`; `R025:8-18` |
| Port matching by VID/PID fails with libremidi under in-box Windows MIDI Services | No Push port in libremidi's port view carried the USB identity 2982/1969 (the backend fills VID/PID only for USB-typed identifiers), so a matcher keyed on VID/PID found zero ports and the host had to bind through an explicit port row (the UE node logs behind this are not in the archive) | Reported | S08 | `R011:42-54` |
| Windows MIDI Services endpoint record and VID/PID | The SDK's own endpoint record does carry VID 2982 / PID 1969 (S04, SDK runtime 1.0.17-rc.4.25, not the in-box Preview 10 of S08) | Measured | S04 | `R025:21` |
| python-rtmidi suffix | python-rtmidi appends a list position to the WinMM names; it differs between sessions (5/6/7, 7/8, 0/1/2) | Measured | S02, S04, S06, S08, S10, S11 | `R006:1`; `R009:1` |
| CoreMIDI client creation can abort | Creating a CoreMIDI client right after `MIDIServer` stops can abort the process with -304 (not catchable in python-rtmidi); `MIDIServer` exits when it has no users; no -304 occurred in S09 | Reported | S03, S09 | `R001:86`; `R023:20-21` |
| Note On with velocity 0 over Windows MIDI Services, source analysis | The service translates a MIDI 2.0 (MT4) Note On with velocity 0 to velocity 1 on the way to a MIDI 1.0 device, so the Push would receive `90 nn 01` (a lit LED or stuck note); the audit marks it "not observed on hardware" | Inferred | — | `R052:226-233` |
| Note On with velocity 0 over Windows MIDI Services, measurement | With the production path (UMP MT2, MIDI 1.0 channel-voice messages in UMP) 64 Note On velocity 0 plus 76 CC 0 darkened every pad and button, nothing stuck lit (owner eyes-on; the log records the sends, not the light). The two rows do not contradict each other: they concern different message types on the wire into the service. The byte-API path (which converts to MT4) was never run with velocity-0 Note Ons on the unit | Reported | S04 | `R001:216-221`; `R030:2278-2280` |

Sources for this table:

1. `R048:22`; `R045:79-86`; `R053` (8 `SYSEX-COMPLETE`); `R054` (0 `SYSEX-COMPLETE`, 8 `GT RX`).

## Not yet verified

- Standalone Mode: USB identity, ports and enumeration; and whether the unit is a Standalone-capable model.
- Descriptor-level data: bcdUSB, bus speed, power draw, endpoints of IF#1-IF#6, the purpose of IF#6 (xPort), and whether the VID 0x0424 hub is inside the Push.
- Power-on MIDI mode (Dual): prose only, no raw log on either OS; not re-checked after any update.
- Which controls reach the User Port in Live and Dual mode (S01 showed only the User button there, the S03 pad captures every pad message; the mode was not queried in either).
- User button without Live on a Mac (one S01 press showed no mode report; no deliberate run); flap statistics (Windows 8, 6 and 5 presses; Mac 2 trials); cause of the flap.
- Hand-back by a host-sent `0A 00` (n = 1, raw log not kept); Live backing off (n = 1 log).
- The Windows 520.7 s display wait (n = 1): what ended it, and whether Live's helper was the holder.
- External Port: nothing behind it (USB-A, DIN) was ever attached.
- Mode behaviour after unplug and replug, and after Live quits, on Windows.

See [08-gaps.md#connection](08-gaps.md#connection).

## Conflicting records

| Question | Record A | Record B | Which wins and why |
|---|---|---|---|
| Does the User button need Live to do anything? | `R001:177` calls its use without Live "an inference, not yet tested" | `R001:227` (3 presses, S06) and `R011:85` (S08) show no mode change without Live | The later direct measurements win for Windows. On the Mac the one S01 press (no mode report) is the only record; no deliberate run exists. |
| How soon does Live answer a User press? | The notes and an earlier working summary say "about 150 ms" | Spy capture: 152 ms for press 1 but 44 ms for press 2 | The raw capture wins: Live's answer time varies (n = 2). |
| How many poll replies after the hand-back press? | `R001:171`: 86 | Recount of `R040`: 91 `3A` replies in 109 s | Immaterial; the recount is from the raw file. |
| How many group blocks does the Windows MIDI Services endpoint have? | Earlier working summary: 3 blocks | `R025:21-27`: 6 (3 outputs, 3 inputs) | The raw probe line wins. |
| Is the one MODE report 748.8 ms after a request on cable 3 a reply? | Working summary: unexplained | The log timeline: it answers the next request (cable 1, sent 699 ms later), and the harness's own summary attributes nothing to the cable-3 request | Resolved: cable 3 gave no report. |
| Why did "mode reports=0" appear in the S04 block a logs? | The device not reporting | `R045:79-86` and the probe: the pre-fix library dropped every SysEx | Host loss wins; the WinMM logger in the same era saw the reports. |
| Does Windows MIDI Services rewrite a velocity-0 Note On? | Source analysis: yes (MT4 path) | Measurement: no (MT2 path, owner eyes-on) | Both stand; different message types. See Host-side notes. |
| Which controls appear on the User Port outside User mode? | S01 guided capture (2026-10-01): only the User button; `R001:13` adds the pads, which come from the S03 pad captures of the next morning | S03 `mode_test`: Play appears on the Live Port only in Live mode and on both in Dual | **Unresolved.** The S01 and S03 a records are different sessions and neither queried the mode; Live mode in S01 and Dual in S03 a would explain both, untested. |
| Did the External ports show in the S08 boot listing? | `R011:41`: only Live and User at boot, External in the replug lines | The S04 probe listing shows all three outputs and inputs | **Unresolved**; the S08 host logs are not in the archive. |
