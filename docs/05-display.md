# Display

This page records how the 960 x 160 display of the Push 3 unit is fed over USB: the interface and endpoint, the frame format the unit accepted, the rates and write times that were logged, what the unit shows when frames stop, and what happens when the host program or the cable goes away. It rests on S01 and S03 (macOS; a Python test script over libusb, and an IOKit spike), S02 (Windows; the same script over libusb), S04 and S06 (Windows; an Unreal Editor spike over WinUSB and a Python device mock), S08 (Windows; a host program and an unplug), S09 (macOS; the tilt-card runs of the same test script, block b) and S09 and S10 (macOS and Windows; a screen mock using pyusb). The sessions are defined in [09-sessions.md](09-sessions.md); the display interface and who may hold it are also covered in [01-connection.md](01-connection.md#display-interface-usb-if0).

Three things a reader must know first.

1. The frame-format rows are labelled `Reported`. The recipe is the set of constants in the Python display test script; the logs show that frames built from it were sent at the rates listed with no write error; the notes say the unit showed the image and that the protocol is the Push 2 one (`R001:12`). No raw record captures what the screen showed. `Reported` does not mean that each element of the recipe was shown to be required: no negative test was run (no frame without the XOR, with a different chunk size, with the filler left raw, header only, or truncated), and no record states that red, green and blue appeared in the right colours. Whether the recipe is necessary, as opposed to sufficient, rests on the Push 2 specification, which is Third-party here.
2. The stop-sending behaviour (screen dark after about 2 s, built-in guide screen after about 12 s) rests on one prose sentence in the findings notes. `R016` records only the start and stop lines of those runs, not when the screen changed. See [Stop-sending behaviour](#stop-sending-behaviour).
3. Write times and frame rates are host-side timings of the USB write call. No measurement of the time from the end of a write to light on the glass exists; the figures given for the full path are host-pipeline timings or estimates.

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| USB interface | IF#0 "Ableton Push 3 Display", bulk OUT endpoint 0x01 (type 2), wMaxPacketSize 512; VID 0x2982, PID 0x1969 | Measured | S01, S02 | `R016:1`, `R017:1` |
| Frame | 16-byte header, then 160 lines x 2048 bytes = 327,680 bytes; every 4 bytes XORed with `E7 F3 E7 FF`; 16-bit RGB565 little endian | Reported | S01, S02 | `R115:18-22,38-49,100-103`, `R001:12` |
| Refresh | Ceiling of about 60 fps: 60.1 fps unthrottled on macOS, 60.2 fps on Windows, 59.8-60.0 fps through WinUSB | Measured | S01, S02, S04 | [1], [2], [3] |
| Frames stop | Dark about 2 s after the last frame; the unit's own "Connect Push to a computer..." screen about 12 s after. Prose only, no raw record | Reported | S01 | `R001:22` |
| Holder | One program holds IF#0; the first claimant wins and the second is refused | Measured | S03, S04 | [4], [5], [6] |
| MIDI mode | The display does not depend on the MIDI mode | Reported | S03 | `R001:152,173` |
| Official | No Ableton document gives the Push 3 display protocol; one secondary statement says the display is unchanged from Push 2 | Official | — | `R018:198` |

Sources for this table:

1. `R016:26`
2. `R017:2`
3. `R116:1871-2236`
4. `R013:4`
5. `R003:1859`
6. `R001:141-145`

Not tested: any variant of the frame recipe, the colour order on the glass, the exact stop timings, the display at brightness 0, the time from write to light.

## USB interface

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Identity | IF#0 string "Ableton Push 3 Display"; bulk OUT endpoint 0x01, type 2 (bulk), wMaxPacketSize 512; VID 0x2982, PID 0x1969 | Measured | S01, S02 | `R016:1`, `R017:1`, `R115:18` |
| Interface class | Vendor-specific (class 255) | Reported | S01 | `R001:12` |
| How the script opened it | Finds the interface by the string "Ableton Push 3 Display", claims it (no kernel-driver detach call in the script) and takes its first OUT endpoint; the same code ran on both operating systems | Measured | S01, S02 | `R115:79-98`, `R016:1`, `R017:1` |
| Windows driver | Microsoft WinUSB is bound to IF#0 automatically; the interface opens without administrator rights, through the libusb-package DLL or through SetupAPI and WinUSB | Reported | S02, S04 | `R001:17,123,190-191` |
| Independence from the MIDI mode | While Live showed "User Mode Active" (Live's own text for the User mode), a second claim of IF#0 still failed: the holder of the interface decides who draws, not the MIDI mode. Entering User mode darkens the LEDs and leaves the display alone | Reported | S03 | `R001:152,173` |
| Who holds the interface | First claimant wins both ways; the second claimant is refused (macOS `LIBUSB_ERROR_ACCESS` -3; Windows Win32 5 "Access is denied") | Measured | S03, S04 | [1], [2], [3] |
| Who holds the interface, further observations | macOS IOKit spike: the second claimant is refused with `kIOReturnExclusiveAccess`; Live's helper keeps retrying and takes the interface the moment it is free | Reported | S03 | `R001:141-145`, `R117:385-387` |

Sources for this table:

1. `R013:4`
2. `R003:1859`
3. `R001:141-145`

The coexistence runs with Ableton Live (counts, timings and the 520.7 s Windows wait) are in [01-connection.md](01-connection.md#display-interface-usb-if0). Not repeated here.

## Frame format

One frame is one header write followed by the pixel data. The constants below are the ones in the test script that drove the unit in S01 and S02.

| Element | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Header | 16 bytes: `FF CC AA 88` followed by 12 bytes of `00`; written on its own at the start of every frame | Reported | S01, S02 | `R115:20,100-103`, `R001:12` |
| Frame size | 327,680 bytes = 160 lines x 2048 bytes (after the header) | Reported | S01, S02 | `R115:19,43-49`, `R001:12` |
| Line layout | 960 pixels x 2 bytes = 1920 bytes of pixels, then 128 bytes of filler (zero before the XOR); 2048 bytes per line | Inferred | — | `R115:19,43-49` |
| XOR | Every 4 bytes of each 2048-byte line are XORed with `E7 F3 E7 FF` | Reported | S01, S02 | `R115:21,43-49`, `R001:12` |
| XOR, filler | The 128 filler bytes are included in the XOR | Inferred | — | `R115:21,43-49` |
| Pixel | 16-bit little endian: bits 15-11 = blue (5 bits), bits 10-5 = green (6 bits), bits 4-0 = red (5 bits); value = (r>>3) OR ((g>>2)<<5) OR ((b>>3)<<11). The bit order is from the Push 2 specification; no record checks it against the colour seen | Third-party | — | `R115:38-40`, `R001:12` |
| Transfer | Header, then the frame in 20 bulk writes of 16,384 bytes; every write with a timeout of 1000 ms | Inferred | — | `R115:22,100-103` |
| Test content | Red, green and blue bars (320 pixels each) over the top 100 lines, a white 2-pixel border, a grey ramp over the bottom 60 lines; an animation of 48 frames | Inferred | — | `R115:52-78` |
| Other senders | The UE spike over WinUSB (S04, S06 b), the device mock (S06 a), the working mock (S09 c, S10) also sent frames and logged 0 write errors | Measured | S04, S06, S09, S10 | [1], [2], [3] |
| Banding | RGB565 shows about 60 visible grey steps on the ramp (the 64 green levels dominate). Observation in prose, not quantified | Reported | S01 | `R001:24`, `R115:52-66` |

Sources for this table:

1. `R116:1871-2236`
2. `R022:576-582`
3. `R056:47-90342`

Note: the other senders' code is not part of this archive in every case, so the table states only that their frames were accepted without write errors; it does not say that their bytes equal the script's.

## Rate and write time

The write time is the average time the host spent in the USB write call for one frame. Every row has 0 write errors unless stated.

| Run | Result | Evidence | Sessions | Source |
|---|---|---|---|---|
| macOS, Python + libusb, animation, unthrottled, 301 frames | 60.1 fps, average write 16.64 ms | Measured | S01 | `R016:26` |
| macOS, same, target 60 fps, 300 frames | 60.0 fps, average write 13.49 ms | Measured | S01 | `R016:28` |
| macOS, same, target 60 fps, 1200 frames (20 s) | 59.9 fps, average write 15.77 ms | Measured | S01 | `R016:34` |
| Windows, Python + libusb (libusb-package), animation, unthrottled, 301 frames, driven over SSH | 60.2 fps, average write 16.62 ms | Measured | S02 | `R017:2` |
| Windows, UE spike over WinUSB, bar pattern, 180 status lines | 59.8-60.0 fps; average write 16.56-16.62 ms; 0 write errors | Measured | S04 | `R116:1871-2236` |
| Windows, UE spike over WinUSB, page content, 466 status lines | about 30 fps (25.2-35.0); average write 10.94-11.32 ms; 0 write errors | Measured | S04 | `R030:1913-4748` |
| Windows, device mock, 40-minute run | 418 changed frames and 4592 repeats of the last frame; average write 11.1-11.6 ms per 10 s status line (239 lines), largest single write 19.2 ms; 0 write errors, 0 re-opens | Measured | S06 | `R022:13-575,576-582` |
| macOS, screen mock (pyusb), 1996 status lines (every `status` line with a `usb sent` field in lines 1-57189) | By the mock's render clock, to the nearest 10 fps: about 30 fps in 1101 lines, about 50 fps in 855, about 40 fps in 37, about 60 fps in 3. Only 1411 of these lines carry a `target N fps` label (517 at 30, 38 at 40, 853 at 50, 3 at 60); the other 585 (lines 47-8987) carry none and all run at 30 fps; label and clock band differ on 3 lines. USB frames actually sent fell below 90 % of the clock in 91 of the 30 fps lines (down to 16.4 frames/s); the mock counts each frame it replaced before sending as dropped. Write 13.0-19.3 ms; 0 write errors. At a 60 fps target it could not hold 60 over 3.0 s (50.8 USB frames/s, write 17.7 ms) and stepped itself down to 50 fps, which it held (50.3 frames/s, write 17.9 ms) | Measured | S09 | `R056:47-57178`, `R056:9036-9037` |
| Windows, screen mock (pyusb), 3968 status lines | 30 fps (3961 lines at 30.0); write 11.3-11.8 ms; 0 write errors | Measured | S10 | `R056:57220-90342` |
| macOS, IOKit spike, bar pattern | 59.8-60.0 fps, average write 16.6 ms, 0 errors; in page mode the write took 12.5 ms. Prose in the research notes; no raw log. Assigned to S03 by its date, which 09-sessions.md does not state | Reported | S03 | `R117:383-400` |
| macOS, repeated static image at 10 fps for 2-15 minutes (tilt-card runs) | Ten runs. Seven wrote the image for the stated period and logged "done; display interface released"; three (15:46:37, 15:51:40, 15:53:14) have no "done" line and were followed by a new open before their period ended, cause not logged. No error line; what was on the screen is not logged | Measured | S09 | `R016:39-65` |
| Ceiling | The unit accepts about 60 frames per second and no more: an unthrottled loop gave 60.1 fps. That the limit sits in the device and not in the host loop is a deduction by the note's author | Inferred | — | `R001:23` |

Notes.
- The unthrottled write time (16.64 ms) is longer than the paced one (13.49 ms) in the same session. Both are consistent with the write call blocking until the unit accepts the next frame; that explanation is not tested.
- The two mock rows at 30 fps differ by host (13.0-18.4 ms on macOS, 11.3-11.8 ms on Windows) and so do the bare-script rows against the page-mode rows (16.6 ms against 11 ms). Whether frame content, host stack or device pacing causes this was not isolated; see [Conflicting records](#conflicting-records).
- Host-pipeline numbers from the UE spike on the Mac (S03 g, prose, no raw log): the CPU-raster path takes about 22 ms from input to the end of the USB write (p95 30 ms, max 32 ms); the GPU readback path about 128-145 ms. These time the host, not the display. The research note adds an estimate of about 115 ms from input to the screen for a GPU-bound scene, which is a pipeline sum and was not measured on the glass.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Host pipeline, CPU raster path | Input to USB-done about 22 ms (p95 30, max 32). Prose only, no raw log | Reported | S03 | `R100:42` |
| Host pipeline, GPU readback path | Input to USB-done about 128 ms (delivery B) and 145 ms (A). Prose only, no raw log | Reported | S03 | `R100:42` |
| Write to light on the glass | Estimated at about 115 ms in total for the GPU-bound scene (about 100 ms draw to readback, about 13 ms USB); the glass was not timed | Inferred | — | `R117:398` |

## Stop-sending behaviour

The display test script held a static frame (10 fps), stopped sending, then spoke a counter once per second so that the person watching could note when the screen changed. The log records the lines the script writes; the counter readings were not written down by the script.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Static runs | Eight runs on macOS: 1.0 s, six runs of 10 s, 2.0 s, each at 10 fps. Each log has "sending", "STOPPED sending" and "done" lines only | Measured | S01, S03 | `R016:1-4,5-8,9-12,13-16,17-20,21-24,29-32,35-38` |
| Counting window | The six 10-s runs have a 15 s counting window after the stop ("done (15s after stop)"); the 1.0 s and 2.0 s runs have 0.0 s, so no counting happened in them. The 2 s and 12 s transitions can only have been timed by the spoken counter in the 15 s windows (deduced from the log). The script keeps IF#0 claimed through the counting window and releases it afterwards (from the code) | Inferred | — | `R016:8,12,16,20,24,32`, `R115:124-135,150-152` |
| Result: dark | The screen goes dark about 2 s after the last frame. Prose only; not in the log, no per-run values | Reported | S01 | `R001:22` |
| Result: guide screen | The unit's built-in guide screen ("Connect Push to a computer...") returns about 12 s after the last frame. Prose only | Reported | S01 | `R001:22` |
| Windows static run | One run of 15.0 s at 10 fps with a 0.0 s counting window: no after-stop timing recorded | Measured | S02 | `R017:4-6` |
| Windows owner's view | After the host program released the interface and the screen went black, the owner saw the unit's own prompt ("Connect Push to a computer and open Live to make music") "about 10 s later", a rough value; the host's last frame was the black frame sent at release | Reported | S08 | `R011:155-157` |
| Push 2 specification | A Push 2 shows black if no frame arrives for 2 s | Third-party | — | `R064:55` |
| Repeating the last frame | A host program that re-sent the last frame every 0.5 s (script constant, commented "display blanks after ~2 s") ran for 40 minutes with 4592 repeats and 0 write errors. Whether the screen stayed lit throughout is not recorded in the log | Measured | S06 | [1], [2] |

Sources for this table:

1. `R058:68`
2. `R022:576-582`

Reading: the 2 s value agrees with the Push 2 specification, and the 12 s and about 10 s values are two rough observations on two operating systems; neither is a measured interval. No camera record or timestamped observation exists.

## Release, loss and recovery

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Orderly release (Windows) | The host sent `0A 02` (Dual) first; a black frame was written 25 ms later and IF#0 was released | Measured | S08 | `R011:146-157` |
| Orderly release (Windows), what the Push showed | After the release the Push showed black and, after its own timeout, its prompt | Reported | S08 | `R011:146-157` |
| Orderly release, spike | "stop - black frame sent, interface released" | Measured | S06 | `R042:2226`, `R118:2170` |
| Cable pulled, spike | The 16-byte header write failed with Win32 22 at 03:33:18.771 UTC and the program re-opened every 1 s; Windows MIDI Services reported the ports removed 2.3 s later; after the replug the display re-opened without host action and `0A 01` was acknowledged at once | Measured | S06 | `R118:1938`, `R001:252-253` |
| Cable pulled, spike, display while unplugged | The Push on its own power showed its "connect to Live" screen while unplugged | Reported | S06 | `R001:252-253` |
| Cable pulled, host program | The write failed with Win32 995 at 03:23:44.397 UTC and the program re-opened every 1 s; PnP reported removal 2.56 s after the write failed; the unit was absent for 10.1 s; the display was claimed again 1.30 s after the replug, after 13 failed attempts, and MIDI ports returned 0.81 s after the replug | Measured | S08 | `R011:124-139` |
| Order of loss | In both unplugs the display write failed first: 2.56 s before PnP reported the device removed (S08), 2.3 s before the MIDI ports were removed (S06) | Measured | S06, S08 | `R011:126-139`, `R001:252` |

## Push 2 desk research and the Push 3 unit

The Push 2 display protocol is documented by Ableton (the Push 2 MIDI and Display Interface Manual) and implemented in open code. These sources are Third-party here. The first table lists what the Push 3 unit was shown to match; the second lists claims that were not tested on the unit.

### What the unit matched or differed on

| Item | Push 2 specification says | Push 3 unit | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Vendor ID | 0x2982 | 0x2982: same | Measured | S01 | `R016:1`, `R064:42` |
| Product ID | 0x1967 | 0x1969: differs | Measured | S01 | `R115:18`, `R064:43` |
| Interface and endpoint | Claim interface 0; bulk OUT 0x01 | IF#0, bulk OUT 0x01: same | Measured | S01, S02 | `R016:1`, `R064:45-46` |
| Header | `FF CC AA 88` + 12 x `00` | Accepted: same | Reported | S01, S02 | `R115:20`, `R064:47` |
| XOR | `E7 F3 E7 FF` repeated | Accepted with the filler XORed | Reported | S01, S02 | `R115:21`, `R064:48` |
| Geometry | 960 x 160, 2048 bytes per line, 327,680 bytes per frame | Accepted: same | Reported | S01, S02 | `R115:19`, `R064:49-51` |
| Pixel | RGB565 little endian, blue in the top bits | Accepted | Reported | S01, S02 | `R115:38-40`, `R064:52` |
| Pixel, channel order on the glass | RGB565 little endian, blue in the top bits | Not checked on the glass | Not tested | — | `R115:38-40` |
| Timeout | 1000 ms | Used 1000 ms | Inferred | — | `R115:100-103`, `R064:54` |
| Timeout, other values | 1000 ms | No other value tried | Not tested | — | — |
| Frame rate | 60 fps | About 60 fps ceiling | Measured | S01, S02, S04 | `R016:26`, `R064:55` |
| No frame for 2 s | Black display | Dark after about 2 s (prose) | Reported | S01 | `R001:22`, `R064:55` |
| Guide screen after about 12 s | Not in the quoted specification lines | Seen on the Push 3 (prose) | Reported | S01, S08 | `R001:22`, `R011:155-157` |

### Claims not tested on the unit

| Claim | Source of the claim | Status on this unit | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| Host buffers of about 16 KB are recommended; the frame may be sent in smaller pieces | Push 2 specification | The script used 16,384-byte chunks; no other size was tried | Not tested | — | `R064:53` |
| One write of header plus 327,680 bytes; the filler is XORed | DrivenByMoss (DBM) | Not tried; a single large write was never sent. The XORed filler is what the script sent | Not tested | — | `R064:53,58` |
| The filler is left at zero and is not XORed | Ableton's JUCE reference code | Not tried; whether the unit tolerates raw filler is unknown | Not tested | — | `R064:58` |
| Push 3 display runs at 30 fps and takes 16,384-byte chunks; Push 2 takes 512-byte chunks at 15 fps | The P3D protocol notes, which are internally inconsistent (327,664 against 327,680 bytes) | Differs: 60 fps was reached on the unit. The 16,384-byte chunk is what was used | Third-party | — | `R064:273-275` |
| Up to 60 fps | The ofou Python port (OFOU) author, in an issue comment; no measurement in that repository | Agrees with the measured ceiling | Third-party | — | `R089:98` |
| Double buffering, and repeat of the previous frame if the next one is late | Push 2 specification | Not tested | Not tested | — | `R064:55` |
| Display protocol unchanged from Push 2 | A secondary source in the official catalogue; no Ableton document found that gives the Push 3 protocol | Consistent with the matches above, not proven by them | Official | — | `R018:198` |

## What `R016` contains

`R016` is the output of the Python display test script. It has HH:MM:SS stamps only (no dates, three separate days), no host name and no firmware. It records the lines below and nothing else.

| Lines | Session | Content |
|---|---|---|
| 1-4 | S01 | Open line; one static run of 1.0 s at 10 fps; "STOPPED sending"; "done (0.0s after stop)" |
| 5-24, 29-32 | S01 | Six static runs of 10 s at 10 fps; each followed by "done (15s after stop)" |
| 25-28, 33-34 | S01 | Three animation runs with frames, fps and average write time |
| 35-38 | S03 | One static run of 2.0 s at 10 fps; "done (0.0s after stop)" |
| 39-65 | S09 | Tilt-card runs: a PNG shown at 10 fps for 120-900 s each; some runs have no "done" line and the cause is not logged |

The log does not record when the screen went dark, when the guide screen appeared, what was on the screen, or any error. The file `R017` (S02) holds the Windows anim run and one static run on the same terms.

## Not yet verified

- Negative tests of the frame recipe on the unit: no XOR, filler left raw, header only, truncated frame, one large write, 512-byte chunks, other timeouts.
- Colour order on the glass: whether the red, green and blue bars appeared as red, green and blue.
- Exact stop timings: the 2 s and 12 s values are prose; no timestamped record, no Windows static run with a counting window.
- Send intervals other than 10 fps (static) and about 60 fps / 30 fps (animation): 120 Hz, 15 Hz and irregular intervals were not tried.
- The device-side limit on frame rate: whether 60 fps is a device or a host limit (the 16.6 ms against 11 ms write time difference was not isolated).
- Light-on-glass latency, display gamma, colourimetry and brightness curve: none was measured; display brightness `08` at 0 was never judged on the glass ([04-sysex.md](04-sysex.md#08-set-display-brightness)).
- The display on a unit in Standalone Mode, and with a second display client other than Live's helper.
- Push 2 specification items not tried on the unit: double buffering, repeat of the previous frame.

The device test that would close each item is in [08-gaps.md](08-gaps.md#display).

## Conflicting records

| Topic | Records | Resolution |
|---|---|---|
| Windows frame rate figure | The findings notes give "60.2 fps / 16.6 ms" for the Python script on Windows, and an earlier working summary found no raw log | A raw log exists outside the archive: `R017:2` (301 frames, 60.2 fps, 16.62 ms; S02). The archive's own `R016` holds only macOS runs and must not be quoted as a Windows result |
| 60 fps against less | Bare scripts and the UE spike reached 60 fps with 16.6 ms writes; the macOS screen mock could not hold 60 and stepped down to 50, 40 and 30, and at 30 it sometimes sent only 16-27 frames/s with writes of about 17-18 ms; the Windows mock ran at 30 with 11.3 ms writes; page-mode frames write in 11 ms against 16.6 ms for the bar pattern | Not resolved. Frame content, host stack and device pacing were not separated |
| Windows error code at unplug | `WinUsb_WritePipe` failed with Win32 22 in S06 and Win32 995 in S08 | Both are cable-pull observations in different runs of different programs on different days. The behaviour is the same (write fails, re-open every 1 s); the code differs. Not shown to depend on the device |
| Time to the guide screen | About 12 s after the last frame (macOS, S01) against about 10 s after release (Windows, S08) | Two rough prose observations on two systems, counted from different starting points (last content frame against the black frame at release) and with the interface in different states (still claimed by the S01 script during its counting window, by its code; released in S08). Recorded as "about 10-12 s"; not resolved |
| Push 3 frame rate | P3D claims 30 fps for Push 3; OFOU claims up to 60 fps; the unit sustained 60 fps | The measurement wins; P3D's figure is not a device limit |
