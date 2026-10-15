# Timing

**Scope.** This page collects every latency, response-time, rate and ordering measurement recorded for the Push 3, with the sample count, the method and the session of each. It rests mostly on S04 (Windows, 2026-10-03: ground-truth probe logs and the Python focus logger), S03 and S09 (macOS), S06 and S11 (Windows) and S08 (Windows, replug); two audio rows rest on the prose-only UE audio spikes S12 (Windows) and S13 (macOS). Display rates are repeated here as rates; the display protocol itself is in [05-display.md](05-display.md), and what each control sends is in [02-controls.md](02-controls.md). MIDI channels, where they appear, are numbered 1-16. Read these caveats first.

- **Message loss was never counted.** No session sent a numbered stream and compared it with what arrived, and no session counted lost input. What exists are exchanges that completed (every reply arrived) and host-side losses in a library (see the last section).
- **Every time is a host-side interval.** A reply time is the gap between the host's send stamp and its receive callback, so it includes the host's MIDI stack. Clock resolution differs by record: 0.1 ms (the probe harness's own reply times; its log lines carry 0.001 ms stamps), 0.1 to 1 ms (Python tools), 10 ms (the first macOS mode test).
- **Windows has far more samples than macOS.** The macOS sub-millisecond reply times are n = 3.
- **Gaps of about 49.7 ms between the probe log's TX and RX lines are not reply times.** The harness's own stamps put the same replies at 0.3 to 0.6 ms; the extra time is host-side delay in writing the log line (S04 block b notes below).
- Firmware: FW label 1.5.4 (from notes) applies to S01 and S03 only; Device Inquiry build 91 was read in S01, S02, S03, S04 and S11; in the other sessions firmware is not logged. Windows build 26300 for S04, S06 and S08.
- Evidence labels: `Measured` means the result is in a raw record from this unit; `Reported` means it is written down only in notes or prose, with no raw record of the result; `Inferred`, `Third-party`, `Official` and `Not tested` are as defined in the README legend.

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Reply to a Device Inquiry or palette read | Median 0.4 to 0.5 ms, maximum 0.9 ms (Windows, harness stamps, n = 18 per port and request) | Measured | S04 | `R027` (43 files, `GT SUMMARY` lines, recounted) |
| Reply to a mode request (`0A`) | Median 0.4 ms in each of the two Windows groups (n = 17 and 23, harness stamps); 0.6 to 1.3 ms (macOS, n = 3, either port's copy) | Measured | S04, S09 | `R027`; `R024:7-9,31-33`; `R038:91-93` |
| Gap between the Live-port and User-port copies of a report | Median 0.07 ms, maximum 0.14 ms (Windows, n = 76 pairs) | Measured | S04 | `R014:4-3586` (recounted) |
| Active Sensing | `FE` every 270 ms on the Live and User ports; none on the External port | Measured | S04, S07, S09 | [1] |
| User button to mode change | Done by Live: 26 to 938 ms depending on the case (tables below) | Measured | S03, S04 | `R010:150-152,170`; `R014:4-1689` |
| Display frame rate | About 60 fps cap; write about 16.6 ms at 60 fps and about 11 ms at 30 fps | Measured | S01, S02, S04, S06 | `R016:26`; `R116:1871-2236` |
| Display stops being fed | Dark after about 2 s, built-in guide screen after about 12 s | Reported | S01 | `R001:22` |
| Audio capture stage (macOS) | Onset to reader callback: median 9.97 ms (input), n = 10 | Measured | S09 | `R129:261-262`; `R127:6` |
| Replug, ports back | 0.81 s after arrival; display claimed 1.30 s after (n = 1) | Reported | S08 | `R011:126-139` |
| Message loss | Not counted | Not tested | — | — |

Sources for this table:

1. `R054:14-23` (S04: `FE` packets 270 ms apart on the Live and User ports); `R029:1133,2997` (S07: final counts); `R024:24` (S09: final counts); the same figures in the notes `R048:79`, `R026:27`, `R023:14-16`.

## Request and reply times

### Windows ground-truth client, S04 block b

Method: a WinMM client (python-rtmidi, Windows QPC timestamps; `R130:101-106,140-155`) opened all three Push ports next to the library under test and logged its own sends (TX) and receives (RX). The value is the client's own reply time from its `GT SUMMARY` lines: from a QPC stamp taken just before the send call to the stamp taken on entry to the receive callback, printed at 0.1 ms resolution (a Device Inquiry or palette reply on the same cable; for a mode request the first mode report on cable 1 or 2 before the next send). Counts and times were recomputed over all 43 merged logs for this page; Live was closed. The log labels "mode1" and "mode2" are the mode values 1 (`0A 01`) and 2 (`0A 02`).

| Request | Cable (port) | Requests | Replies | Minimum / median / maximum, ms | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|---|
| Device Inquiry | 1 (Live) | 18 | 18 | 0.3 / 0.4 / 0.9 | Measured | S04 | `R027` |
| Device Inquiry | 2 (User) | 18 | 18 | 0.3 / 0.5 / 0.6 | Measured | S04 | `R027` |
| Device Inquiry | 3 (External) | 14 | 0 | — | Measured | S04 | `R027` |
| Palette read (entry 0) | 1 (Live) | 18 | 18 | 0.3 / 0.4 / 0.7 | Measured | S04 | `R027` |
| Palette read (entry 0) | 2 (User) | 18 | 18 | 0.3 / 0.5 / 0.6 | Measured | S04 | `R027` |
| Palette read (entry 0) | 3 (External) | 1 | 0 | — | Measured | S04 | `R027` |
| Mode set, value 1 | 1 (Live) | 17 | 17 | 0.3 / 0.4 / 0.6 | Measured | S04 | `R027` |
| Mode set, value 1 | 2 (User) | 1 | 1 | 0.4 | Measured | S04 | `R027` |
| Mode set, value 2 | 1 (Live) | 23 | 23 | 0.3 / 0.4 / 0.5 | Measured | S04 | `R027` |
| Mode set, value 2 | 3 (External) | 1 | 0 | no report for this request | Measured | S04 | `R027` |

Source files: the 43 `R027` files; example lines `R019:9-34`.

Notes. The gap between a TX log line and the matching RX log line is a different and worse measure: the TX line is stamped after the send call returns, and an RX line is stamped when it is written, after the callback. Measured that way, 113 replies give a median of 0.23 ms, but 8 of them show 49.57 to 49.80 ms (4 of 41 mode replies, 2 of 36 Device Inquiry replies, 2 of 36 palette replies), one palette reply 17.57 ms and one mode reply 10.25 ms. The harness's own stamps put every one of these replies at 0.3 to 0.6 ms (for example `R019:19-21,32`: 49.80 ms between the lines, +0.4 ms in the summary), so the long values are host-side delay between the callback and the writing of its log line, not reply time. The first report after the External-cable mode request arrived 748.77 ms later; it answers the next request (cable 1, sent 699 ms after), and the harness attributes nothing to the cable-3 request, so cable 3 gave no report (`R019:22-25,33`).

### Other request and reply records

| Record | Result | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| macOS, `mode_test`, 10 ms log resolution | The mode report is stamped 0.01 s after each send (upper bound only) | 4 | Measured | S03 | `R008:4-6,9-11,16-18,21-23` |
| macOS, python-rtmidi, perf-counter ms in one process, `0A 01` sent on the Live Port | Report on the Live port 0.6, 0.85 and 1.2 ms after the send; on the User port 0.9, 1.18 and 1.3 ms after | 3 | Measured | S09 | `R024:7-9,31-33`; `R038:91-93` |
| Windows, Python, `0A 01` on the Live-port output | Report on both ports 0.0 and 0.1 ms after the send, and 0.2 and 0.3 ms after a re-send (0.1 ms log resolution); a third send reported in the same millisecond (1 ms resolution) | 3 | Measured | S07 | [1] |
| Windows, User Port, millisecond log: `0A 01`, Device Inquiry, four `38` reads, `27` read, `0A 02` | Each reply on the asked port (the two mode reports on both ports) in the same or the next millisecond (0 or 1 ms) | 8 requests, 8 answered | Measured | S11 | `R009:3-28` |
| Windows, User Port, `1F` reply after `1E 00` | Reply 0.3, 0.3, 0.4 and 0.4 ms after the write, in four sessions. In the first session the reply carried the old value (02, MPE); in the other three it carried 00 | 4 | Measured | S11 | [2] |
| Settings read-out, 727 values | All obtained in about 4 s (reads paced by the host; port not stated) | 1 run | Reported | S03 | `R001:106` |
| Palette dump, 128 entries | About 3 s (log stamps 23:16:25 for the inquiry and 23:16:28 for the finished dump, 1 s resolution), with a 20 ms wait per entry set in the script (host pacing, not a device limit) | 1 run | Measured | S01 | `R005:4-5`; `R104:89` |

Sources for this table:

1. `R029:11-13`; `R036:49-51`; `R037:14-16`; `R026:24`.
2. `R060:20-22`; `R063:22-24`; `R061:20-22`; `R062:20-22`.

### Mode-report latency: three records and their time bases

Three records give the delay between a mode request (`0A`) and its report as different numbers. Each was re-read in its original log; the time base explains the difference.

| Record | Figure as first reported | Time base | Check against the original | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| Windows, User Port probe | Report in the same millisecond as the request | Seconds since the probe started, printed at 1 ms; the same script writes the TX and the RX lines | `0A 01`: the TX line and both RX lines read +0.059 s; the exit `0A 02`: TX +3.390 s, RX +3.391 s. So 0 to 1 ms, limited by the 1 ms resolution | Measured | S11 | `R009:3-5,25-27` |
| Windows, libremidi probe, ground-truth client | About 50 ms between the TX line and the RX line of a mode request | `T=` monotonic stamps of the log lines: the TX line is stamped after the send call returns, the RX line when it is written | TX `T=521535.810293`, RX `T=521535.860093` = 49.80 ms; the harness's own summary for the same request reads +0.4 ms (pre-send stamp to callback entry). By line stamps 4 of 41 mode replies read 49.57 to 49.80 ms; by the harness stamps all 41 read 0.3 to 0.6 ms | Measured | S04 | `R019:19-21,32`; `R130:101-106,140-155` |
| macOS, H27-Mac capture | Mode report "at +752.5 ms" (Live port) and "+752.8 ms" (User port) | Milliseconds since the capture process started (Python perf_counter); the evidence note gives no time base. The same process sent the request | TX 751.9 ms, RX Live 752.5 ms and RX User 752.8 ms on that clock, so 0.6 ms and 0.9 ms after the send; the wall clock reads 12:45:50.255 for the send and 12:45:50.256 for both reports. The figure is a clock offset, not a delay | Measured | S09 | `R024:7-9`; `R023:32` |

The three records agree once the time base is read: the report follows the request by 0.3 to 1.3 ms on both OSes at the resolution each record has. The 50 ms is host-side delay in writing a log line and the 752 ms is the capture's own clock at the moment of the request; neither is a report latency. What does not reconcile is the clock itself: the S04 harness stamps (QPC, printed at 0.1 ms), the S07 and S09 Python perf counters, the S11 script stamps (1 ms) and the S03 log (10 ms) are four different clocks, so differences below 1 ms between sessions are not comparable. That is recorded as a gap.

### The two copies of a report

Every mode report and every User-button message appears on both the Live port and the User port. The gap between the copies:

| Case | Gap | n | First copy | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| macOS, mode report | 0.3, 0.33 and 0.1 ms; the User copy later each time | 3 | Live 3 of 3 | Measured | S09 | `R024:8-9,32-33`; `R038:92-93` |
| Windows, mode report | Median 0.07 ms, maximum 0.14 ms over all 76 pairs below | 28 | Live 20, User 8 | Measured | S04 | `R014:4-3586` |
| Windows, User button press (CC 59 = 127) | as above | 24 | Live 15, User 9 | Measured | S04 | `R014:4-3586` |
| Windows, User button release (CC 59 = 0) | as above | 24 | Live 24, User 0 | Measured | S04 | `R014:4-3586` |

The Windows order is the order in which the host's callbacks fired, which need not equal the order on the wire.

## User button and mode changes

The Push sends CC 59 and does not change mode itself; Live sends `0A` after a press (mechanics in [01-connection.md](01-connection.md)). Time is from the press (CC 59 = 127) to the first mode report. All of these ran with Live started.

| Case | Press to report | n | Notes | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| macOS, Live to User | Live's `0A 01` 152 ms after the press | 1 | The Push reported the same millisecond | Measured | S03 | `R010:17-18,150-152` |
| macOS, User to Live | Live's `0A 00` 44 ms after the press | 1 | The Push reported 3 ms after Live's command | Measured | S03 | `R010:168-172,639-640` |
| Windows, to User, another process holds the display | 567 to 938 ms | 8 | 4 of the 8 flapped | Measured | S04 | `R014:4-177` |
| Windows, to Live, another process holds the display | 95 to 500 ms | 4 | | Measured | S04 | `R014:4-177` |
| Windows, to User, Live's own helper holds the display | 133 to 200 ms | 6 | 0 flaps | Measured | S04 | `R014:968-1689` |
| Windows, to Live, Live's own helper holds the display | 26 to 34 ms | 6 | | Measured | S04 | `R014:968-1689` |
| macOS, display held by Unreal Editor | No flap | 2 trials | Prose only | Reported | S05 | `R001:207` |

Press to report in ms, in press order. Display held by another process: to User 567, 938, 653, 815, 797, 641, 667, 904; to Live 412, 236, 500, 95. Display held by Live's helper: to User 200, 160, 144, 134, 191, 133; to Live 33, 34, 26, 34, 28, 26. A flap is `0A 01` followed by `0A 00` within 0 to 1 ms (4 of 4 flaps in this log had the `0A 01` first); the recount differs by a few milliseconds from the notes (570 to 940, 133 to 199, 94 to 501, 25 to 33). The 8 presses with the spike bound (S06 block b, 2 flaps) have no press times in the log (`R042:2108-2132`). The macOS ordering in the first two rows (Live's command, then the Push's report) is the order a MIDI Monitor capture lists the messages in, taken during a burst of about 460 LED messages from Live to the Push, and may not reflect the wire order.

## Periodic traffic and rates

| Item | Value | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Active Sensing (`FE`) | Every 270 ms on the Live and User ports; none on the External port | S09: 1,900 per port in 513 s (macOS); S07: 1,369 per port in about 371 s (Windows); S04: 270 ms between packets (probe log) | Measured | S04, S07, S09 | [1] |
| Live's polling replies on the Live port | `38 18 ...` and `38 0D ...` replies alternate, each type with a median interval of 0.60 s (so one reply about every 0.3 s); `3A` replies every 1.20 s (median) | 3,430 replies in 900 s: 1,372, 1,373 and 685 | Measured | S04 | `R014:1-3586` (recounted) |
| Live's polling on macOS | About every 0.3 s and about every 1.2 s | prose | Reported | S03 | `R001:77,149` |
| Held pad in MPE mode, CC 74 stream | 45,112 CC 74 messages on channel 4 in 607 s, about 74 per second, while one pad stayed pressed (mock-run log, User mode). Inside the hold the log shows one Note Off and Note On pair 0.1 ms apart on the same note (`R056:47911-47914`) | 1 pad, 1 hold | Measured | S09 | `R056:11180-56613` (recounted by grep) |
| Live's start-up burst | 336 SysEx messages to the Push in 2.18 s | 1 start | Measured | S03 | `R046:1-336` |
| Live's `0A 00` after its helper starts (as the Push's mode report; the script logs only the Push's output) | 2.562 s and 2.557 s after the helper appeared | 2 | Measured | S03 | `R020:196,202-203`; `R041:53,59-60` |
| LED steps sent and shown | 20 steps per second (a four-pad chase, 64 steps per 3.20 s, 20 laps); every host send returned ok and the owner saw smooth motion. This is the host's rate; no ceiling was sought | 20 laps | Measured | S04 | `R030:2329-3738`; `R001:221,223` |

Sources for this table:

1. `R024:24` (S09); `R029:1133,2997` (S07); `R054:14-23` (S04); the same figures in the notes `R048:79`, `R026:27`, `R023:14-16`.

Pad, touch strip and jog input rate ceilings (messages per second at the fastest gestures) were not measured as rates.

## Display

Rates are per 5 s status window of the host (frames sent, average write time of the bulk transfer, peak). "Errors 0" counts USB write failures.

| Case | Result | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| macOS, libusb, unthrottled | 301 frames, 60.1 fps, average write 16.64 ms | 1 run | Measured | S01 | `R016:26` |
| macOS, libusb, target 60 fps | 300 frames, 60.0 fps, 13.49 ms; a 20 s run: 1,200 frames, 59.9 fps, 15.77 ms | 2 runs | Measured | S01 | `R016:28,34` |
| Windows, libusb over WinUSB | 301 frames, 60.2 fps, average write 16.62 ms | 1 run | Measured | S02 | `R017:2` |
| macOS, native IOKit | 59.8 to 60.0 fps, write 16.6 ms on average, 0 errors; page mode: write 12.5 ms | n/l (prose) | Reported | S03 | `R117:385-388,399` |
| Windows, WinUSB, bar pattern at 60 fps | 179 windows, 53,637 frames, 59.8 to 60.0 fps, write average 16.56 to 16.62 ms, peak 21.88 ms, errors 0, open failures 0 | 179 windows | Measured | S04 | `R116:1871-2236` |
| Windows, WinUSB, page mode at 30 fps | 84 windows, 12,587 frames, 29.4 to 30.4 fps, write 10.96 to 11.13 ms, peak 16.80 ms, errors 0 | 84 windows | Measured | S06 | `R042:1919-2227` |
| Windows, WinUSB, page mode with LED traffic | 465 windows, 69,940 frames, 25.2 to 35.0 fps, write 10.94 to 11.32 ms, peak 23.39 ms, errors 0 | 465 windows | Measured | S04 | `R030:1913-4748` |
| Windows, WinUSB, idle keep-alive frames | 41 windows, 416 frames, 1.8 to 5.2 fps, write 10.95 to 11.11 ms, errors 0 | 41 windows | Measured | S04 | `R003:1908-2505` |
| Windows, 40 minutes | 2,400.7 s: 418 frames and 4,592 keep-alive frames, 0 write errors, 0 re-opens, write average 11.1 to 11.6 ms (239 status lines) | 1 run | Measured | S06 | `R022:578-582` |
| Windows, libusb, mock host at 30 fps | 5 runs between 2026-10-07 18:41 and 2026-10-08 02:47 (the longest about 5.1 h): 3,968 status lines, all with errors 0, write 11.3 to 11.8 ms (recounted) | 3,968 lines | Measured | S10 | `R056:57190-90353` |
| macOS, libusb, mock host aiming at 60 fps | Not sustained: 50.2 to 51.1 frames/s at 17.7 to 18.1 ms per write, so the mock stepped down to 50 (3 times); at 50 it reached 37.9 and 38.1 frames/s at 18.1 and 19.1 ms, so it stepped to 40 (2 times) and once on to 30; no USB errors | 6 step-downs | Measured | S09 | `R056:9036-9037,9445,9657,10583-10584,11164-11166` |
| macOS, libusb, mock host at 30 fps | 517 status lines, write 16.6 to 18.4 ms, errors 0 (recounted) | 517 lines | Measured | S09 | `R056:1-57189` |

On Windows the write time depends on the send rate (about 11 ms at 30 fps against about 16.6 ms at 60 fps). On macOS it does not follow that pattern: the libusb mock wrote 16.6 to 18.4 ms per frame at 30 fps. That the 16.6 ms is device pacing and not bus time is therefore only an inference from the Windows pattern (Inferred, Windows-only, no direct test). The cause of the macOS mock not holding 60 fps was not isolated (frame content, host path or device pacing).

| Item | Value | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Frames stop arriving | Screen goes dark after about 2 s; the built-in guide screen ("Connect Push to a computer...") appears after about 12 s. The times are the notes' summary; the log records only the runs | 7 static runs (6 with a 15 s watch after the stop) | Reported | S01 | `R001:22`; `R016:1-32` |
| Same, Windows | Black, then the guide prompt about 10 s later (rough, by eye) | 1 | Reported | S08 | `R011:155-160` |
| Orderly release, 40-minute run | `0A 02`, then the black frame 218 ms later, with IF#0 released in the same instant | 1 | Measured | S06 | `R022:578-581` |
| Orderly release, hosted controller | `0A 02`, then the black frame 25 ms later, then IF#0 released | 1 | Reported | S08 | `R011:150-151`; the `0A 02` report itself is `R012:61-62` |
| Input to display, CPU raster path | Input to USB done: average about 22 ms (p95 30, max 32); raster about 0.3 ms per frame. n not stated | n/l | Reported | S03 | `R100:42` |
| Input to display, GPU readback path | About 128 ms (delivery B) and 145 ms (delivery A), of which about 100 ms is waiting for the GPU readback | n/l | Reported | S03 | `R100:42` |
| Draw to glass | An estimate of about 115 ms from input to screen (about 100 ms of readback plus about 13 ms of USB); the glass was not instrumented | — | Inferred | — | `R117:398` |
| Re-acquire after another process lets go (macOS, IOKit) | Within 0.8 s of the holder's release | n/l (prose) | Reported | S03 | `R117:387` |

Latency from a write to light on the glass was not measured on either OS.

## Audio

| Case | Result | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| macOS, CoreAudio, input stream | 863 callbacks of 512 frames in 10.11 s (a callback every 11.7 ms); 0 sample-time discontinuities, 0 overloads | 1 capture | Measured | S09 | `R120:21,38` |
| macOS, click onset to the reader's callback, Push input stream | Median 9.97 ms (minimum 2.50, maximum 13.27) | 10 clicks | Measured | S09 | `R127:6` |
| Same clicks, global process tap | Median 6.59 ms (minimum 0.59, maximum 9.92) | 10 clicks | Measured | S09 | `R127:7` |
| Push onset minus tap onset, per click | 2.33 to 2.36 ms, median 2.36 | 10 clicks | Measured | S09 | `R127:4-5` |
| macOS, CoreAudio-reported input path | 25 + 68 frames + one 512-frame buffer = 13.72 ms at 44.1 kHz | — | Measured | S09 | `R124:10` |
| macOS, Unreal capture, callback size | Callbacks at 256, 512 and 1024 frames; jitter at most 0.15 ms; 0 overflows in 5 s and 20 s (prose; run count n/l) | at least 2 runs | Reported | S13 | `R119:331` |
| Windows, Unreal WASAPI shared mode | 436 callbacks of 441 frames, one per 10.0 ms (sd 0.07 ms), 435 intervals = 4.35 s; the requested 512 frames were not honoured; one overflow flag at the first packet (inferred to be the first-packet gap). Prose only; Live's state not recorded | 1 run, 5 s | Reported | S12 | `R119:334-336` |

The click source was the Mac's headphone output patched to the Push's input 1, so the figures cover only the capture stage after the signal reaches the Push. The two differences (the medians differ by 3.4 ms, the onsets by 2.3 ms) are different quantities as logged. No Windows capture latency, no output latency and no round trip were measured. Audio detail is in [06-audio.md](06-audio.md).

## Disconnects, recovery and waits

| Case | Timeline | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Replug, Windows, spike bound (UTC) | Display write fails 03:33:18.771 (Win32 22); MIDI ports removed 03:33:21.112 (+2.34 s); display re-acquired 03:33:27.783 (after 9 attempts, 9.011 s after the first failure); ports back 03:33:27.827; `0A 01` acknowledged at once | 1 | Measured | S06 | `R118:1938,2017`; `R001:250-252` |
| Replug, Windows, hosted controller (UTC) | Display write fails 03:23:44.397 (Win32 995); PnP removal 46.957; MIDI ports removed 46.999 to 47.015; device back 57.094 (absent 10.1 s); MIDI ports 57.900 to 58.022; display claimed 58.393 after 13 attempts; bound 58.397; `0A 01` acknowledged 58.411 | 1 | Reported | S08 | `R011:126-139`; the PnP times are also in `R131:5-10` |
| From the arrival to usable | Arrival to MIDI ports 0.81 s; arrival to display claimed 1.30 s (the display's 1 s retry cadence) | 1 | Reported | S08 | `R011:137-138`; arrival time also in `R131:8` |
| Display fails before the OS notices, spike bound | The display write failed 2.34 s before the MIDI ports were removed | 1 | Measured | S06 | `R118:1938,2017`; `R001:250-252` |
| Display fails before the OS notices, hosted controller | The display write failed 2.56 s before PnP reported the removal | 1 | Reported | S08 | `R011:137`; the PnP time is also in `R131:5` |
| Window recorded in the first status window after the re-open | One window averaged 65.78 ms per write with a 499.87 ms peak (9 frames); the next windows were back to 11.5 ms | 1 | Measured | S06 | `R118:2066,2069` |
| Live started first on Windows, display requested by a second process | Refused with Win32 5 for 520.704 s (03:31:56.035 to 03:40:36.739 UTC), retried every 1000 ms, 524 failed attempts, then acquired. **Single observation.** What ended the wait is not logged | 1 | Measured | S04 | `R003:1859,2357-2358` |

The Win32 error code at the pull differs between runs (22 against 995); see Conflicting records.

## Message ordering

Further control-level detail is in [02-controls.md](02-controls.md). The order is the order the host received messages.

| Case | Order observed | n | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Mode report | Delivered on the Live port and the User port; which copy comes first varies (see "The two copies of a report") | 28 | Measured | S04 | `R014:4-3586` |
| Flap | `0A 01` first, `0A 00` within 0 to 1 ms | 4 | Measured | S04 | `R014:4-177` |
| Press of the User button | The Push reports nothing; Live commands, then the Push reports (order as the MIDI Monitor lists it) | 2 (macOS) | Measured | S03 | `R010:150-152,168-172` |
| MPE note start | Channel pressure and CC 74 initial values arrive before the Note On; a channel pressure 0 follows it in the burst logs | burst logs | Measured | S03 | `R028:2-5` |
| Encoder touch before turn | In the guided captures the touch Note came 299 to 530 ms before the first turn CC (6 gestures: jog wheel, Swing & Tempo and Volume, 2 each). In the long mock logs, 125 of 15,001 turn CCs (encoders, Swing & Tempo, Volume and jog) arrived with no active touch (macOS 69 of 4,613; Windows 56 of 10,388) | 6 guided; 15,001 turns | Measured | S01, S09, S10 | [1] |
| Jog press and its touch Note | The press arrived before its own touch-on in 46 of 52 presses (1 to 89 ms, median 50); touch-on first in 6 (recount of the raw log: 52 presses, 58 touch-ons, 6 touch-ons ahead of their press) | 52 | Measured | S07 | `R029`; `R026:43-49` |
| D-pad centre, S01 guided capture (Live Port; MIDI mode not queried) | CC 91 = 127 first, Note 13 on 59 ms later; Note 13 off about 190 ms after the CC release | 1 | Measured | S01 | `R007:76`; `R074` |
| D-pad centre, User mode (User Port) | Note 13 already on at the centre press in 5 of 5 (lead 15, 43, 230, 1272 and 1695 ms; the 230 and 1695 ms presses fall in one Note 13 hold) | 5 | Measured | S11 | `R060:1042-1043,2308-2315,9524-9533` |
| Touch strip touch and release | Pitch bend at centre (raw 8192, "0" on the signed scale) in the same millisecond as Note 12: after the touch-on Note, and before the touch-off Note on release | 1 | Measured | S01 | `R073`; `R007:35-36` |
| Two encoders touched together | Two touch-ons 3 ms apart | 1 | Measured | S06 | `R022:44-70` |
| Unplug while a control is held | Its release is never sent | 1 | Reported | S06 | `R001:250-254` |
| Read after write, pad mode | A `1F` read issued right after `1E 00` (sent by the mock without a log line, `R070:425-432`) returned the old value (02) in one of four sessions and the new value in the other three | 4 | Measured | S11 | `R060:20-22`; `R063:22-23` |

Sources for this table:

1. `R001:33`; `R072`; `R071`; `R032`; `R056:1-90353` (recounted).

## Message loss

Loss was not counted in any session. The records that bear on it:

| Record | What it shows | Evidence | Sessions | Source |
|---|---|---|---|---|
| Requests answered | Every Device Inquiry, palette and mode request on the Live and User cables was answered (36 of 36, 36 of 36 and 41 of 41 in the table above); no request on the External cable was ever answered (why is not documented) | Measured | S04 | `R027` |
| Settings write and read-back | 18 `37` writes, 18 `38` read-backs, 18 of 18 equal | Measured | S11 | `R063:2777-2986` |
| Settings read-backs in the owner walk | 33 `38` replies in the owner walk all matched | Reported | S11 | `R099:325-326` |
| Display writes | 0 write errors in every status window of the Windows spikes, the 40-minute run and both mock segments (USB writes, not MIDI) | Measured | S04, S06, S09, S10 | `R116:1871-2236`; `R022:579`; `R056:1-90353` |
| Host-side SysEx loss | A pre-fix libremidi path completed 0 of 8 SysEx replies that the raw callback and the WinMM client received 8 of 8; this is host loss, not device loss (see [01-connection.md](01-connection.md), Host-side notes) | Measured | S04 | [1] |
| Lost release at unplug | A control held at the pull never sent its release | Reported | S06 | `R001:253-254` |

Sources for this table:

1. `R053` (8 `SYSEX-COMPLETE`) and `R054` (0 `SYSEX-COMPLETE`, 8 `GT RX`); the same result in the notes `R048:22`.

## Not yet verified

- Message loss in any direction: no counted burst of notes, controllers or SysEx at stepped rates, and no long run with a sent-against-received tally.
- Input rate ceilings (pad aftertouch, jog, touch strip, encoders) as messages per second.
- Reply times at sub-0.1 ms resolution on either OS (the harness prints 0.1 ms; macOS n = 3 only); the External cable never answered. One clock for the mode-report latency across sessions: the S03, S04, S09 and S11 figures use four different stamps (see "Mode-report latency").
- Button-to-Live reaction time on macOS (n = 2), flap statistics (Windows 8 and 6 presses; macOS 2 trials) and the dependence on what holds the display.
- Write-to-glass display latency on either OS; the macOS 115 ms is a pipeline estimate.
- Blink and pulse periods in milliseconds (only their order was recorded, see [03-leds.md](03-leds.md)).
- Windows audio latency, output latency and any round trip; capture stage on macOS with Live running.
- Power-up and wake times; time from USB plug-in to the first answered request.
- Recovery after replug with a different host state (Live running, macOS); the Win32 22 against 995 difference.

See [08-gaps.md#timing](08-gaps.md#timing).

## Conflicting records

| Question | Record A | Record B | Which wins and why |
|---|---|---|---|
| Resolution of the macOS mode-report time | `R008` (S03): 10 ms resolution, report 0.01 s after each send | `R024` (S09): 0.6 to 1.3 ms | Not a conflict. The S09 perf-counter records give the finer value; the S03 value is an upper bound. |
| Windows libusb 60.2 fps has a raw record | `R001:17` quotes 60.2 fps without a source | `R017:2` | The raw log exists (it is a separate file from the macOS `R016`). |
| Win32 error code at the pull | `R001:250` and the spike log: 22 | `R011:126`: 995 | Different runs and builds (S06 spike, S08 hosted controller); cause not recorded. Unresolved. |
| Press to Live's reaction | Notes: 570 to 940, 133 to 199, 94 to 501 and 25 to 33 ms | Recount of `R014`: 567 to 938, 133 to 200, 95 to 500 and 26 to 34 ms | The recount from the raw log wins (differences of a few ms). |
| D-pad centre: Note 13 before or after CC 91 | S01 capture (Live Port, MIDI mode not queried): CC 91 first, Note 13 +59 ms (n = 1) | S11 User mode: Note 13 first in 5 of 5 | Unresolved. The modes, ports and dates differ; the S11 sample is larger. See [02-controls.md](02-controls.md). |
| Touch Note always before a turn | `R001:33` | 125 of 15,001 turn CCs without an active touch | The long logs show exceptions (about 0.8 %); the notes' "always" holds for the guided single gestures only. |
| Why the display write time is 11 ms at 30 fps and 16.6 ms at 60 fps (Windows) | Device pacing (inference) | Not isolated; the macOS mock could not hold 60 fps at 17.7 ms, and at 30 fps it still wrote 16.6 to 18.4 ms per frame | Unresolved. No test separated device pacing from host path or frame content. |
| Why some probe replies looked like 49.7 ms | Earlier recount from the TX and RX log-line stamps | The harness's own `GT SUMMARY` stamps: 0.3 to 0.6 ms for the same replies | The harness stamps win: they are taken at the send call and at callback entry, the log-line stamps when the line is written. Resolved. |
| What is the "+752 ms" mode report in the H27 note? | `R023:32`: "`0A 01` ... at +752.5 ms" | `R024:7-9`: 752.5 ms is the capture process's own clock; the request is stamped 751.9 ms on the same clock | Resolved: the report came 0.6 ms after the request; +752.5 ms is the offset from the start of the capture. The log wins; the note states no time base. |
