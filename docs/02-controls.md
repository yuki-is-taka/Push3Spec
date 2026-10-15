# Controls

This page lists every physical control of the Push 3 unit and the MIDI message it sends: the 64 pads, the eight encoders above the display, the Volume and Swing & Tempo encoders, the jog wheel, the touch strip, the D-pad, the 70 plain buttons, and the pedal jacks and External Port, which were never exercised. It rests on S01 (the guided control map, macOS, 2026-10-01), S03 (pad captures, mode routing and User-mode input, macOS, 2026-10-02) and, for User mode, S07 to S11 (mostly Windows); S02, S04 and S06 add the User button and the Windows power-on state. Session columns, hosts and firmware are defined in [09-sessions.md](09-sessions.md); a source is cited as a record ID and line number (`R012:45`; [10-sources.md](10-sources.md)). MIDI channels are written 1-16 throughout. Note and CC numbers are decimal; status bytes are given in hex (`0x90`, `0xB0`) only where the logs print them.

Read these caveats first.

1. **The full map is a single recording.** All 70 plain buttons and one gesture each for Volume, Swing & Tempo, the jog wheel, the touch strip and the D-pad centre were measured once, in Control Mode, on macOS, with Live not running, reading the Live Port with the User Port also open (S01; the jog wheel also appears in a short unguided recording of the same night, section 3.4). The MIDI mode (`0A`) was not queried that night. The same numbers were seen again in User mode for only 32 of the 70 plain buttons (section 3.7; six of them only in a decoded Windows log that does not print the values), and for the encoders, Volume, Swing & Tempo (turn), jog wheel, touch strip and pads. The remaining 38 plain buttons were never recorded in User mode or on Windows.
2. **Never tested.** Poly aftertouch input (pad mode `1E 01`); any pedal input; any External Port input; a pad strike at velocity 127 (highest strike 100, highest slide re-trigger 126); the meaning of CC 114 (seen five times, section 3.9); vertical jog tilts; whether the Volume knob changes the analogue level without Live.
3. **Which port carries what in the S01 recording is unexplained.** In S01 only the User button was also copied to the User Port, although both ports were open for every block; no pad was recorded in S01. The pads were recorded the next morning (S03 block a, mode also not queried) and appeared on both ports. Other records (Dual mode, Windows power-on) show every control on both ports. The readings are not reconciled; see section 2 and Conflicting records. The leading candidate reading (Inferred) is that the unit was in Live mode in S01 and in Dual mode by S03 block a, after a power cycle that was not logged.
4. **The pad mode of the pad captures is not logged.** `R033` (Mono aftertouch behaviour) and `R034` (MPE behaviour) are labelled by file name and identified by what they contain; no `1E` write for them is in the archive.

## At a glance

| Fact | Value | Evidence | Sessions |
|---|---|---|---|
| Plain buttons (70) | Control Change, ch1, 127 on press, 0 on release; no velocity, no pressure, no Note messages; only 127 and 0 seen | Measured | S01, S09, S10, S11 |
| Encoder touch (encoders 1-8, Volume, Swing & Tempo, jog) | Note On ch1, velocity 127 on touch; release is Note On velocity 0, never Note Off | Measured | S01, S09, S10, S11 |
| Encoder turn | Control Change ch1, relative, 7-bit two's complement: right = 1.., left = 127..; steps seen -4 to +5 | Measured | S01, S09, S10, S11 |
| Pads | 64 pads, Note 36 to 99; 36 of 64 notes seen | Measured | S03, S09, S10, S11 |
| Pad layout grid | Note = 36 + 8 x (row - 1) + (column - 1), fitted to 8 anchor pads (section 3.1.2) | Inferred | — |
| Pad release | Note Off (`0x80`) velocity 64 on the strike channel; never Note On velocity 0 | Measured | S03, S09, S10, S11 |
| Pad expression (Mono, MPE) | Mono: channel pressure ch1; MPE: per strike ch2-16 with channel pressure, CC 74, pitch bend | Measured | S03, S09, S10, S11 |
| Pad expression (Poly) | Not tested: no pad strike was recorded in Poly mode | Not tested | — |
| Pad velocity | 1 to 126 seen; strikes reach 100; 127 never seen | Measured | S03, S09, S10, S11 |
| Touch strip | Touch Note 12; position pitch bend ch1 in 64-count steps (256 positions); PB 0 on release | Measured | S01, S03, S10 |
| Jog wheel | Touch Note 11, turn CC 70, press CC 94, tilt left CC 93, tilt right CC 95 | Measured | S01, S06, S07, S08, S09 |
| D-pad | Arrows CC 46 up, 47 down, 44 left, 45 right; centre CC 91 plus touch Note 13; works in User mode | Measured | S01, S11 |
| User button | CC 59 127/0 on the Live Port and the User Port where both ports were read (S01, S03, S04, S08); S06 read the User Port only (section 3.7) | Measured | S01, S03, S04, S06, S08 |
| Port routing | Follows `0A` MIDI mode (tested with Play only); S01 recording not explained | Measured | S01, S03 |
| Active Sensing | `FE` every 270 ms on Live Port and User Port (User mode without Live; also with Live running, macOS); none on External Port (S09 only) | Measured | S03, S07, S09 |
| Active Sensing in Dual mode | Not tested: the Python scripts of S01 and S03 discarded `FE` | Not tested | — |
| Pedal jacks (input) | Not tested: no pedal was connected in any session | Not tested | — |
| Pedal jacks (manual) | Ableton documents functions only, no MIDI numbers | Official | — |
| External Port input (captures) | No message arrived on it in any capture where it was open | Measured | S03, S04, S07, S09 |
| External Port input (device attached) | Not tested: no DIN or USB-A device was attached | Not tested | — |

## 1. Where each control was recorded

| Case | Mode and port | Host | Controls recorded | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| Guided map, 87 gesture rows | Control Mode; MIDI mode not queried; Live Port read, User Port also open; Live not running | macOS | 70 plain buttons; one gesture set each for Volume, Swing & Tempo, jog wheel, touch strip, D-pad centre | Measured | S01 | `R007:8-94` |
| First 90 s recording (jog) | Same, Live Port | macOS | Jog touch, 3 tilts left, 2 tilts right, 2 presses, 1 right turn | Measured | S01 | `R005:134-169` |
| Encoders 1-8 | Same, Live Port | macOS | Touch Notes 0-7, right turns CC 71-78 | Measured | S01 | `R005:170-438` |
| Pad captures | Control Mode, Live not running; both ports; pad mode by file name | macOS | Pad layout anchors; Mono and MPE behaviour | Measured | S03 | [1] [2] [3] |
| Play across MIDI modes | Host set `0A 00`, `0A 02`, `0A 01`, `0A 00`; Live Port and User Port read | macOS | Play only | Measured | S03 | `R008:1-26`; `R055:11` |
| Dual-looking state | Mode not queried; Live had run and quit earlier that day | macOS | Pads only (15 one-second summaries) | Measured | S03 | `R031:29-167` |
| User mode, Live running but backed off | `0A 01`; three ports open | macOS | Pads (one strike), Play, encoder 8 touch and turn, touch strip | Measured | S03 | `R028:1-237` |
| Windows power-on | Described as Dual mode; prose only, no raw input log in the archive | Windows | Pads, Play, encoder 8 on both ports | Reported | S02 | `R001:127` |
| Device-mock run (40 min) | `0A 01` acknowledged; User Port read; the log prints control names and press / release, not bytes | Windows | Encoder and jog touches, jog press and tilts, Volume (left turns, push), Swing & Tempo (left turns), Tap Tempo, Lower display buttons 1-3, Device, Mix, Clip, Session (CC 34), Page Left, Page Right, Play | Measured | S06 | [4] [5] |
| Jog, Learn in User mode | `0A 01` set by the host; Live not running | Windows | Jog touch, turn, press; Learn | Measured | S07 | `R026:34-35, 70-72` |
| Learn, jog tilt in User mode | `0A 01`; Live and Unreal Editor not running | macOS | Learn; jog touch, turn, tilts | Measured | S09 | [6] [7] |
| Working mock, Mac segment | `0A 01`, pads in MPE; date inferred | macOS | Encoders, jog, 17 plain buttons, pads, strip | Measured | S09 | `R056:1-57189` |
| Working mock, Windows segment | `0A 01`, pads in MPE | Windows | Encoders, jog, Volume, Swing & Tempo, 7 plain buttons, pads, strip | Measured | S10 | `R056:57190-90353` |
| Settings-page sessions | `0A 01`, pads in Mono (`1E 00`) | Windows | Encoders, jog, D-pad (arrows, centre), 12 of the 16 display buttons, Setup, Learn, Volume, Swing & Tempo, pads | Measured | S11 | [8] [9] [10] [11] |
| User button | Mode not queried (S01); Live and User modes (S03, S04, S06, S08); Live running and not running (section 3.7); both ports read in S01, S03, S04, S08, User Port only in S06 | Both | CC 59 only | Measured | S01, S03, S04, S06, S08 | [12] [13] [14] [15] [16] |

Sources for this table:

