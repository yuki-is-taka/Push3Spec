# LEDs

This page explains how to light the pads, the buttons and the touch strip of the unit: colour through the palette, blinking and pulsing, global brightness, and what worked in which MIDI mode, on which port and on which OS. The SysEx bytes behind palette, brightness and strip control are in [04-sysex.md](04-sysex.md); the MIDI messages the controls send are in [02-controls.md](02-controls.md); mode switching is in [01-connection.md](01-connection.md). The page rests on S01 (palette dump, macOS), S02 (Windows palette dump), S03 (macOS output tests on the Live Port), S04 block d (Windows, User Port, through the UE5 test host), S07 (Windows, Learn LED), S09 block a (macOS, Learn LED on the User Port) and S11 (Windows brightness check). Firmware: the notes label the unit "1.5.4" for S01 and S03 only; the Device Inquiry reply (build 91) is identical in S01, S02, S03, S04 and S11; firmware is not logged for the other sessions.

Three caveats first.

1. Every statement about how an LED looked is an observation by the owner's eye (and one photograph). No light output was measured, so "visible", "darker" and "smooth" carry no numbers. Such statements are labelled `Reported`, including the owner's yes/no answers that a script stored in `R091`: those files hold the owner's judgement, not a measurement of the unit. Palette dumps, readbacks and transmitted bytes in logs are `Measured`.
2. The macOS tests drove the Live Port with the MIDI mode not queried (Live or Dual, because Live-port writes took effect). The Windows tests drove the User Port in User mode. No test ran the same scene on both, so a difference between the two columns of [What worked where](#what-worked-where) is not evidence of a difference in the unit.
3. The palette was not stable between sessions: two dumps taken 11 hours apart differ in 78 of 128 entries, and entry 100 changed several times, three of them with no logged writer. See [Conflicting records](#conflicting-records) before relying on any palette entry. MIDI channels in this page are written 1-16; a status byte is shown with its channel (`B0` = channel 1, `B9` = 10, `BD` = 14, `BE` = 15, `BF` = 16).

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Pad colour | Note On, channel 1, notes 36-99 (64 pads), velocity = palette index 0-127; velocity 0 turns the pad off | Reported | S03, S04 | `R092`, `R030:1916-1918` |
| Button colour | Control Change on channel 1; value = palette index on RGB buttons, value selects white level on white buttons; 0 = off | Reported | S03, S04, S07 | `R093`, `R026:108-139` |
| RGB buttons | 34 (CC numbers in [Buttons](#buttons)); the other 37 are white-only | Reported | S03 | `R007:96-102` |
| Palette | 128 entries of (r, g, b, w); r, g, b writable with `03` then `05`; w did not follow a write | Measured | S01, S03 | `R094`, `R020:11-13` |
| Palette stability | Not stable; entry 100 and 78 of 128 entries differ between two dumps | Measured | S01, S02, S03 | see [Conflicting records](#conflicting-records) |
| Blink | Two writes: steady value on channel 1, then value on channel 14, 15 or 16; the three channels blink at three speeds (14 fastest) | Reported | S07, S09 | `R026:146-147`, `R023:56,87` |
| Pulse | Mac only: channel 10 pulsed all buttons "slowly" (owner yes) | Reported | S03 | `R093` |
| Blink and pulse versus MIDI clock | Not tested; the records conflict | Not tested | — | see [Conflicting records](#conflicting-records) |
| LED brightness (`06`) | 0 darkens almost everything; sweeps 0-127 are visible; a single step to 20 was not visible on static LEDs | Reported | S03, S11 | `R092`, `R095` |
| White balance, PWM | Not tested on this unit; the Push 2 interface defines `14`, `15`, `23` (white balance) and `0B` (LED PWM) | Not tested | — | `R064:115,117` |
| Touch strip | Host-driven LEDs through `17` and `19`, or pitch bend; macOS Live Port only | Reported | S03 | `R093` |

## What worked where

"Worked" means the owner saw the expected result. "Notes only" means the claim is in the written notes and no raw log of the observation exists. The bytes of every row are in [04-sysex.md](04-sysex.md).

| What | Port, mode, OS, sender | Result | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| All pads and buttons at velocity or value 127; pad recolouring by palette writes | Live Port, Live or Dual mode (not queried), macOS, our output-test script | Worked (owner yes to "all lit"; yes to the palette sweep of entry 100 on pads and coloured buttons) | Reported | S03 | `R092`, `R095` |
| Same LED writes in User mode | User Port, User mode, macOS | Worked; the whole set (64 pads, buttons, strip) | Reported | S03 | `R001:153-156` (notes only) |
| LED writes on the Live Port while in User mode | Live Port, User mode, macOS | Ignored | Reported | S03 | `R001:156` (notes only) |
| Entering User mode | macOS | All LEDs go dark; the display is unaffected | Reported | S03 | `R001:152` (notes only) |
| 64-pad rainbow, `buttons 127`, `off`, 20 Hz chase | User Port, User mode, Windows, a spike in the UE5 test host, through its MIDI layer, libremidi and Windows MIDI Services | Worked; the transmitted bytes are in the log, the observation is in the notes | Reported | S04 | [1], [2] |
| Learn LED steady, dim levels, blink on three channels | User Port, User mode, Windows, python-rtmidi (WinMM) | Worked | Reported | S07 | `R026:80-156` |
| Learn LED steady, dim levels, blink on three channels | User Port, User mode, macOS, python-rtmidi (CoreMIDI) | Worked; same speed order as Windows | Reported | S09 | `R023:54-60` |
| 64 pad Note Ons | Live Port, Dual mode per notes, Windows, test script | Sent; the reaction is not logged | Measured | S02 | `R006:33` |
| 64 pad Note Ons, reaction | Live Port, Dual mode per notes, Windows, test script | The notes say the LEDs worked as on macOS (notes only) | Reported | S02 | `R001:126` |
| `06` LED brightness 0-100 % | User Port, User mode, Windows, the working mock | Worked (owner saw the change); no readback | Reported | S11 | [3], [4] |
| Palette writes `03`, `05` | User Port, User mode, macOS | Worked (notes only) | Reported | S03 | `R001:155` |
| Palette writes `03`, `05` | Windows, any port | Not tested; no write appears in any Windows log | Not tested | — | `R019:14-18` (reads only) |
| Touch strip LEDs (`17`, `19`, pitch bend) | Live Port, Live or Dual mode, macOS | Worked | Reported | S03 | `R093` |
| Touch strip LEDs | User Port, User mode, macOS | Worked (notes only) | Reported | S03 | `R001:154` |
| Touch strip LEDs | Windows, any port | Not tested | Not tested | — | — |
| Blink or pulse on a pad | Any | Not tested | Not tested | — | `R093`, `R026:80-156` |

Sources for this table:

1. `R030:1916-1918,2175-2177,2278-2280,2329-2396`
2. `R001:210-223`
3. `R063:1465-1475`
4. `R096:271`

## Pads

A pad is lit by a Note On. The pads are notes 36 to 99; note 36 is the bottom-left pad and the notes run in rows of eight (the Windows rainbow sends one palette index per diagonal "bottom-left to top-right"). The input side of the same notes is in [02-controls.md](02-controls.md).

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Message | `90 nn vv`: Note On, channel 1, `nn` = 36-99, `vv` = palette index 0-127 | Reported | S03, S04 | `R097:55-58`, `R030:1916-1918` |
| Off | `vv` = 0. Windows through Windows MIDI Services: 64 pads at velocity 0 and 76 button CCs at 0 left every pad and button dark | Reported | S04 | `R030:2278-2280`, `R001:221` (notes only) |
| Note Off (`80`) | Not tested; no test used Note Off, velocity 0 is the tested off | Not tested | — | `R097:55-58` |
| Rainbow example (Windows), bytes | Notes 36-99, one index per diagonal: 127 3 38 8 9 10 126 46 16 17 21 22 23 26 25. Message start: `90 24 7F 90 25 03 90 26 26 90 27 08` | Measured | S04 | `R030:1916-1918` |
| Rainbow example (Windows), observed | The owner saw the expected diagonal colour bands | Reported | S04 | `R001:212` |
| Chase, timing | A 4-pad snake at 20 Hz measured 64 steps per 3.20 s on every lap, every send accepted | Measured | S04 | `R030:2329-3738` |
| Chase, observed | The owner saw smooth motion with no stalls | Reported | S04 | `R001:223` |
| Channel other than 1 | Not tested for pads | Not tested | — | — |
| Answer pads in the guided tests | The guided scripts lit notes 40, 41, 42, 43 with indices 122, 125, 126, 127 as the back, no, yes and quit pads (the scripts name them white, blue, green, red) and the owner answered by pressing them. No mismatch was recorded | Inferred | — | `R035:23,91-92`, `R097:7-9` |

## Buttons

A button is lit by a Control Change on channel 1: `B0 cc vv`. For an RGB-LED button `vv` is a palette index. For a white-LED button `vv` selects a brightness through the same table (see [White-LED brightness](#white-led-brightness)). `vv` = 0 turns either kind off.

Which buttons have an RGB LED was judged from a photograph of the unit with every button CC driven to 127 (value 127 is red in the palette and full brightness for a white LED), plus the owner's confirmation that everything that looked red was red and that the pinkish look was the photo's colour cast.

| Type | Buttons and CC numbers | Count | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| RGB | Upper display row 102-109; lower display row 20-27; Master 28; scene column 36-43 (1/4 to 1/32t); Lock 83; Stop Clip 29; Mute 60; Solo 61; Automate 89; Capture 65; Record 86; Play 85; D-pad centre 91 | 34 | Reported | S03 | `R007:96-102`, `R097:113` |
| White | Sets 80; Setup 30; Learn 81; User 59; Undo 119; Save 82; Tap Tempo 3; Metronome 9; Quantize 116; Fixed Length 90; New 92; Device 110; Mix 112; Clip 113; Session (screen) 34; Add 32; Swap 33; D-pad arrows 44, 45, 46, 47; Note 50; Session (pad mode) 51; Scale 58; Layout 31; Repeat 56; Accent 57; Double Loop 117; Duplicate 88; Convert 35; Delete 118; Octave 54, 55; Page 62, 63; Shift 49; Select 48 | 37 | Reported | S03 | `R007:101` |
| Addressed but not classed | CC 15, 93, 94, 95, 111 (encoder and jog presses and tilts). The tests wrote 76 CCs; HW lists 71 buttons with LEDs; no record says whether these five have an LED | 5 | Inferred | — | `R093` (`button_ccs`), `R007:100-101` |

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Value 127 on an RGB button | Red | Reported | S03 | `R007:102` |
| Value 125 on an RGB button | Blue; confirmed on Lock, Stop Clip and the D-pad centre only | Reported | S03 | `R092` |
| Value 127 on a white button | Full white | Reported | S03 | `R007:98-102` |
| Windows, `buttons 127` | 76 CCs at 127: RGB buttons red, white buttons white; the pad rainbow stayed intact | Reported | S04 | `R030:2175-2177`, `R001:220` (notes only) |
| All buttons off | `B0 cc 00` for every addressed CC; owner yes | Reported | S03 | `R093` |
| Dim steady levels (white Learn button, User Port) | Values 127, 64, 32, 16, 8 were all visibly lit; 64 was only slightly darker than 127; the fall-off is steep at the low end (the owner thought gamma-like). Values 1-7 were not tested. macOS reproduced it: six levels distinguishable, the 0 step off | Reported | S07, S09 | `R026:143-145`, `R023:55` |
| White button at value 48 against 127 (Live Port, macOS) | The owner did not see it become darker | Reported | S03 | `R098` |
| Smooth host fades | A white button swept 0 to 127 and back, palette entry 100 swept 0 to 255 in red, and global brightness swept 0 to 127 without and with re-sending every LED value looked smooth (owner yes, four scenes) | Reported | S03 | `R095` |
| Perceived curve | Strong gamma by any method: the top half barely changes, then a sudden fall; steps visible in the dark end | Reported | S03 | `R001:67` (notes only) |

### White-LED brightness

How a white button's value maps to light is Inferred, not measured directly. The palette read from the unit has a fourth component `w` for every entry, and the white buttons follow it: `w` equals 2 x index for indices 0-40, then rises more slowly (48 -> 84, 64 -> 93, 96 -> 110, 127 -> 127). The curve is monotonic across all 128 entries. This is consistent in direction with the owner results above (127 against 64 slightly different, 127 against 48 not different), but no test wrote `w` to see the effect, and a written `w` was not stored (see [Palette](#palette)). The two owner observations used different methods in different sessions (S07: the Learn button alternating between 127 and 64 on the User Port on Windows; S03: a single step from 127 to 48 on the Live Port on macOS) and are not reconciled with each other.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| `w` against index in the dump | 0 -> 0, 8 -> 16, 20 -> 40, 40 -> 80, 41 -> 80, 48 -> 84, 64 -> 93, 96 -> 110, 100 -> 113, 127 -> 127; non-decreasing over 128 entries | Measured | S01 | `R094` |
| White buttons use the `w` column | Consistent with the owner results; never isolated | Inferred | — | `R098`, `R026:143-145` |

## Palette

The palette has 128 entries of (r, g, b, w), each component 0-255. A pad velocity or an RGB button value selects an entry; entry 0 is black. Ableton Live wrote all 128 entries in the one Live start whose traffic was captured (S03, 11:05), and entry 100 took Live's value at both Live starts in the hold runs (15:07, 15:53), so a palette written before Live starts does not survive Live's start. The read, write and reapply bytes are in [04-sysex.md](04-sysex.md#palette).

| Entry | S01 dump (macOS, 2026-10-01 23:16) | S02 dump (Windows, 2026-10-02 10:22) | Note |
|---|---|---|---|
| 0 | (0, 0, 0, 0) | (0, 0, 0, 0) | Off |
| 1 | (255, 64, 50, 2) | (242, 22, 0, 2) | Differs between dumps |
| 2 | (128, 4, 0, 4) | (255, 64, 50, 4) | Differs between dumps |
| 100 | (3, 6, 33, 113) | (3, 1, 29, 113) | Differs; see the timeline below |
| 120 | (255, 255, 255, 124) | (255, 255, 255, 124) | White; the UE5 test host's walk saw 120 and 124 behave as white and dim after a cold power-on (prose) |
| 122 | (204, 204, 204, 125) | (204, 204, 204, 125) | Light grey |
| 124 | (20, 20, 20, 126) | (20, 20, 20, 126) | Dim |
| 125 | (0, 0, 255, 126) | (0, 0, 255, 126) | Blue (confirmed on three buttons) |
| 126 | (0, 255, 0, 127) | (0, 255, 0, 127) | Green |
| 127 | (255, 0, 0, 127) | (255, 0, 0, 128) | Red (confirmed on buttons); only `w` differs |

Evidence for the table: Measured; sessions S01 and S02; sources `R094` and `R005:6-133` (S01), `R006:36-163` (S02, full dump; the first Windows dump at 10:12:52, lines 5-32, lists entries 0-27 and agrees). The test-host observation in the note of entry 120 is Reported (S11, prose): `R099:321-322`.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Write | `03` then `05`; recolouring shows at once without re-sending LED values (a sweep that wrote only `03`/`05` each step faded visibly) | Reported | S03 | `R095`, `R097:66-71,142-145` |
| Red, green, blue taken | Entry 100 written (255, 0, 255, 0) read back (255, 0, 255, ...) in five of five hold runs (in the 15:53 run the entry already held that colour before the write) | Measured | S03 | `R020:11-13` |
| White component not taken | The same write read back `w` = 113 each time (it had been 113; it was written as 0). Whether the unit ignores `w`, derives it from the index, or needs `05` first is unknown | Measured | S03 | `R020:13`, `R031:13` |
| Live rewrites all 128 at start | In Live's start burst: 128 x `03` then `05`; entry 100 changed from our magenta to Live's value 155 s into a hold run, when Live started | Measured | S03 | `R046:34-162`, `R020:207` |
| Live's write equals the S01 dump | Compared entry by entry: identical in all 128 entries | Measured | S01, S03 | `R094`, `R046:34-161` |
| Persistence, as the notes state | The notes say the palette survives power cycles (notes only) | Reported | S03 | `R001:71,128` |
| Persistence over a power cycle | Not tested: no log of a power cycle with a palette read exists, and the dumps in [Conflicting records](#conflicting-records) point the other way or at least do not support it | Not tested | — | — |
| Third-party use | DrivenByMoss reads all 128 entries at start, writes differences back, and places DAW colours at 70-96 | Third-party | — | `R064:109` |

## Blink and pulse

An LED animates when a second value is written on a different MIDI channel: a steady value on channel 1 (`B0 cc A`) sets the base look, then `Bn cc B` on channel `n + 1` starts the animation between the two. For buttons the status byte is `B` plus the 0-based channel; for pads the same applies to Note On (`9n`), which was never tested.

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Blink write | `B0 51 00` then `BE 51 7F` (Learn LED, dark and 127): the LED blinks between dark and on. Both messages were sent in the same millisecond | Reported | S07 | `R026:84-106,119-139` |
| Blink channels and speed | `BD` (channel 14) fastest, `BE` (15) medium, `BF` (16) slowest; all three blink. Only the order was observed, not the periods | Reported | S07, S09 | `R026:146-147`, `R023:56,87` |
| Blink on all buttons | `buttons(127)` on channel 1, then `buttons(0)` on `BE`: the owner answered yes to "blinking", with no clock and with a 120 BPM clock | Reported | S03 | `R093`, `R097:205-215` |
| Pulse | `buttons(127)` on channel 1, then `buttons(0)` on `B9` (channel 10), with a 120 BPM clock running: the owner answered yes to "slowly brightening and dimming" | Reported | S03 | `R093`, `R097:81-93,209-214` |
| Pulse on Windows, and pulse without a MIDI clock | Not tried | Not tested | — | — |
| Clearing | A 0 on the steady channel (`B0 51 00`) or on the animation channel (`BE 51 00`) stops the blink | Reported | S07, S09 | `R026:148-149`, `R023:57-58,89` |
| Switching | A nonzero write on the other channel does not switch the mode: blink then `B0 51 7F` keeps blinking; steady then `BE 51 7F` stays steady. Go through 0 first | Reported | S07, S09 | `R026:150-152`, `R023:57-58,89` |
| Back-to-back pair from a lit LED | `B0 51 00` then `BE 51 7F` (and the reverse), 0.26-0.59 ms apart, worked on macOS with no inserted frame. The pass rests on a judgement recorded in the evidence notes, not on a measurement: the owner could not tell the cycle boundaries | Reported | S09 | `R023:59,66-82,91-93` |
| Back-to-back pair from a lit LED, Windows | Not observed on Windows | Not tested | — | `R026:156-159` |
| Mode re-send | `0A 01` re-sent during a blink is reported back and does not reset the LED | Reported | S07, S09 | `R026:153-155`, `R023:60,94-95` |
| One-shot channels, pulse other than channel 10, blink or pulse on pads, rates, RGB blink between two non-dark colours | Not tested | Not tested | — | — |
| Animation channel table | Push 2 interface as quoted by the desk research (status low nibble, 0-based): 1-5 one-shot, 6-10 pulse, 11-15 blink. The measured pulse on nibble 9 and blink on 13, 14, 15 agree with it. The community Push 3 notes (P3D) give 1-6 one-shot, 7-12 blink, 13-15 pulse, which the measurement contradicts | Third-party | — | `R064:276` |

Clock synchronisation is open. The notes state that animations run at 120 BPM without a MIDI clock and are synchronised when a clock is sent (`R001:64`). The only clock test asked whether the buttons were blinking and the owner said yes both without and with a clock; it did not compare rates. The Windows record says plainly that whether a received clock changes the blink is not tested. No source or log supports the notes' sentence.

## Brightness

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Message | `F0 00 21 1D 01 01 06 v F7`, `v` = 0-127; one global value, observed on pads and buttons together (the strip was not checked) | Reported | S03, S11 | the Python output-test script, `R063:1465-1475` |
| 0 and 127 | `06 00` leaves almost nothing or nothing lit (owner yes to "all or nearly all dark"); `06 7F` restores | Reported | S03 | `R092` |
| Step to 20 | A single step from 127 to 20 was not visibly darker on static LEDs, with `05` reapplied (owner no) or with every LED value re-sent (owner no) | Reported | S03 | `R092`, `R093` |
| Sweeps | 0-127 sweeps were visible, with and without re-sending LED values (owner yes) | Reported | S03 | `R095` |
| Windows, messages sent | 94 messages sweeping 0-100 % (`v = round(percent x 127 / 100)`) | Measured | S11 | `R063:1465-2578` |
| Windows, observed | The owner saw the change | Reported | S11 | `R096:271` |
| Carry-over into Live mode | The value set in User mode persisted when Live took over after the User button; Live did not re-send it. One device-wide setting. Prose only | Reported | S11 | [1], [2] |
| Cold power-on on Windows | After a cold power-on without Live, the first display frame appeared at the unit's own brightness before the dimming to the saved 60 % display and 40 % LEDs. Prose only | Reported | S11 | `R099:318-320` |
| Readback | `07` answered on the macOS Live Port with 127 only. The details are in [04-sysex.md](04-sysex.md#readback-status) | Measured | S03 | `R093` |
| Readback in User mode or on Windows | `07` was not tried in User mode or on Windows | Not tested | — | — |
| USB-power limit (Push 2) | The Push 2 interface limits LED brightness to 8 on USB power alone | Third-party | — | `R064:116` |
| Power state of this unit | On its power adapter for the macOS sessions of 2026-10-01/02 (S01, S03, per the notes' header) and on its own power while unplugged in S06; the power state is not logged for the other sessions (notes only) | Reported | S01, S03, S06 | `R001:4,252` |
| USB-power limit on this unit | Untested on Push 3 | Not tested | — | — |
| Official settings | "Display Light" and "LED Brightness" are user settings, default 100 % | Official | — | `R018:75-76,147` |

Sources for this table:

1. `R099:329-334`
2. `R100:258`

Display brightness (`08`) is a separate control; its bytes are in [04-sysex.md](04-sysex.md#08-set-display-brightness) and the display itself in [05-display.md](05-display.md).

## Touch strip

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Host control by SysEx | `17` with `flags OR 03` makes the host drive the strip LEDs; `19` then sets 31 levels of 0-7; every fourth LED at 7, and a bottom-to-top gradient, both looked right (owner yes) | Reported | S03 | `R093`, `R097:223-229` |
| Host control by pitch bend | With flags `(f OR 01) AND NOT 02`, `E0 lo hi` swept 0 to 16256 in steps of 128 every 10 ms moved the LED dot from bottom to top (owner yes) | Reported | S03 | `R097:230-233`, `R093` |
| Restore, readback | `17` written back to 0x68 and read back as 104 | Measured | S03 | `R093` |
| Restore, behaviour | The strip followed the finger as at the start (owner yes) | Reported | S03 | `R093` |
| Centre dot | Stays lit through "all off"; no note or CC addresses it (power-on default). Notes only | Reported | S04 | `R001:222` |
| Windows, User Port | Not tested | Not tested | — | — |

## Not yet verified

- Palette writes (`03`, `05`) on the User Port and on Windows; whether `05` is needed; what `w` does.
- Why entry 100, and most of the palette, differ between dumps with no logged writer; whether the palette survives a power cycle.
- Blink and pulse on pads, one-shot channels, pulse on Windows and other channels, blink between two non-dark colours, the periods per channel, and the effect of a MIDI clock.
- LED brightness: readback in User mode, light output against value, the USB-power limit.
- The strip on Windows and in User mode, and the individual bits of the `17` flags.
- Whether CC 15, 93, 94, 95 and 111 have an LED.
- White balance (`14`, `15`, `23`) and LED PWM (`0B`).
- Why the owner saw 64 slightly darker than 127 in S07 but no change at 48 in S03 (different methods and sessions; not reconciled with each other or with the `w` curve).
- Back-to-back blink transitions from a lit LED on Windows, and pulse on Windows or without a MIDI clock.
- Standalone Mode (never used in any session).

Full list with the device test that would close each: [08-gaps.md](08-gaps.md#leds).

## Conflicting records

### Palette entry 100 and the two dumps

The notes explain a change of entry 100 by "Live rewrites the palette at every start" and say the palette survives power cycles (`R001:71,128-129`). The raw records do not support that explanation.

| When (JST) | Host | Entry 100 | Context | Evidence | Sessions | Source |
|---|---|---|---|---|---|---|
| 2026-10-01 23:16:28 | macOS | (3, 6, 33, 113) | Dump of all 128; Live not running | Measured | S01 | `R005:106` |
| 2026-10-02 07:44 | macOS | (3, 1, 29, 113) | Read at the start of the dim test; Live not running | Measured | S03 | `R098` |
| 2026-10-02 10:22:48 | Windows | (3, 1, 29, 113) | Dump of all 128; Live not running on this host | Measured | S02 | `R006:136` |
| 2026-10-02 11:05:15 | macOS | (3, 6, 33, 113) | Written by Live in its start burst | Measured | S03 | `R046:134` |
| 15:02:49 and 15:05:05 | macOS | (3, 1, 29, 113) | Baseline of two hold runs, Live not started | Measured | S03 | `R101:11`, `R020:11` |
| 15:07:41 | macOS | (3, 6, 33, 113) | After Live's start (our magenta overwritten) | Measured | S03 | `R020:207` |
| 15:18:45 | macOS | (255, 0, 255, 113), our magenta from 15:05 | Changed back 47 s after the first `0A 01` of that run; the script logs no palette write. Live was running, and the hold log records only what the unit sent, not Live's output | Measured | S03 | `R020:3488-3492,3550` |
| 15:24:46 | macOS | restored to (3, 1, 29, 113), verified | End of run | Measured | S03 | `R020:4260` |
| 15:53:21 | macOS | (255, 0, 255, 113) | Baseline of the next run: the magenta is back although none of our tools wrote it between 15:24:46 and 15:53:21 (Live ran in that interval; its traffic was captured only from 15:43 to 15:46 and holds no palette write) | Measured | S03 | `R041:11` |
| 15:53:55 | macOS | (3, 6, 33, 113) | After Live's start | Measured | S03 | `R041:63` |
| 15:56:01 | macOS | restored to (255, 0, 255, 113) | End of run | Measured | S03 | `R041:589` |
| 18:36:03 and 20:00:21 | macOS | (3, 1, 29, 113) | Baselines of two hold runs, Live not started | Measured | S03 | `R031:11`, `R102:11` |

What this shows. Live's 128-entry write equals the S01 dump in every entry, so (3, 6, 33) is Live's value and Live's start does explain it. It does not explain (3, 1, 29): that value appears in every reading taken with Live not running from 07:44 on, on both hosts, except the 15:53:21 baseline (magenta). Live also ran on the Mac from about 07:53 to at least 08:12 (`R004`, `R103`), after the 07:44 reading and before the S02 dump; its start traffic was not captured. The S02 dump (Windows, 10:22) differs from the S01 dump in 78 of 128 entries (indices 1-26, 65-69, 71-116 and the `w` of 127); the S01 dump is Live's palette, so the S02 dump is some other state of the unit. The notes' attribution of the Windows difference to Live (`R001:129`) is contradicted: Live's value is the S01 one. Nothing in the records explains how the unit went from Live's palette at 23:16 to the other one by 07:44, or why our magenta reappeared at 15:18:45 and 15:53:21. A palette that depends on events (power, USB re-plug, a mode change) is a possibility that no test separated. The comparison was made on 2026-10-15 from `R094`, `R006:36-163` and `R046:34-161`; it can be repeated.

### Other conflicts

| Topic | Records | Which wins and why |
|---|---|---|
| Blink and pulse clock sync | `R001:64` says clock sync works; `R026:147` says it was not tested | The Windows note: the only clock test does not compare rates and the notes' sentence has no log. Unresolved as a fact; the page records "not tested" |
| `07` readable | `R100:258` says the unit cannot report LED brightness; `R001:66` lists `07` | Both stand for their scope: a reply exists on the macOS Live Port (127 only); User mode on Windows was never tried. Unresolved |
| White-LED curve | `R001:65` says values 0-32 are doubled | The palette dump shows `w` = 2 x index up to index 40. The dump wins |
| Number of LED buttons | `R001:154` says 76 LED buttons; `R007:100-101` lists 71 | 76 is the count of CCs the tests sent; the five extra are CC 15, 93, 94, 95, 111 (see [Buttons](#buttons)). Resolved |
| Animation channel tables | Push 2 table (as quoted) against the community P3D table | The measurement agrees with the Push 2 table; the P3D table is contradicted. The Push 2 spec text was not re-read for this comparison |
| Velocity-0 Note On over Windows MIDI Services | `R052:228-232` predicts that unfixed libremidi, which sends MIDI 2.0 (UMP MT4), makes the service deliver velocity 1 and light palette colour 1 | Not a direct conflict. The S04 test used the fixed libremidi path (MIDI 1.0 in UMP, MT2; `R001:211,218`), and on it the owner saw every pad and button dark. The MT4 path the audit describes was not tried on the unit; the audit marks its claim as not observed on hardware |