1. `R033:7`
2. `R034:7`
3. `R057:7`
4. `R022:1-582`
5. `R058:41-53`
6. `R023:40-42`
7. `R059:7-10`
8. `R060:1-10270`
9. `R061:1-1337`
10. `R062:1-8616`
11. `R063:1-3028`
12. `R032:349`
13. `R042:1955`
14. `R010:17-18`
15. `R014:4-1691`
16. `R011:85`

**S06 rows (device-mock run).** The log was written by a host script that decoded the incoming bytes: it logs the four pin buttons (Device, Mix, Clip, Session) as DOWN (value 64 or more) and up (below 64), every other CC outside its encoder 1-8 and jog-turn handlers as DOWN only (a CC outside its name table prints as `cc<number>`, for example `cc79`), and counts encoder 1-8 and jog turns without logging them. The raw bytes were not kept (`R058:41-53, 395-443`). The S06 rows stay Measured because the log was written live from this unit's input; the CC numbers and the 64 threshold are the script's decoding.

The button counts for the two mock segments are the numbers of distinct plain-button CCs seen in each (section 3.7). Pad counts in the settings sessions include light brushes.

## 2. Ports and MIDI mode: what the controls send where

The mechanics of switching the mode are in [01-connection.md](01-connection.md); the `0A` bytes are in [04-sysex.md](04-sysex.md). This table gives only what the controls themselves sent.

| Condition | Controls | Live Port | User Port | External Port | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|---|
| Host set `0A 00` (Live mode) | Play CC 85 | Yes | No | Not recorded | Measured | S03 | `R008:7-8, 24-25` |
| Initial state of that run (no `0A` sent; Live had been started earlier that morning) | Play CC 85 | Yes | No | Not recorded | Measured | S03 | `R008:2-3` |
| Host set `0A 02` (Dual mode) | Play CC 85 | Yes | Yes | Not recorded | Measured | S03 | `R008:12-15` |
| Host set `0A 01` (User mode) | Play CC 85 | No | Yes | Not recorded | Measured | S03 | `R008:19-20` |
| User mode, Live running but backed off | Pads, Play, encoder 8 (Note 7, CC 78), touch strip (Note 12, pitch bend): 236 messages | 0 | 236 | 0 | Measured | S03 | `R028:1-237` |
| User mode, Live not running | Jog (Note 11, CC 70, CC 94), Learn CC 81 | Only `FE` and one mode report | All | None stated | Measured | S07 | `R026:34-35, 70-72` |
| User mode, Live not running, hosted by Unreal Editor | Jog touch (Note 11, 4 touches, no CC) | No (tap) | Yes | Not tapped | Measured | S08 | `R011:78` |
| User mode, Live not running | Learn CC 81; jog touch, turn, tilts | Only `FE` and one mode report | All | Nothing | Measured | S09 | [1] [2] |
| S03 pad captures, the morning after S01 (mode not queried, Live not running) | Pads | Yes | Yes, identical, within 1 ms | Not opened | Measured | S03 | `R033:7`; `R034:7` |
| S01 recording (mode not queried) | User button CC 59 | Yes | Yes | Not opened | Measured | S01 | `R032:349` |
| S01 recording (mode not queried) | All other buttons, encoders, jog, touch strip (every gesture in 12 blocks) | Yes | No | Not opened | Measured | S01 | `R007:8-94` |
| Mac, mode not queried (Live had run and quit that day; a power cycle since then is not recorded) | Pads | Yes | Yes, identical in 15 of 15 one-second summaries | Opened, no message listed | Measured | S03 | `R031:29-167` |
| Power-on, Windows (notes say Dual mode) | Pads, Play CC 85, encoder 8 | Yes | Yes | Not recorded | Reported | S02 | `R001:127` |
| Power-on, Mac (notes say Dual mode) | Play CC 85 | Yes | Yes | Not recorded | Reported | S03 | `R001:97` |
| User button, modes and Live states of section 3.7 (both ports read) | CC 59 127/0 | Yes | Yes | None in S04; not opened (S01), not in the capture (S03), not tapped (S08) | Measured | S01, S03, S04, S08 | [3] [4] [5] [6] |
| User button, User mode, Live not running (the spike read the User Port only) | CC 59, 2 messages per press (press and release) | Not read | Yes, no mode report | Not read | Measured | S06 | [7] |

Sources for this table:

1. `R023:40`
2. `R059:7-12`
3. `R032:349`
4. `R010:17-18`
5. `R014:4-1691`
6. `R011:85`
7. `R042:1955, 1958, 1961`

The two power-on rows are Reported: they rest on prose only, and no raw input log of either observation is in the archive (the S02 log `R006` has no input recordings). The Mac pad summaries in the Dual-looking row list Note On, Note Off, channel pressure and pitch bend counts only, with no note numbers or channels, so "pads" is read from the message types. The S01 rows and the S03 pad-capture row are about 7.5 hours apart; whether the unit was power-cycled or Live was run in between is not recorded. The Push 2 interface manual, cited second-hand, gives Live as the initial MIDI mode (`R064:121`, Third-party); the Push 3 notes report Dual after power-up. The leading candidate reading of the S01 and S03 rows (Inferred) is given under Conflicting records.

## 3. Control map

Values are the bytes the Push sent. "Press 127 / release 0" means Control Change value 127 on press and 0 on release. Hold times in the logs (118 to 341 ms in S01) are finger holds, not a device property. Panel names follow the Ableton manual as recorded in `R065`; DrivenByMoss names the same numbers differently for CC 80 (Files), CC 81 (Help), CC 33 (Browse), CC 32 (Add Track), CC 65 (Capture MIDI) and CC 34 (Session Display) (`R065:39`). Every S01 number matched the DrivenByMoss or Ableton expectation where one existed; the jog wheel touch (Note 11) had none.

### 3.1 Pads

#### 3.1.1 Layout anchors

| Pad (position) | Note | Evidence | Sessions | Source |
|---|---|---|---|---|
| Bottom row, column 1 (bottom-left corner) | 36 | Measured | S03 | `R033:3783` |
| Top row, column 8 (top-right corner) | 99 | Measured | S03 | `R033:3831` |
| Pad A: row 4 from bottom, column 4 from left | 63 | Measured | S03 | `R033:7` |
| Right neighbour of Pad A (row 4, column 5) | 64 | Measured | S03 | `R034:14514` |
| Row 4, column 1 (left end of Pad A's row) | 60 | Measured | S03 | `R057:7` |
| Row 4, column 8 (right end of Pad A's row) | 67 | Measured | S03 | `R057:11774` |
| Pad directly above Pad A (row 5, column 4) | 71 | Measured | S03 | `R057:22363` |
| Pad directly below Pad A (row 3, column 4) | 55 | Inferred | — | `R033:1673` |

The owner was prompted for each position and the first Note On was compared with the expected note. The note 55 row is Inferred because the up-down slide at Pad A produced only Notes 55 and 71, and 71 is confirmed above. In `R033:2194` the prompt asked for the second pad from the right and the owner pressed Pad A's right neighbour (Notes 64 and 63; expected 66, `match` false); the prompt was changed to "right neighbour" for the later run. `R034` and `R066` are byte-identical.

#### 3.1.2 Layout grid

| Pad row (from bottom) | Column 1 to 8 (left to right) | Evidence | Sessions | Source |
|---|---|---|---|---|
| 1 | 36 37 38 39 40 41 42 43 | Inferred | — | [1] [2] |
| 2 | 44 45 46 47 48 49 50 51 | Inferred | — | [1] [2] |
| 3 | 52 53 54 55 56 57 58 59 | Inferred | — | [1] [2] |
| 4 | 60 61 62 63 64 65 66 67 | Inferred | — | [1] [2]; 60, 63, 64, 67 measured |
| 5 | 68 69 70 71 72 73 74 75 | Inferred | — | [1] [2] |
| 6 | 76 77 78 79 80 81 82 83 | Inferred | — | [1] [2] |
| 7 | 84 85 86 87 88 89 90 91 | Inferred | — | [1] [2] |
| 8 | 92 93 94 95 96 97 98 99 | Inferred | — | [1] [2]; 99 measured |

Sources for this table:

1. `R033:3783, 3831`
2. `R057:7, 11774`

All rows follow the formula note = 36 + 8 x (row - 1) + (column - 1), fitted to the 8 anchors of section 3.1.1. Every Note On seen on a pad in any log lies inside 36 to 99 and agrees with the formula; the logs do not record which pad was struck outside the prompted captures.

#### 3.1.3 Pad notes seen as Note On

| Notes seen (velocity above 0) | Count | Evidence | Sessions | Source |
|---|---|---|---|---|
| Guided pad captures: 36, 55, 60, 61, 63, 64, 67, 71, 99 | 9 | Measured | S03 | [1] [2] [3] |
| Mac User-mode listen: 41 | 1 | Measured | S03 | `R028:4` |
| Mac mock: 37, 38, 39, 40, 45, 48, 53, 56, 61, 64, 69, 77, 79, 87, 88, 93, 96 | 17 | Measured | S09 | `R056:1-57189` |
| Windows mock: 36, 44, 45, 46, 47, 53, 54, 55, 60, 61, 68, 69, 70, 72, 76, 79, 86, 87, 93, 95 | 20 | Measured | S10 | `R056:57190-90353` |
| Windows settings session: 43 | 1 | Measured | S11 | [4] |
| Union | 36 of 64 | Measured | S03, S09, S10, S11 | [1] [5] [6] [4] |

Sources for this table:

1. `R033:7`
2. `R034:7`
3. `R057:7`
4. `R061:473-506`
5. `R028:4`
6. `R056:1-90353`

Not seen as Note On in the scanned logs (28 notes): 42, 49, 50, 51, 52, 57, 58, 59, 62, 65, 66, 73, 74, 75, 78, 80, 81, 82, 83, 84, 85, 89, 90, 91, 92, 94, 97, 98. The scan covered the guided captures, the mock logs, the settings logs, the coexistence logs and the User-mode listen; the raw spike logs of S04, S06 and S08 were not scanned for pad notes. During the S01 guided blocks the bottom-row pads 40 to 43 were the script's own control pads and their messages were removed from the saved captures (`R035:22-24, 98-110`).

#### 3.1.4 Pad messages

| Message | Mode | Bytes and range | Behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| Strike | All | Note On (`0x90`), note 36-99; velocity 1 to 126 seen | Channel 1 in Mono (S03 `R033`, S11); channels 2-16 in MPE (next rows). Strike velocities: Mono 25-89 (11 strikes), MPE 30-100 (9), pitch-bend option runs 25-46 (7), Mac mock 1-80 (29), Windows mock 1-83 (32), Windows Mono 1-26 (5), Mac User-mode listen 74 (1), slide captures 1-126 (7 Note Ons, 4 of them re-triggers) | Measured | S03, S09, S10, S11 | [1] [2] [3] [4] [5] [6] [7] [8] |
| Strike velocity ceiling | All | 127 never seen from a strike; highest strike 100 (MPE, strong strike); 126 on a slide re-trigger | Without Live, hard hits stopped near 89 in Mono. The velocity curve read back with `27 08` is monotone 1 to 127 (entry 63 and later = 127), so a harder hit than any recorded may reach 127; which curve was active during the captures is not known (bytes in [04-sysex.md](04-sysex.md)) | Measured | S03 | [9] [10] [11] [12] |
| Release | All | Note Off (`0x80`), velocity 64, same channel as the strike | 34 of 34 in the guided pad captures, 61 of 61 in the mock logs, 5 of 5 in the Windows Mono run, 1 of 1 in the Mac User-mode listen. Note On velocity 0 is not used for pads (it is used for touch release, sections 3.2 to 3.5) | Measured | S03, S09, S10, S11 | [13] [14] [15] [16] [17] [6] [7] [18] |
| Pressure | Mono (`1E 00`) | Channel pressure (`0xD0`), ch1; one stream shared by all pads; 0 to 127 | With two pads held and the right one pressed harder, only one stream arrived (94 values, 0-127). Slow 2-3 s press ramp on one pad reached 33; left-right roll gave 85 values (0-127); up-down slide gave 25 values (0-36). No pitch bend and no CC 74 in Mono | Measured | S03 | `R033:103, 264, 1673, 2194` |
| Crossing pads | Mono | Note On on the new pad while the first note stays on | In the up-down slide Note 63 stayed on throughout; Note 55 (on at +1.334 s, off at +1.787 s) and then Note 71 (on at +2.174 s, off at +2.380 s) came and went inside it, and Note 63's Note Off came last at +3.048 s. There is no continuous X or Y | Measured | S03 | `R033:1673` |
| Per-pad pressure | Poly (`1E 01`) | Polyphonic Key Pressure (`0xA0`) expected | Not tested: no `0xA0` message appears in any capture and no pad strike was recorded in Poly mode | Not tested | — | — |
| Poly mode listed | Poly (`1E 01`) | — | Ableton lists Poly Aftertouch as a pad mode | Official | — | [19] |
| Poly mode set from Live's UI | Poly (`1E 01`) | — | The notes report that Live switched the unit to it; the input was not tried (notes, macOS) | Reported | S03 | `R001:40` |
| Channel per strike | MPE (`1E 02`) | Channels 2-16; channel 1 unused | Slow separate strikes take ascending channels (2, 3, 4 ... 10 in the first file, up to 16 in later files). Two simultaneous strikes took two channels. The 16th simultaneous note was never tested; at most 4 (Mac mock) and 7 (Windows mock) channels were held at once (page tally) | Measured | S03, S09 | [20] [6] |
| Burst allocation | MPE | 18 Note Ons in 0.36 s used channels 7, 4, 5, 3, 15, 2, 10, 12, 6, 14, 11, 16, 8, 9, 13, then 4, 7, 3 | Each of ch2-16 was used once before any repeated. The notes repeat (87 on four channels, 37 and 79 on three), so the burst is not 18 distinct pads; what touched the pads is not recorded | Measured | S09 | `R056:11177-11304` |
| Pressure | MPE | Channel pressure (`0xD0` + channel), 0 to 127, per strike channel | Light tap peaked at 35, strong strike reached 127 | Measured | S03 | `R034:7, 323` |
| Y axis | MPE | CC 74 on the strike channel; 0 to 127 | Slide across rows: 141 values 0-127; light tap 50-55; hard tap 10-53; bottom-left corner pad 52-63; top-right corner pad 84-87 | Measured | S03 | [21] [10] |
| X axis | MPE | Pitch bend (`0xE0` + channel), not quantised (steps of 1 to a few counts) | Within one pad a left-right roll gave -77 to 0 (102 messages). Sliding across a row of 8 pads kept one note sounding, PB -85 to +1275 (292 messages; the notes equate about 171 counts with one pad, `R001:45`; see the note under this table); right to left gave -934 to +78 and re-triggered once (Note 61 on a new channel). Each simple strike sends one PB 0. Steps are a few counts (for example -71, -74, -77, -74, -71 and -85, -82, -79, -77), unlike the touch strip's 64 | Measured | S03 | [22] [23] [24] |
| Message order at note start | MPE | Channel pressure 0 and CC 74 first, then Note On, then the pressure stream | e.g. `D1 00`, `B1 4A 37`, `91 29 4A`, `D1 5B`, then pressure, CC 74 and pitch bend (Mac, Live backed off); in the mock the pressure after the Note On was 0 (`D3 00`, `B3 4A 7E`, `93 3D 1A`, `D3 00`) | Measured | S03, S09 | [25] [26] |
| Resting stream | MPE | CC 74 on the strike channel at about 74 per second | Note 87 on ch4 stayed on for 479 s, then got a Note Off (`83 57 40`) and a new Note On (`93 57 0D`, velocity 13) on the same note and channel 0.1 ms apart and stayed on another 127 s; over those 607 s ch4 carried 45,112 CC 74 values, 32 distinct, mostly 91-95; channel pressure only around note boundaries. Whether the pad was physically still is not recorded | Measured | S09 | [27] [28] |
| Glide across a row | MPE | Per-pad pitch bend switch `26 07 08` | With the switch on, a slide to the right neighbour kept Note 63 (PB 0-170); with it off the neighbour re-triggered (Notes 63 then 64, PB 0-159). Pitch bend was present in both: 166 and 132 messages for the roll. The `26 07 08` sends are not logged; the labels come from the file names and `R001:47` | Measured | S03 | [29] [30] [31] |
| Glide across rows | MPE | Glide groups `26 09` written by Live | Crossing to another row re-triggers: after 1.1 s on Pad A (velocity 42), Notes 71, 63 and 71 followed within 0.24 s at velocities 47, 83 and 126 | Measured | S03 | `R057:22363`; `R001:44-46` |
| Pad mode query | Mono, MPE | `1E m` set, `1F` read | `1E 00` then `1F` in the same millisecond returned 02 (MPE) in 1 of 4 Windows runs (all S11) and 00 in the other 3 (see the note under this table on the `1F` request) | Measured | S11 | [32] [33] |
| Pad mode after Live quits | MPE | — | Live leaves the pads in MPE after it quits and until power-off (notes, macOS) | Reported | S03 | `R001:100` |
| Default mode | After a clean power-up | Not tested | No measurement after a power cycle without Live exists | Not tested | — | — |
| Default mode (notes) | After a clean power-up | Mono | The notes call Mono the unset state | Reported | S03 | `R001:39` |
| Default mode (manual) | After a clean power-up | MPE | Ableton's manual lists MPE as the default | Official | — | `R018:44` |

Sources for this table:

1. `R033:7, 55`
2. `R034:7, 323`
3. `R067:7`
4. `R068:7`
5. `R057:7`
6. `R056:1-90353`
7. `R061:473-506`
8. `R028:4`
9. `R034:323`
10. `R057:22363`
11. `R001:53`
12. `R069:413-476`
13. `R033:7-3877`
14. `R034:7-19432`
15. `R067:7-15919`
16. `R068:7-15031`
17. `R057:7-25687`
18. `R028:27`
19. `R018:44, 49`
20. `R034:7-19201`
21. `R034:7, 323, 18963, 19201`
22. `R034:2902`
23. `R057:7, 11774`
24. `R001:45`
25. `R028:2-5`
26. `R056:11177-11182`
27. `R056:11180-56620`
28. `R056:47911-47914`
29. `R067:12547`
30. `R068:9123`
31. `R001:47`
32. `R060:20-22`
33. `R061:20-22`

**About 171 counts per pad (Inferred).** The figure is derived, not read from a per-pad measurement. The notes (`R001:45`) give it next to the 8-pad sweep of PB -85 to +1275 and equate it with one semitone of a bend range of 48 semitones either way (8192 / 48 = about 171).

**The `1F` request (Inferred).** The log records the `1E` write (`TX [...] pad mode 1E`) and the pad-mode report, but not the `1F` request. The script sends it immediately after the `1E` write (`R070:432`), so that it was sent, and when, is deduced from the code.

### 3.2 Encoders above the display (1-8)

Touch is Note On ch1 with velocity 127; release is Note On velocity 0. Turn is a relative Control Change on ch1.

| Encoder | Touch note | Turn CC | S01 recording: touches, turn CCs | User mode turn CCs, right / left: S09; S10; S11 | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|---|
| 1 | Note 0 | 71 | 2, 13 (value 1) | 412 / 308; 20 / 71; 1225 / 1384 | Measured | S01, S09, S10, S11 | [1] [2] [3] [4] [5] [6] [7] |
| 2 | Note 1 | 72 | 2, 24 (value 1) | 247 / 249; 1530 / 936; 1389 / 1348 | Measured | S01, S09, S10, S11 | [1] [2] [3] [4] [5] [6] [7] |
| 3 | Note 2 | 73 | 3, 30 (value 1) | 231 / 204; 3027 / 2291; 446 / 448 | Measured | S01, S09, S10, S11 | [1] [2] [3] [4] [5] [6] |
| 4 | Note 3 | 74 | 2, 38 (value 1) | 413 / 457; 168 / 203; 320 / 387 | Measured | S01, S09, S10, S11 | [1] [2] [3] [4] [5] [6] |
| 5 | Note 4 | 75 | 2, 29 (value 1) | 511 / 650; 970 / 445; 382 / 69 | Measured | S01, S09, S10, S11 | [1] [2] [3] [4] [5] [6] |
| 6 | Note 5 | 76 | 2, 31 (value 1) | none; 338 / 298; 508 / 442 | Measured | S01, S10, S11 | [1] [2] [4] [5] [6] |
| 7 | Note 6 | 77 | 2, 35 (31 x value 1, 4 x value 2) | 97 / 61; none; 239 / 135 | Measured | S01, S09, S11 | [1] [2] [3] [5] [6] |
| 8 | Note 7 | 78 | 2, 34 (32 x value 1, 2 x value 2) | 329 / 395; none; 49 / 17 | Measured | S01, S09, S11 | [1] [2] [3] [6] |

Sources for this table:

1. `R007:7`
2. `R005:170-438`
3. `R056:1-57189`
4. `R056:57190-90353`
5. `R060:1-10270`
6. `R062:1-8616`
7. `R063:1-3028`

No left turn was recorded in S01; left turns of all eight encoders were seen in User mode ("left" = value 64-127). Encoder 8 was also seen on both ports at Windows power-on (S02, Reported: prose only, `R001:127`). Counts are events in the logs, not gestures. Turn step sizes decoded as signed 7-bit values:

| Source | Events (CC 71-78) | Steps seen | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Mac mock | 4,564 | -4 x1, -3 x89, -2 x450, -1 x1784, +1 x1824, +2 x307, +3 x95, +4 x12, +5 x2 | Measured | S09 | `R056:1-57189` |
| Windows mock | 10,297 | -3 x15, -2 x238, -1 x3991, +1 x5611, +2 x411, +3 x26, +4 x5 | Measured | S10 | `R056:57190-90353` |
| Windows settings logs | 8,788 | -4 x1, -3 x73, -2 x431, -1 x3725, +1 x4015, +2 x434, +3 x97, +4 x12 | Measured | S11 | [1] [2] [3] [4] |

Sources for this table:

1. `R060:1-10270`
2. `R061:1-1337`
3. `R062:1-8616`
4. `R063:1-3028`

Fast turns reach +5 and -4 per message. The Mac and Windows mock tallies were recounted from the log for this page and match the earlier working summary.

### 3.3 Volume and Swing & Tempo encoders

| Control | Gesture | Message | Values and behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| Volume | Touch | Note On ch1, Note 8 | 127 on touch, Note On velocity 0 on release; no CC when only touched | Measured | S01 | `R007:11`; `R032:97` |
| Volume | Turn right / left | CC 79 ch1 | S01: right = 1 (x3), left = 127 (x3) with the touch note around them. User mode, Windows: 7 right and 7 left (S10), 3 right (S11), 65 left turns (S06; the log prints only values of 64 or more, so no right turns); only +-1 seen in S01, S10 and S11, S06 prints no values | Measured | S01, S06, S10, S11 | [1] [2] [3] [4] [5] |
| Volume | Push | CC 111 ch1 | 127 on press, 0 on release, with the touch note around it. User mode, Windows: 14 press pairs (S10), 4 presses (S06, DOWN only; releases not logged) | Measured | S01, S06, S10 | [6] [7] [3] [8] |
| Swing & Tempo | Touch | Note On ch1, Note 10 | 127 on touch, Note On velocity 0 on release | Measured | S01, S10, S11 | [9] [10] [3] [4] [11] |
| Swing & Tempo | Turn right / left | CC 14 ch1 | S01: right = 1 (x3), left = 127 (x4). User mode, Windows: 76 events in the mock (+1 x42, -1 x34), 65 in the settings logs (+1 x27, -1 x38), 11 left turns in S06 (the log prints only values of 64 or more); only +-1 seen in S01, S10 and S11 | Measured | S01, S06, S10, S11 | [12] [13] [3] [4] [11] [14] |
| Swing & Tempo | Push | CC 15 ch1 | 127 on press, 0 on release, with the touch note around it; never seen in User mode | Measured | S01 | `R007:25`; `R071:280` |
| Second small knob | Touch / turn | Note 9 (touch) expected | Not seen: the unit has one Swing & Tempo encoder, and no Note 9 appears in the 12 S01 guided-map files (434 events, counted for this page); Push 2 and DrivenByMoss assign Note 9 to a second small knob | Measured | S01 | `R007:22-25`; `R071:127-280` |

Sources for this table:

1. `R007:12-13`
2. `R032:127, 184`
3. `R056:57190-90353`
4. `R060:1-10270`
5. `R022:295-370`
6. `R007:14`
7. `R032:241`
8. `R022:352-373`
9. `R007:22`
10. `R071:127`
11. `R062:1-8616`
12. `R007:23-24`
13. `R071:157, 214`
14. `R022:282-293`

A working summary that fed this page attributed the CC 14 and CC 79 turns to both operating systems; the Mac mock log contains neither. Volume and Swing & Tempo turn values other than +-1 were never recorded (fast spins not tested). Whether the Volume knob changes the analogue level without Live was not tested. The S06 counts are from the decoded device-mock log (see the note under the table in section 1).

### 3.4 Jog wheel

| Gesture | Message | Values and behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Touch top | Note On ch1, Note 11 | 127 on touch, Note On velocity 0 on release. A bare touch sends nothing else (4 touches in S08) | Measured | S01, S08 | [1] [2] [3] |
| Turn right | CC 70 ch1 | Value 1 per click (S01: 4 clicks). In User mode only +1 seen: Mac 16 events (S09), Windows 197 in the settings logs (S11) and 1 in the Windows mock (S10) | Measured | S01, S09, S10, S11 | [4] [5] [6] [7] [8] [9] [10] |
| Turn left | CC 70 ch1 | Value 127 per click (S01: 4 clicks). In User mode only -1 seen: Mac 33 events (S09) and Windows 154 in the settings logs (S11) | Measured | S01, S09, S11 | [11] [12] [6] [8] [9] [10] |
| Press | CC 94 ch1 | 127 on press, 0 on release. S07: 52 presses, hold 59-167 ms (median 120). Strays: pressing produced a +-1 CC 70 tick in 4 of 52 gestures (5 ticks, 20-29 ms after press-down or 6-15 ms after release). S06: 2 presses logged (DOWN only) | Measured | S01, S06, S07 | [13] [14] [15] [16] |
| Tilt left | CC 93 ch1 | One 127 / 0 pair per tilt (hold 204 ms in S09). S06: 2 tilts logged (DOWN only) | Measured | S01, S06, S09 | [17] [18] [19] [20] |
| Tilt right | CC 95 ch1 | One 127 / 0 pair per tilt (hold 218 ms in S09). No auto-repeat when held 3.2 s. A rapid double tilt gave two clean pairs 303 ms apart inside one touch. A turn followed by a tilt 885 ms later showed no cross-talk. Auto-repeat and cross-talk were tried on the right tilt only. S06: 1 tilt logged (DOWN only) | Measured | S01, S06, S09 | [21] [22] [23] [24] |
| Tilt up / down | Not tested | No vertical tilt was looked for in any session | Not tested | — | — |
| Tilt up / down (manual) | — | The manual and the notes describe left and right tilts only | Official | — | `R065:32` |

Sources for this table:

1. `R007:59`
2. `R072:7`
3. `R011:78`
4. `R007:60`
5. `R072:38`
6. `R056:1-57189`
7. `R056:57190-90353`
8. `R060:1-10270`
9. `R061:1-1337`
10. `R062:1-8616`
11. `R007:61`
12. `R072:105`
13. `R007:62`
14. `R072:172`
15. `R026:39-49`
16. `R022:110, 115`
17. `R007:63`
18. `R072:221`
19. `R059:7`
20. `R022:130, 138`
21. `R007:64`
22. `R072:270`
23. `R059:7-10`
24. `R022:134`

In S09 (User mode) nothing from these gestures appeared on the Live or External Port. In S07 tilt CC 93 and CC 95 did not occur in the whole 52-press capture. The S06 counts are from the decoded device-mock log (see the note under the table in section 1). S01 also holds a first 90 s Live Port recording, made before the encoder recording (`R005:134-169`): 10 jog touches, 3 tilts left, 2 tilts right, 2 presses and 1 right turn, every CC inside a touch, with the same numbers as the guided map.

### 3.5 Touch strip

| Gesture | Message | Values and behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Touch | Note On ch1, Note 12 | 127 on touch, Note On velocity 0 on release | Measured | S01, S03, S09, S10, S11 | [1] [2] [3] [4] [5] [6] |
| Position | Pitch Bend ch1 (`0xE0`) | 14-bit; the least significant byte is only 0x00 or 0x40, so positions come in steps of 64 counts (256 positions). S01 sweep bottom to top: 223 messages, 222 distinct values, -8192 to +8128. Mac User-mode listen: 153 messages, -6592 to +3200. Windows mock: 1,552 messages, -8192 to +7872. Mac mock: 12 messages, 0 to 3776. Windows settings logs: 8 messages, -6912 to +8128 | Measured | S01, S03, S09, S10, S11 | [7] [8] [9] [10] [11] [12] [13] |
| Release | Pitch Bend ch1, value 0 | PB 0 in the same millisecond as the touch-off note; a bare touch sent PB 0 twice (touch-on and touch-off). No PB message arrived without an active touch (0 of 1,552 Windows, 0 of 12 Mac in the mock) | Measured | S01, S09, S10 | [14] [2] [4] |
| CC output mode | `17` flags | Not tested on this unit: a different strip mode that sends CCs instead of pitch bend was never tried | Not tested | — | — |
| CC output mode (DrivenByMoss) | `17` flags | DrivenByMoss selects pitch bend, volume, pan or discrete with `17` | Third-party | — | `R064:173` |

Sources for this table:

1. `R007:35`
2. `R073:7`
3. `R028:83, 237`
4. `R056:1-90353`
5. `R061:1-1337`
6. `R062:1-8616`
7. `R007:36`
8. `R073:56`
9. `R028:84-236`
10. `R056:57190-90353`
11. `R056:8530-8540, 9436-9442`
12. `R061:1059-1066`
13. `R062:1232-1236`
14. `R007:35-36`

The notes describe the strip as a spring-return pitch bend in the default mode (Reported, `R001:34`) and give the strip configuration read back on 2026-10-02 (S03) as 0x68 (Reported, `R001:69`; no raw record of the readback is cited here), and whether Live had run since power-on at that moment is not recorded; the bytes of the mode setting are in [04-sysex.md](04-sysex.md). DrivenByMoss selects pitch bend, volume, pan or discrete with `17` (Third-party, `R064:173`).

### 3.6 D-pad

| Control | Message | Values and behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Up | CC 46 ch1 | Press 127 / release 0. User mode, Windows: 2 presses | Measured | S01, S11 | [1] [2] [3] |
| Left | CC 44 ch1 | Press 127 / release 0. User mode, Windows: 4 presses | Measured | S01, S11 | [4] [5] [6] |
| Right | CC 45 ch1 | Press 127 / release 0. User mode, Windows: 1 press | Measured | S01, S11 | [7] [8] [9] |
| Down | CC 47 ch1 | Press 127 / release 0. User mode, Windows: 10 presses | Measured | S01, S11 | [10] [11] [12] |
| Centre press | CC 91 ch1 | Press 127 / release 0. User mode, Windows: 5 presses | Measured | S01, S11 | [13] [14] [15] |
| Centre touch sense | Note On ch1, Note 13 | 127 on touch, Note On velocity 0 on release. Order against CC 91 differs between S01 and S11 (section 4). 3 of 8 touches in S11 carried no CC; touches lasted 268 to 2,291 ms (median 501 ms) | Measured | S01, S11 | [13] [14] [16] |

Sources for this table:

1. `R007:74`
2. `R074:7`
3. `R060:997`
4. `R007:75`
5. `R074:38`
6. `R060:1025`
7. `R007:77`
8. `R074:118`
9. `R060:2346`
10. `R007:78`
11. `R074:149`
12. `R060:991`
13. `R007:76`
14. `R074:69`
15. `R060:1043`
16. `R060:1029-9535`

The D-pad works in User mode (Windows, S11). An earlier working assumption held that it did not; the arrows and the centre all arrived on the User Port. The identity of Note 13 is not confirmed by any source other than these measurements (`R001:32, 118`).

### 3.7 Buttons

All 70 plain buttons send Control Change on ch1, value 127 on press and 0 on release, with no velocity or pressure; the four D-pad arrows are in section 3.6, the other 66 follow. The press value was 127 in every S01 row and only 127 and 0 appeared for any button CC in the mock logs. Each S01 row is a single press (two messages on the Live Port).

| Button | Panel group | CC (ch1) | Press / release | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| Sets | Top-left | 80 | 127 / 0 | Measured | S01 | `R007:8`; `R032:7` |
| Setup | Top-left | 30 | 127 / 0 | Measured | S01 | `R007:9`; `R032:37` |
| Learn | Top-left | 81 | 127 / 0 | Measured | S01 | `R007:10`; `R032:67` |
| Undo | Top-left | 119 | 127 / 0 | Measured | S01 | `R007:15`; `R032:289` |
| Save | Top-left | 82 | 127 / 0 | Measured | S01 | `R007:16`; `R032:319` |
| User | Top-left | 59 | 127 / 0, also copied to the User Port | Measured | S01 | `R007:17`; `R032:349` |
| Lock | Left-middle | 83 | 127 / 0 | Measured | S01 | `R007:18`; `R071:7` |
| Stop Clip | Left-middle | 29 | 127 / 0 | Measured | S01 | `R007:19`; `R071:37` |
| M (Mute) | Left-middle | 60 | 127 / 0 | Measured | S01 | `R007:20`; `R071:67` |
| S (Solo) | Left-middle | 61 | 127 / 0 | Measured | S01 | `R007:21`; `R071:97` |
| Tap Tempo | Left column | 3 | 127 / 0 | Measured | S01 | `R007:26`; `R075:7` |
| Metronome | Left column | 9 | 127 / 0 | Measured | S01 | `R007:27`; `R075:38` |
| Quantize | Left column | 116 | 127 / 0 | Measured | S01 | `R007:28`; `R075:69` |
| Fixed Length | Left column | 90 | 127 / 0 | Measured | S01 | `R007:29`; `R075:100` |
| Automate | Left column | 89 | 127 / 0 | Measured | S01 | `R007:30`; `R075:131` |
| New | Left column | 92 | 127 / 0 | Measured | S01 | `R007:31`; `R075:162` |
| Capture | Left column | 65 | 127 / 0 | Measured | S01 | `R007:32`; `R075:193` |
| Record | Left column | 86 | 127 / 0 | Measured | S01 | `R007:33`; `R075:224` |
| Play | Left column | 85 | 127 / 0 | Measured | S01 | `R007:34`; `R075:255` |
| Upper display button 1 | Above display | 102 | 127 / 0 | Measured | S01 | `R007:37`; `R076:7` |
| Upper display button 2 | Above display | 103 | 127 / 0 | Measured | S01 | `R007:38`; `R076:38` |
| Upper display button 3 | Above display | 104 | 127 / 0 | Measured | S01 | `R007:39`; `R076:69` |
| Upper display button 4 | Above display | 105 | 127 / 0 | Measured | S01 | `R007:40`; `R076:100` |
| Upper display button 5 | Above display | 106 | 127 / 0 | Measured | S01 | `R007:41`; `R076:131` |
| Upper display button 6 | Above display | 107 | 127 / 0 | Measured | S01 | `R007:42`; `R076:162` |
| Upper display button 7 | Above display | 108 | 127 / 0 | Measured | S01 | `R007:43`; `R076:193` |
| Upper display button 8 | Above display | 109 | 127 / 0 | Measured | S01 | `R007:44`; `R076:224` |
| Lower display button 1 | Below display | 20 | 127 / 0 | Measured | S01 | `R007:45`; `R077:7` |
| Lower display button 2 | Below display | 21 | 127 / 0 | Measured | S01 | `R007:46`; `R077:38` |
| Lower display button 3 | Below display | 22 | 127 / 0 | Measured | S01 | `R007:47`; `R077:69` |
| Lower display button 4 | Below display | 23 | 127 / 0 | Measured | S01 | `R007:48`; `R077:100` |
| Lower display button 5 | Below display | 24 | 127 / 0 | Measured | S01 | `R007:49`; `R077:131` |
| Lower display button 6 | Below display | 25 | 127 / 0 | Measured | S01 | `R007:50`; `R077:162` |
| Lower display button 7 | Below display | 26 | 127 / 0 | Measured | S01 | `R007:51`; `R077:193` |
| Lower display button 8 | Below display | 27 | 127 / 0 | Measured | S01 | `R007:52`; `R077:224` |
| Device | Top-right | 110 | 127 / 0 | Measured | S01 | `R007:53`; `R078:7` |
| Mix | Top-right | 112 | 127 / 0 | Measured | S01 | `R007:54`; `R078:38` |
| Clip | Top-right | 113 | 127 / 0 | Measured | S01 | `R007:55`; `R078:69` |
| Session (Session Screen Mode) | Top-right | 34 | 127 / 0 | Measured | S01 | `R007:56`; `R078:100` |
| Add | Top-right | 32 | 127 / 0 | Measured | S01 | `R007:57`; `R078:131` |
| Swap | Top-right | 33 | 127 / 0 | Measured | S01 | `R007:58`; `R078:162` |
| Master Track (Main Track) | Master / scene column | 28 | 127 / 0 | Measured | S01 | `R007:65`; `R079:7` |
| Scene 1/32t | Master / scene column | 43 | 127 / 0 | Measured | S01 | `R007:66`; `R079:38` |
| Scene 1/32 | Master / scene column | 42 | 127 / 0 | Measured | S01 | `R007:67`; `R079:69` |
| Scene 1/16t | Master / scene column | 41 | 127 / 0 | Measured | S01 | `R007:68`; `R079:100` |
| Scene 1/16 | Master / scene column | 40 | 127 / 0 | Measured | S01 | `R007:69`; `R079:131` |
| Scene 1/8t | Master / scene column | 39 | 127 / 0 | Measured | S01 | `R007:70`; `R079:162` |
| Scene 1/8 | Master / scene column | 38 | 127 / 0 | Measured | S01 | `R007:71`; `R079:193` |
| Scene 1/4t | Master / scene column | 37 | 127 / 0 | Measured | S01 | `R007:72`; `R079:224` |
| Scene 1/4 | Master / scene column | 36 | 127 / 0 | Measured | S01 | `R007:73`; `R079:255` |
| Note | Navigation | 50 | 127 / 0 | Measured | S01 | `R007:79`; `R074:180` |
| Session (Session Pad Mode) | Navigation | 51 | 127 / 0 | Measured | S01 | `R007:80`; `R074:211` |
| Scale | Navigation | 58 | 127 / 0 | Measured | S01 | `R007:81`; `R074:242` |
| Layout | Navigation | 31 | 127 / 0 | Measured | S01 | `R007:82`; `R074:273` |
| Repeat | Edit | 56 | 127 / 0 | Measured | S01 | `R007:83`; `R080:7` |
| Accent | Edit | 57 | 127 / 0 | Measured | S01 | `R007:84`; `R080:38` |
| Double Loop | Edit | 117 | 127 / 0 | Measured | S01 | `R007:85`; `R080:69` |
| Duplicate | Edit | 88 | 127 / 0 | Measured | S01 | `R007:86`; `R080:100` |
| Convert | Edit | 35 | 127 / 0 | Measured | S01 | `R007:87`; `R080:131` |
| Delete | Edit | 118 | 127 / 0 | Measured | S01 | `R007:88`; `R080:162` |
| Octave Up | Octave / Page / Shift | 55 | 127 / 0 | Measured | S01 | `R007:89`; `R081:7` |
| Page Left | Octave / Page / Shift | 62 | 127 / 0 | Measured | S01 | `R007:90`; `R081:38` |
| Page Right | Octave / Page / Shift | 63 | 127 / 0 | Measured | S01 | `R007:91`; `R081:69` |
| Octave Down | Octave / Page / Shift | 54 | 127 / 0 | Measured | S01 | `R007:92`; `R081:100` |
| Shift | Octave / Page / Shift | 49 | 127 / 0 | Measured | S01 | `R007:93`; `R081:131` |
| Select | Octave / Page / Shift | 48 | 127 / 0 | Measured | S01 | `R007:94`; `R081:162` |

Scene column: the nine buttons right of the pads are Master Track (labelled "Main Track" in the manual's chapter 17) and eight note-length buttons. The two buttons named Session are different: CC 34 (top-right group) and CC 51 (navigation group).

#### Plain buttons seen again in User mode (32 of 70)

These thirty-two, including the four D-pad arrows and Play and Learn, were recorded in User mode with the same CC numbers. The row for each is Control Change ch1. Values are 127 / 0 except for the six buttons seen only in the S06 device-mock log, which prints a control name and "DOWN" (value 64 or more) or "up" (below 64), decoded by the host script's CC table (`R058:41-53`); the raw bytes were not kept. That log also shows Tap Tempo, Lower display buttons 1-3 and Play in User mode on Windows.

| Button | CC | User-mode record | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Tap Tempo | 3 | Mac mock 17; Windows mock 27 (presses), 127 / 0 | Measured | S09, S10 | [1] [2] |
| Setup | 30 | Windows settings logs 9 (presses), 127 / 0 | Measured | S11 | [3] |
| Shift | 49 | Mac mock 6 (presses), 127 / 0 | Measured | S09 | `R056:3695` |
| Lower display button 1 | 20 | Mac mock 1; Windows settings logs 8 (presses), 127 / 0 | Measured | S09, S11 | [4] [5] |
| Lower display button 2 | 21 | Mac mock 10; Windows mock 1; Windows settings logs 7 (presses), 127 / 0 | Measured | S09, S10, S11 | [6] [7] [8] |
| Lower display button 3 | 22 | Mac mock 6; Windows mock 3; Windows settings logs 7 (presses), 127 / 0 | Measured | S09, S10, S11 | [9] [10] [11] |
| Lower display button 4 | 23 | Mac mock 1; Windows mock 16; Windows settings logs 8 (presses), 127 / 0 | Measured | S09, S10, S11 | [12] [13] [14] |
| Lower display button 5 | 24 | Mac mock 1; Windows mock 5; Windows settings logs 4 (presses), 127 / 0 | Measured | S09, S10, S11 | [15] [16] [17] |
| Lower display button 6 | 25 | Mac mock 1; Windows settings logs 3 (presses), 127 / 0 | Measured | S09, S11 | [18] [19] |
| Lower display button 7 | 26 | Mac mock 6; Windows settings logs 5 (presses), 127 / 0 | Measured | S09, S11 | [20] [21] |
| Lower display button 8 | 27 | Mac mock 7 (presses), 127 / 0 | Measured | S09 | `R056:1301` |
| D-pad left | 44 | Windows settings logs 4 (presses), 127 / 0 | Measured | S11 | [22] |
| D-pad right | 45 | Windows settings logs 1 (presses), 127 / 0 | Measured | S11 | [23] |
| D-pad up | 46 | Windows settings logs 2 (presses), 127 / 0 | Measured | S11 | [24] |
| D-pad down | 47 | Windows settings logs 10 (presses), 127 / 0 | Measured | S11 | [25] |
| Upper display button 1 | 102 | Windows settings logs 9 (presses), 127 / 0 | Measured | S11 | [26] |
| Upper display button 2 | 103 | Mac mock 4; Windows mock 39; Windows settings logs 18 (presses), 127 / 0 | Measured | S09, S10, S11 | [27] [28] [29] |
| Upper display button 3 | 104 | Mac mock 2; Windows mock 75; Windows settings logs 49 (presses), 127 / 0 | Measured | S09, S10, S11 | [30] [31] [32] |
| Upper display button 4 | 105 | Mac mock 8 (presses), 127 / 0 | Measured | S09 | `R056:9424` |
| Upper display button 5 | 106 | Mac mock 2 (presses), 127 / 0 | Measured | S09 | `R056:9412` |
| Upper display button 6 | 107 | Mac mock 2 (presses), 127 / 0 | Measured | S09 | `R056:9407` |
| Upper display button 7 | 108 | Mac mock 6; Windows settings logs 7 (presses), 127 / 0 | Measured | S09, S11 | [33] [34] |
| Upper display button 8 | 109 | Mac mock 3; Windows settings logs 16 (presses), 127 / 0 | Measured | S09, S11 | [35] [36] |
| Device | 110 | Windows device-mock run 6 presses (DOWN / up) | Measured | S06 | [37] |
| Mix | 112 | Windows device-mock run 1 press (DOWN / up) | Measured | S06 | [38] |
| Clip | 113 | Windows device-mock run 3 presses (DOWN / up) | Measured | S06 | [39] |
| Session (Session Screen Mode) | 34 | Windows device-mock run 4 presses (DOWN / up) | Measured | S06 | [40] |
| Page Left | 62 | Windows device-mock run 3 presses (DOWN only; releases not logged) | Measured | S06 | [41] |
| Page Right | 63 | Windows device-mock run 3 presses (DOWN only; releases not logged) | Measured | S06 | [42] |
| Learn | 81 | 5 / 5 presses on Windows (hold 98-130 ms), 5 / 5 on Mac (hold 128-167 ms), 2 presses in S11, 4 in S08 (2 in the walk, steps 6 and 7; 2 in the supplementary run); User Port only and no mode report where all ports were read (S07, S08, S09); the S11 mock read the User Port only | Measured | S07, S08, S09, S11 | [43] [44] [45] [46] [47] [48] |
| Play | 85 | Follows the MIDI mode (section 2); Mac User-mode listen: one press pair on the User Port | Measured | S03 | [49] [50] |
| User | 59 | 127 / 0 in User mode without Live: 3 presses on Windows delivered 2 messages each on the User Port and no mode report (S06; the spike read the User Port only); one press 127 then 0 on the Live Port and the User Port in the same millisecond (S08) | Measured | S06, S08 | [51] [52] |

Sources for this table:

1. `R056:5644`
2. `R056:73094`
3. `R060:198`
4. `R056:10327`
5. `R060:4874`
6. `R056:1366`
7. `R056:59152`
8. `R060:2293`
9. `R056:1451`
10. `R056:59160`
11. `R060:2356`
12. `R056:10348`
13. `R056:61784`
14. `R060:2862`
15. `R056:10351`
16. `R056:61782`
17. `R060:3346`
18. `R056:10360`
19. `R060:4856`
20. `R056:1288`
21. `R060:4864`
22. `R060:1025`
23. `R060:2346`
24. `R060:997`
25. `R060:991`
26. `R060:1048`
27. `R056:9447`
28. `R056:59030`
29. `R060:217`
30. `R056:9431`
31. `R056:59164`
32. `R062:85`
33. `R056:1242`
34. `R061:919`
35. `R056:9122`
36. `R060:7285`
37. `R022:142-165`
38. `R022:167-169`
39. `R022:171-179, 204-206`
40. `R022:183-202`
41. `R022:210, 216, 218`
42. `R022:208, 212, 214`
43. `R026:70-72`
44. `R023:40-42`
45. `R011:83-84`
46. `R062:1763`
47. `R012:34-37`
48. `R082:30-33`
49. `R008:2-25`
50. `R028:28-29`
51. `R042:1955, 1958, 1961`
52. `R011:85`

The 38 plain buttons not seen in User mode are CC 80, 119, 82, 83, 29, 60, 61, 9, 116, 90, 89, 92, 65, 86, 32, 33, 28, 36 to 43, 50, 51, 58, 31, 56, 57, 117, 88, 35, 118, 54, 55 and 48. The User button (CC 59) is a plain button; its behaviour with and without Live is described next.

#### User button

| Case | Message | Behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Live not running (mode not queried in S01; User mode in S06, S08) | CC 59 ch1 | 127 then 0; on the Live Port and the User Port in the same millisecond where both were read (S01, S08; S06 read the User Port only); no `0A` report follows; a press is an ordinary control | Measured | S01, S06, S08 | [1] [2] [3] |
| Live running (Mac) | CC 59 ch1 | Press 1: Push sends 127 on both ports at 15:46:09.383, Live sends `0A 01` to the Live Port 152 ms later and only then does the Push report the mode. Press 2: Live sends `0A 00` 44 ms after the press. The firmware does not toggle the mode itself | Measured | S03 | `R010:17-18, 150-152, 170, 639-640` |
| Live running (Windows) | CC 59 ch1 | On the Live Port and the User Port 0.05 to 0.14 ms apart in Live and User modes. Hold 111 to 203 ms, median 154 ms (24 presses, tally recounted for this page); Live replies 567-938 ms after the press while Unreal Editor holds the display (4 of 8 presses flapped) and 133-200 ms when Live holds it (0 of 6) (S04; recounted from the raw log, as in 01-connection.md and 07-timing.md). With the spike bound and holding the display, 2 of 5 returns to User flapped (S06) | Measured | S04, S06 | [4] [5] [6] |

Sources for this table:

1. `R032:349`
2. `R042:1955, 1958, 1961`
3. `R011:85`
4. `R014:4-1691`
5. `R001:196-204, 229-244`
6. `R042:2108-2132`

Mode-switching mechanics and the `0A` reports are covered in [01-connection.md](01-connection.md).

### 3.8 Pedals and External Port

| Control | Message | Behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Pedal 1 jack | Not tested | No pedal was connected in any session | Not tested | — | — |
| Pedal 2 jack | Not tested | As above | Not tested | — | — |
| Pedal jacks (manual) | — | Ableton's manual: each jack is a footswitch or a CV output; Pedal 1 tap = record, double tap = New; Pedal 2 = sustain (functions inside Live, not MIDI numbers) | Official | — | [1] |
| Pedal footswitch numbers (DrivenByMoss) | — | DrivenByMoss expects footswitch 1 = CC 64 and footswitch 2 = CC 69 | Third-party | — | `R064:174` |
| Pedal configuration | Not tested as input | No pedal input was recorded with any setting | Not tested | — | — |
| Pedal configuration writes | `37 26 <v>` from Live | Live writes `37 26 <v>` (0x50 default, 0x43 after changing Pedal 1, 0x1C after Pedal 2); bits not decoded (bytes in [04-sysex.md](04-sysex.md)) | Measured | S03 | `R001:79`; `R083:1-4` |
| External Port (USB-A, DIN) | No message seen | No message ever arrived on the External Port in any capture where it was open (S03 coexistence and User-mode listen, the S04 Python logger, S07, S09) | Measured | S03, S04, S07, S09 | [2] [3] |
| External Port (USB-A, DIN), device attached | Not tested | No DIN or USB-A device was attached in any session | Not tested | — | — |

Sources for this table:

1. `R018:127-130`
2. `R014:1-3`
3. `R023:15-16, 40`

Ableton's MIDI FAQ, cited second-hand, lists three MIDI ports: Live Port, User Port and External Port (`R084:21`, Official).

### 3.9 Other messages the Push sends

| Message | Value | Behaviour | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Active Sensing | `FE` | Every 270 ms on the Live Port and the User Port. User mode without Live: Windows (S07) and macOS, where a 513 s capture had 1,900 per port and none on the External Port (S09). With Live running (Live and User modes, macOS), MIDI Monitor shows the same 270 ms on both ports (S03; the External Port is not in those captures). | Measured | S03, S07, S09 | [1] [2] [3] [4] [5] |
| Active Sensing in Dual mode | `FE` | Not tested: the Python scripts of S01 and S03 discarded `FE` | Not tested | — | — |
| Mode report | `F0 00 21 1D 01 01 0A m F7` | Answer to a host `0A m`, on both the Live Port and the User Port within 0.3 ms, also when the mode is unchanged. Bytes and timing in [04-sysex.md](04-sysex.md) | Measured | S07, S09 | [6] [7] |
| Unidentified CC 114 | `B0 72 01` / `B0 72 00` | Five messages in the Mac mock log: 1, 0, 1 at 17:21:44.5 and 0, 1 at 17:38:36.5 and 17:38:46.4; values 1 and 0, not 127 and 0; not in the 87-control map; no zone of the mock bound it. Which control, if any, sends it is not known | Measured | S09 | `R056:8965-8967, 9860, 9863` |

Sources for this table:

1. `R026:27`
2. `R023:15-16`
3. `R024:24`
4. `R085:1-994`
5. `R010:1-2`
6. `R026:24-25`
7. `R023:31-32, 63-64`

## 4. Measured ordering facts

Touch notes and the CCs of the same control do not arrive in one fixed order. The notes' statement that a touch note always precedes a turn or press (`R001:33`) holds for single guided gestures and for turns in practice, and fails for direct presses and for the head and tail of turns.

| Gesture | Result | Count | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Guided single gestures with a touch note (turn, push, tilt of Volume, Swing & Tempo, jog) | Touch Note On came first, touch-off after the last CC | 11 of 11. Lead to first CC: turns 299-530 ms (6 gestures); push or tilt 8-205 ms (5 gestures) | Measured | S01 | [1] [2] [3] [4] |
| Encoders 1-8 recording | Turn CC arrived with that encoder's touch active | 232 of 234 turn CCs; the two exceptions came 0.55 s and 0.58 s after that encoder's touch-off (CC 74 and CC 77) | Measured | S01 | `R005:170-438` (exceptions at lines 254 and 364) |
| Mac mock, turns (CC 70-79, 14) | Turn CC with that control's touch active | 4,544 of 4,613; of the 69 without, 54 came within 300 ms after a touch-off and 15 had the touch-on arrive within 300 ms later | Measured | S09 | `R056:1-57189` |
| Windows mock, turns | Same | 10,332 of 10,388; of the 56 without, 16 trailing and 40 leading | Measured | S10 | `R056:57190-90353` |
| Windows settings logs, turns | Same | 9,116 of 9,207 (91 without; page tally) | Measured | S11 | [5] [6] [7] [8] |
| Mac mock, pushes and tilts (127 values of CC 93, 94, 95, 111, 15) | Touch already on at the press | 38 of 76 (tilt left 18 of 33, press 8 of 23, tilt right 12 of 20) | Measured | S09 | `R056:1-57189` |
| Windows mock, pushes and tilts | Same | 10 of 18 (Volume push 10 of 14, jog tilts 0 of 4) | Measured | S10 | `R056:57190-90353` |
| Windows settings logs, pushes and tilts | Same | 47 of 63 (page tally) | Measured | S11 | [5] [6] [7] |
| Direct jog presses (CC 94, Windows, User mode) | Press arrived before its own touch-on | 46 of 52 (1-89 ms, median 50 ms; in 2 the touch-on came only after the release). Touch-on first in 6 of 52 (6-119 ms before the press) | Measured | S07 | `R026:39-49` |
| Jog touch release around a press | Touch can drop at the press, or hold past it | Turn, touch-off, press within 4 ms (S07 press 1); touch-off 445 ms after the press with the finger left on (S08 step 11) | Measured | S07, S08 | [9] [10] |
| Jog tilts (Mac, User mode) | Tilt inside an active touch | 6 of 6 tilts; touch-on led the first CC of five gestures by 118-746 ms | Measured | S09 | `R086:1-23` |
| Jog presses in the UE walk (Windows) | Touch-on before the press | 640 ms (step 3) and 284 ms (step 4); in step 5 the touch-on came 22 ms after the press | Measured | S08 | `R011:80-82` |
| D-pad centre, S01 (Mac, MIDI mode not queried) | CC 91 first, Note 13 on 59 ms later, CC 91 off at +138 ms, Note 13 off at +326 ms | 1 gesture | Measured | S01 | `R007:76`; `R074:69` |
| D-pad centre, User mode (Windows) | Note 13 on before CC 91 | 5 of 5 (leads 15, 43, 230, 1272, 1695 ms). 3 of 8 Note 13 touches carried no CC; one up-arrow press fell inside a touch; 16 arrow presses had no Note 13 | Measured | S11 | [11] |
| Touch strip | PB 0 and touch-on in the same millisecond; PB 0 and touch-off in the same millisecond | 1 bare touch | Measured | S01 | `R073:7` |

Sources for this table:

1. `R007:12-14, 23-25, 60-64`
2. `R032:97-241`
3. `R071:127-280`
4. `R072:7-270`
5. `R060:1-10270`
6. `R061:1-1337`
7. `R062:1-8616`
8. `R063:1-3028`
9. `R026:63-64`
10. `R011:90`
11. `R060:991-9540`

Two other timing facts belong to [07-timing.md](07-timing.md) and are not repeated here.

## Not yet verified

- Poly aftertouch (`1E 01`) input was never recorded.
- The full 87-gesture map was never repeated in User mode or on Windows; 38 of 70 plain buttons were seen only in S01, and six more only in a decoded log without values (S06).
- Pedal input and External Port input (DIN, USB-A) were never exercised.
- A pad strike at velocity 127 was never seen; the factory sensitivity state was never read before Live had run.
- The port routing of S01 (only the User button also on the User Port) and of the S03 pad captures the next morning (pads on both ports), and the MIDI mode of either, are unexplained; pads in Live mode (`0A 00`) were never compared with other controls.
- The power-on MIDI mode (Dual) and the Windows power-on routing are Reported only, with no raw log; the pad mode after a clean power-up is not known (Mono or MPE).
- Pad mode query race: `1E 00` followed at once by `1F` returned MPE in one of four Windows runs (S11).
- Whether the Mono "crossing pads" sequence (Note 63 held while Notes 55 and 71 come and go) is a firmware rule or one finger covering two pads is not recorded.
- The Note Off and new Note On on Note 87 after 479 s in the MPE resting stream (S09) are not explained; whether the pad was physically touched is not recorded.
- Fast-spin step sizes of the jog wheel, Volume and Swing & Tempo encoders (only +-1 seen).
- CC 114 (values 1 and 0) is unidentified; vertical jog tilts and any other unmapped gesture were not looked for.
- Shift and Select modifier effects on the wire in Live mode; touch strip CC modes; 16 simultaneous MPE notes.
- Note 13 as the D-pad touch sense is confirmed by no source other than the measurements; its order against CC 91 differs between S01 (1 gesture) and S11 (5 of 5). Active Sensing in Dual mode is Not tested (the S01 and S03 Python scripts discarded it), and the External Port was checked for it only in User mode (S09).
- Whether the Volume knob changes the analogue level without Live.
- Mac and Windows parity of the input messages was compared only for Learn and the User button.

Device tests that would close each item are in [08-gaps.md](08-gaps.md#controls).

## Conflicting records

| Topic | Records | Resolution |
|---|---|---|
| Which controls are mirrored on the User Port | S01 (`R087` control blocks): only the User button also on the User Port, all other controls Live Port only, with both ports open and the mode never queried. S03 block a, the next morning (`R088`, mode not queried): pads on both ports. `R001:13` reports both as one Live-not-running observation. `R001:96-97, 127` and `R008:12-15`: in Dual mode every control (Play shown) appears on both ports, and power-up is Dual. `R031`: pads mirrored on a day when Live had run and quit | Unresolved. The S01 and S03 raw captures stand as measured; they are separate sessions about 7.5 hours apart, and what happened to the unit in between is not recorded. Leading candidate reading (Inferred): S01 ran in Live mode, where the notes say the non-SysEx output goes to the Live Port only and the User button goes to both ports in every mode (`R001:96`; the S04 capture `R014` shows the User button on both ports with Live running), and the unit was in Dual mode by S03 block a (`R088`, 07:20-07:29 on 2026-10-02) after a power cycle that was not logged. The same reading needs a further unlogged power cycle or mode change before `R031`, taken after Live had run and quit. Neither the mode in S01 nor a power cycle is logged, and only Play was tried in Live mode (`0A 00`), so this is a candidate reading, not a finding |
| D-pad centre order | S01 (`R007:76`): CC 91 first, Note 13 +59 ms. S11 (`R060`): Note 13 first in 5 of 5 | Both stand: one sample on macOS with the MIDI mode not queried against five on Windows in User mode (both in the Control Mode hardware mode, like every session). Note 13 behaves as a touch sense (it appears without CC 91); the OS or mode as the cause is not tested |
| Touch note always first | `R001:33` against the counts in section 4 | The measured counts win: false for direct jog presses and for the edges of turns |
| Default pad mode | `R001:39`: Mono is the unset state. Ableton's manual (Official): MPE is the default. `R060:20-22`: MPE reported at once after `1E 00`; `R001:100`: Live leaves MPE until power-off | Unresolved: the state depends on earlier Live use and no clean power-up was measured |
| Jog wheel touch | DrivenByMoss: none registered; push3-protocol-docs: CC 11 (`R064:214`); ofou: Note 11 (`R089:62`) | Measured: Note 11 (S01). push3-protocol-docs labels the number as a CC; the community survey reads that as a column-label slip, since the same table also lists the strip touch as "CC 12" (`R089:62`) |
| Jog turn, Swing & Tempo, Volume | ofou (`R089:35, 38, 53`): jog on CC 88 (values 1 and 65), Swing and Tempo CC 13, Volume CC 16, CC 14 and 15 clickable. Push 2 map: Swing CC 15 rotate with Note 9 touch (`R064:172, 183-184`) | Measured values follow DrivenByMoss: jog turn CC 70; Volume turn CC 79, push CC 111; one Swing & Tempo encoder with touch Note 10, turn CC 14, push CC 15; Note 9 never seen |
| Volume knob without Live | Forum (`R084:32-33`): completely disabled without Live. Measured: touch, turn and push all emitted without Live | Different claims: MIDI emission without Live is measured in S01 (S10 and S11 recorded it in User mode with the Live state not logged); the analogue level was not tested |
| User mode without Live | Forum (`R084:30-31`): does nothing, pads dark. Measured: a host `0A 01` works without Live and all input arrives on the User Port (S07, S09) | The measurement wins for host-set User mode; the forum reports may concern the physical User button, which needs Live (not tested) |
| User button | `R065:30`, `R089:71`, `R064:217`: unknown. `R001:172` calls the mode switch not yet separated (the same section settles it at `R001:174-177`) | Resolved by measurement: the button sends CC 59 on both ports where both were read (S01, S03, S04, S08; S06 read the User Port only) and the mode follows Live (S03, S06, S08); the older lines are stale |
| D-pad not usable in User mode | An earlier working assumption; `R090:70-71` says the D-pad CC numbers were not yet verified on this unit | False: numbers verified in S01 and S11 and the D-pad works in User mode |
| Velocity-0 Note On over Windows MIDI Services | `R001:221`: a Note On with velocity 0 sent to the Push survives as "off". `R052:230`: the service rewrites velocity 0 to 1 (source reading, not observed) | Concerns host-to-Push traffic and belongs to [03-leds.md](03-leds.md). Push-to-host: touch release arrives as Note On velocity 0 on macOS and on Windows alike, so both paths deliver it unchanged (S01, S09, S10, S11) |
| Working-summary values corrected here | Touch lead of 82-530 ms for turns; Note 13 median 567 ms; "burst of 18 strikes"; CC 14 and CC 79 on both OS; jog Windows "1 event" | Recounted from the logs: turn lead 299-530 ms; Note 13 median 501 ms; the burst contains repeated notes; CC 14 and CC 79 appear only in Windows logs; jog turns on Windows are 351 events in the settings logs |
