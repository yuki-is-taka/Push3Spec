# SysEx

This page is the byte-level reference for every System Exclusive message that was sent to, or received from, the Push 3 unit in the test sessions. It rests on S01, S03 and S09 (macOS, CoreMIDI, Live Port), S02, S04, S06, S07, S08, S11 (Windows, WinMM names served by Windows MIDI Services, Live and User ports) and, for the commands that only Ableton Live sends, on the MIDI Monitor captures of S03. Session definitions are in [09-sessions.md](09-sessions.md); record-ID citations are explained in [10-sources.md](10-sources.md). Hex bytes are space-separated, every message is shown with its `F0` and `F7`, and lengths count those two bytes. Firmware: the notes label the unit "1.5.4" for S01 and S03 only, and the Device Inquiry reply (build 91) is identical in S01, S02, S03, S04 and S11 (raw bytes logged in S03, S04 and S11; S01 and S02 logged only the decoded fields); firmware is not logged for the other sessions.

Four things a reader must know first.

1. Most commands were verified in one place only: the macOS Live Port in S03, with the MIDI mode not queried (Live or Dual, because Live-port writes took effect). Windows evidence on the User Port in User mode exists for `0A`, Device Inquiry, `1E`/`1F`, `06`, `08`, `27`, `37` and `38`; it does not exist for any other command (among them `03`, `05`, `07`, `09`, `17`, `18`, `19`, `21`, `26` and `43`).
2. Entries labelled "sent by Ableton Live" come from MIDI Monitor captures of Live's own traffic (S03, macOS, Live 12.4.5). The unit was not driven by a test tool for those bytes, so they show what Live does, not what the unit was shown to accept from a third party.
3. A write was shown to read back only for a few commands; the exact claim is in [Readback status](#readback-status).
4. The meaning of several commands is unknown (`28`, `33`, `39`, `3A`, `42`, most `37`/`38` ids, the tail of the Device Inquiry reply). They are listed with their bytes and marked as unexplained.

## At a glance

| Item | Value | Evidence | Sessions | Source |
|---|---|---|---|---|
| Header | `F0 00 21 1D 01 01 <command> <arguments> F7`; accepted on the Live Port and on the User Port | Measured | S03, S04, S11 | [1], [2], [3] |
| Commands documented below | 26 commands, counted by command byte (the universal Device Inquiry plus 25 with the Ableton header; `26 07` and `26 09` are counted separately). Where a request and its read form belong together they share one entry | Inferred | — | this page |
| Reply routing | Windows: a reply returns only on the port that asked; the External Port never answers; the `0A` report is the exception (both Live and User Port) | Measured | S04, S11 | [4], [5] |
| Verified write-then-read | Input type (`37 1A`) and input gain (`37 02`): 18 writes, 18 matching `38` reads, User Port, User mode, Windows | Measured | S11 | `R063:2777-2986` |
| LED brightness `06` and display brightness `08`, readback | No `07` or `09` request in any test on the User Port | Not tested | — | `R063:247-2578` (transmit lines) |
| LED brightness `06` and display brightness `08`, effect | Visual check by the owner only | Reported | S11 | `R096:271` |
| `07` / `09` readback | Replies received on the macOS Live Port; only the values 127 and 255 were ever returned | Measured | S03 | `R093`, `R097:243` |
| Device Inquiry | 26-byte reply, family 0x1969, member 3, sw 1.0 build 91, identical on every read | Measured | S01, S02, S03, S04, S11 | [6], [7], [8], [9], [10] |

Sources for this table:

1. `R046:1-336`
2. `R019:19-25`
3. `R009:3-28`
4. `R019:9-25`
5. `R009:3-28`
6. `R005:4`
7. `R006:3`
8. `R009:8`
9. `R020:200` (S03)
10. `R019:9-13` (S04)

## Conventions

| Item | Convention | Evidence | Sessions | Source |
|---|---|---|---|---|
| Header bytes | `00 21 1D` is Ableton's manufacturer ID; `01` device ID and `01` model ID are the Push 2 values. No other value of the last two bytes was ever sent | Third-party | — | `R064:91` |
| Data bytes | All bytes between header and `F7` are 7-bit (`00`-`7F`) | Measured | S03, S11 | `R046:1-336` |
| 14-bit value | `lsb = v AND 7F`, `msb = v >> 7`; value = `lsb + 128 x msb` (for example 1000 = `68 07`) | Measured | S03 | `R046:169` |
| 8-bit value as two bytes | 255 = `7F 01` (used for palette components and display brightness) | Measured | S03 | `R046:2,35`, `R093` |
| Ports | "Live Port" and "User Port" are the CoreMIDI names; the Windows WinMM names are in [01-connection.md](01-connection.md). Cable 1 = Live, 2 = User, 3 = External | Measured | S01, S04 | `R005:1-2`, `R019:2-7` |
| Mode column | "Live or Dual" means the MIDI mode was not queried and Live-port writes took effect, which they do not in User mode | Inferred | — | `R001:156` |

## Readback status

The claims, exactly as the records support them.

- **Input type and gain: 18 writes, 18 matching reads.** In the device check of 2026-10-08 the working mock sent 18 `37` writes (2 on id `1A`: values `01` then `00`; 16 on id `02`: `27 26 25 24 23 22 21 20 1F 1D 1C 1E 20 23 26 28`: `1E` (+5.0 dB) was skipped on the descending pass and written on the way back) and followed each with a `38` read. All 18 reads equalled the written value (log tag `DEVICE OK`, 18 occurrences, no mismatch). All traffic ran on `MIDIOUT2`/`MIDIIN2` (the User Port) in User mode on Windows, through python-rtmidi/WinMM, not through the test host's MIDI layer. The check never wrote IN 2 (`37 1B`, `37 03`).
- **LED brightness (`06`) and display brightness (`08`): no readback.** The same log holds 94 `06` and 126 `08` transmit lines and no `07` or `09` request. The only verification is that the owner saw the brightness change; that observation is written in a hand-over note, not in any log (`Reported`).
- **`07` and `09` on macOS.** `07` returned `07 7F` (127) and `09` returned `09 7F 01` (255) on the Live Port at the start and at the end of each of the four output-test runs (07:36 to 07:46, 2026-10-02). The end read followed a write of the same value, and no run ever read a different value, so these replies show that the commands answer, not that a changed value reads back. `07` and `09` were never sent in User mode, on the User Port or on Windows.
- **Palette `03` read by `04`.** In five hold runs a write of entry 100 = (255, 0, 255, 0) read back as (255, 0, 255, 113): red, green and blue took, the white component did not. In the 15:53 run the entry already held (255, 0, 255, 113) before the write, so that run does not show the write taking.
- **Pad mode `1E` read by `1F`.** On macOS `1F` was only read, and returned 2, the value Live had written. On Windows `1F` followed the mock's `1E 00` with one stale read and then matched (see the `1E` entry); the `1F` request itself is not logged (note under that entry).
- **Prose only (`Reported`).** Matching `38` replies in the UE5 test host's own acquisition runs and walk (S11 block c, through its MIDI layer and Windows MIDI Services, including IN 2) are recorded only in `R099:301-308,325-326,329-332` and `R100:256-260`; no raw log of them is in the archive.

## Entries

Each entry lists the request, the reply, the value encoding, the readback status, the ports and modes on which it was verified, and the evidence. "Verified on" always names the port, the MIDI mode (as far as known), the OS and the sender.

### Identity

#### Device Inquiry (universal SysEx)

| Field | Value |
|---|---|
| Purpose | Identify the unit |
| Request | `F0 7E 7F 06 01 F7` (6 bytes; no Ableton header) |
| Reply | 26 bytes: `F0 7E 01 06 02 00 21 1D 69 32 03 00 01 00 5B 00`, four serial bytes (redacted), `00 03 01 14 04 F7` |
| Value encoding | Decoded by the test script: manufacturer `00 21 1D`; family `69 32` = 0x1969 (7-bit pair, low byte first; equals the USB product ID); member `03 00` = 3; version `01 00 5B 00` = software 1.0, build 91. The nine bytes after the build bytes (the four serial bytes below, redacted, then `00 03 01 14 04`) are not explained; the script labels byte `03` "board rev 3", which no source confirms |
| Verified readback | Not applicable (a read). Byte-identical on every read whose raw bytes were logged (S03, S04, S11); S01 and S02 logged only the decoded fields, which agree |
| Verified on | macOS Live Port (S01, test script); Windows Live Port (S02, test script); Windows Live and User Ports through WinMM ground-truth client with Live closed (S04 block b); Windows User Port in User mode (S11 block b). External Port: no reply (1 Device Inquiry in the cited log; 14 Device Inquiries and 0 replies of any kind over the 43 merged probe logs, recounted 2026-10-15). Live's own request on the Live Port in S03 is answered with the same bytes |
| Evidence | Measured |
| Sessions | S01, S02, S03, S04, S11 |
| Source | [1], [2], [3], [4], [5], [6] |

Sources for this table:

1. `R005:4`
2. `R006:3`
3. `R020:200`
4. `R019:9-13`
5. `R009:7-9`
6. `R104:76-85`

| Field | Value |
|---|---|
| Possible serial number | Four of the Device Inquiry bytes (bytes redacted), read as 7-bit groups with the low group first, decode to the unit's USB serial (8 characters, serial redacted; the serial was read on macOS in S09). This is arithmetic, not a documented field |
| Evidence | Inferred |
| Sessions | — |
| Source | `R009:8`, `R002:9` |

A Push 2 reply has family `67 32` and member `02 00` and is three bytes shorter (Third-party, `R064:93`).

#### `42` request (sent by Ableton Live)

| Field | Value |
|---|---|
| Purpose | Unknown; Live sends it once at start-up, right after its Device Inquiry. The reply looks like an identity or version block |
| Request | `F0 00 21 1D 01 01 42 00 F7` (9 bytes) |
| Reply | 25 bytes: `F0 00 21 1D 01 01 42 00 04 01 01 00 14 00 01 00 00 0D 00 01 01 00 09 00 F7`; identical in two Live starts |
| Value encoding | Not decoded |
| Verified readback | Not applicable. The unit was never sent this command by a test tool |
| Verified on | macOS Live Port; the request is Live's, the reply was logged by the hold script because CoreMIDI delivers device input to every client |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R046:33`; `R020:208`; `R041:64` |

### MIDI mode

#### `0A` Set MIDI mode, with its report

| Field | Value |
|---|---|
| Purpose | Select which port carries the unit's non-SysEx output: Live Port only, User Port only, or both |
| Request | `F0 00 21 1D 01 01 0A m F7` (9 bytes) |
| Reply | The unit answers with the same 9 bytes (`0A m`) on both the Live Port and the User Port; the two reports arrive within the same millisecond of each other. A re-send of the current mode is answered the same way |
| Value encoding | `m` = `00` Live, `01` User, `02` Dual. In the Mac phase test, Play (CC 85) reached the Live Port only after `0A 00`, both ports after `0A 02`, the User Port only after `0A 01`; the User button (CC 59) reached both ports in Live and User modes in the S03 and S04 captures (Dual mode not tested for the button) |
| Verified readback | Report only. Every send was answered by a report; there is no read-only query (Inferred from the absence of any query in the records) |
| Verified on | macOS Live Port output, all three values (S03 block c). Windows Live Port output: `0A 02` (S04 block b, WinMM ground-truth client) and `0A 01` (S07). Windows User Port output: `0A 01` (S04 block b, S06 block a, S08, S11 blocks a and b) and `0A 02` (S06 block a at exit, S08 farewell, S11 block b at exit). macOS Live Port output `0A 01` (S09 block a). Sent on the External cable: no report (S04 block b) |
| Evidence | Measured |
| Sessions | S03, S04, S06, S07, S08, S09, S11 |
| Source | [1], [2], [3], [4], [5], [6], [7], [8], [9] |

Sources for this table:

1. `R008:1-26`
2. `R001:18,158` (User button on both ports)
3. `R010:17-18,168-169` (User button on both ports)
4. `R019:19-25`
5. `R022:9-11,577`
6. `R026:24,153`
7. `R023:26-34`
8. `R011:149-155`
9. `R009:3-6,25-28`

| Field | Value |
|---|---|
| Live's use of `0A` | Live sends `0A 00` once in its start-up burst (after the 128 palette writes), and `0A 01` or `0A 00` after a User button press (152 ms after the first logged press, 44 ms after the second). The mechanics belong to [01-connection.md](01-connection.md) |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R046:182`; `R010:150-152,170,639-640`; `R001:174-177` |

| Field | Value |
|---|---|
| Host-sent `0A 00` and Live | A `0A 00` sent by a test tool while Live sat backed off in User mode did not make Live resume (n = 1; the raw log was not kept; see [01-connection.md](01-connection.md#user-button-and-hand-back)) |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R001:170` |

| Field | Value |
|---|---|
| Power-on state | Dual after power-up; not stored. Notes only: no raw log of the power cycle (Mac, S03; Windows, S02) |
| Evidence | Reported |
| Sessions | S02, S03 |
| Source | `R001:97,127` |

| Field | Value |
|---|---|
| Effect of a mode re-send | A re-send of the current mode does not change the LEDs or the display (a blinking LED kept blinking). Owner observation |
| Evidence | Reported |
| Sessions | S07, S09 |
| Source | `R026:153-155`, `R023:60,94-95` |

### Palette

#### `03` Set palette entry

| Field | Value |
|---|---|
| Purpose | Define colour entry `idx` of the 128-entry palette used by pad velocities and button CC values (see [03-leds.md](03-leds.md)) |
| Request | `F0 00 21 1D 01 01 03 idx rL rH gL gH bL bH wL wH F7` (17 bytes) |
| Reply | None |
| Value encoding | Each of r, g, b, w is 0-255 as `lsb = v AND 7F`, `msb = v >> 7`. Example, Live's write of entry 1: `03 01 7F 01 40 00 32 00 02 00` = (255, 64, 50, 2) |
| Verified readback | Partly. Red, green and blue read back equal by `04`; the white component did not follow a write (wrote 0, read 113, five of five runs) |
| Verified on | macOS Live Port, Live or Dual mode, sender: our hold script (entry 100, five runs) and output tests (entry 100, restored to the original). macOS Live Port, sender Ableton Live (128 writes at start). Not verified on the User Port or on Windows: no `03` write appears in any Windows log |
| Evidence | Measured |
| Sessions | S03 |
| Source | [1], [2], [3], [4], [5], [6], [7] |

Sources for this table:

1. `R020:11-13`
2. `R101:11-13`
3. `R041:11-13`
4. `R031:11-13`
5. `R102:11-13`
6. `R098`
7. `R046:34-161` (sent by Ableton Live)

| Field | Value |
|---|---|
| Palette writes on the User Port (macOS) | Notes state that `03` and `05` take effect when sent on the User Port in User mode. Notes only: no raw log |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R001:155` |

#### `04` Get palette entry

| Field | Value |
|---|---|
| Purpose | Read one palette entry |
| Request | `F0 00 21 1D 01 01 04 idx F7` (9 bytes) |
| Reply | 17 bytes `F0 00 21 1D 01 01 04 idx rL rH gL gH bL bH wL wH F7`; example, entry 100: `04 64 03 00 01 00 1D 00 71 00` = (3, 1, 29, 113) |
| Value encoding | As `03` |
| Verified readback | Not applicable (a read) |
| Verified on | macOS Live Port: all 128 entries in about 3 s (S01), entry 100 every second for 20 minutes (S03 hold runs). Windows through python-rtmidi: all 128 entries (S02; the same log's LED line names the Live Port output `Ableton Push 3 MIDI`). Windows Live and User Ports: entry 0 = `04 00 00 00 00 00 00 00 00 00`, each answered on the port that asked (S04 block b) |
| Evidence | Measured |
| Sessions | S01, S02, S03, S04 |
| Source | [1], [2], [3], [4] |

Sources for this table:

1. `R005:5-133`
2. `R006:4-32,35-163`
3. `R020:11`
4. `R019:14-18`

#### `05` Reapply palette

| Field | Value |
|---|---|
| Purpose | Apply palette writes to lit LEDs (as in the Push 2 interface) |
| Request | `F0 00 21 1D 01 01 05 F7` (8 bytes) |
| Reply | None |
| Value encoding | No arguments |
| Verified readback | No (visual only). Whether a palette write needs `05` was never tested; every write in the tests was followed by `05` |
| Verified on | macOS Live Port, Live or Dual mode, sender: our output tests and hold script; sent by Ableton Live once after its 128 writes |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R097:66-71`; `R046:162` |

### Brightness

#### `06` Set LED brightness

| Field | Value |
|---|---|
| Purpose | Set the global brightness of pad and button LEDs |
| Request | `F0 00 21 1D 01 01 06 v F7` (9 bytes) |
| Reply | None |
| Value encoding | `v` = `00`-`7F`. The host software's Windows Setup page sends `v = round(percent x 127 / 100)`, for example 99 % = `7E`, 98 % = `7C` |
| Verified readback | No readback in any test of `06` on the User Port or on Windows |
| Verified on | macOS Live Port, Live or Dual mode, our output tests (effects in the table below). Windows User Port, User mode (S11 block b): 94 `06` messages sweeping 0-100 % (transmit lines in the device-check log). Sent by Ableton Live: `06 00`, then a ramp of 20 steps from `06 06` to `06 7F` at start; later a paired `06`/`08` fade down to 0 and a paired ramp back up |
| Evidence | Measured |
| Sessions | S03, S11 |
| Source | `R063:1465-1475` (example lines), `R046:1-22,200-241` |

| Field | Value |
|---|---|
| Observed effect (owner) | macOS Live Port, Live or Dual mode, our output tests: 0 darkens almost all LEDs, 127 restores, sweeps 0-127 visibly change, a single step to 20 was not visibly different from 127 on static LEDs. Windows User Port, User mode (S11 block b): the owner saw the change over the 0-100 % sweep |
| Evidence | Reported |
| Sessions | S03, S11 |
| Source | `R092`, `R095`, `R096:271` |

#### `07` Get LED brightness

| Field | Value |
|---|---|
| Purpose | Read the global LED brightness |
| Request | `F0 00 21 1D 01 01 07 F7` (8 bytes) |
| Reply | `F0 00 21 1D 01 01 07 v F7` (9 bytes) |
| Value encoding | As `06` |
| Verified readback | Reply received; value 127 every time (see [Readback status](#readback-status)) |
| Verified on | macOS Live Port, Live or Dual mode, our output-test script. Not sent in User mode, on the User Port or on Windows |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R093` (`orig.led`, `restored.led`); `R097:243-246` |

#### `08` Set display brightness

| Field | Value |
|---|---|
| Purpose | Set the display backlight |
| Request | `F0 00 21 1D 01 01 08 lo hi F7` (10 bytes) |
| Reply | None |
| Value encoding | 0-255 as `lo = v AND 7F`, `hi = v >> 7`; 255 = `7F 01`, 252 = `7C 01`, 0 = `00 00`. The host software's Windows Setup page sends `v = round(percent x 255 / 100)` |
| Verified readback | No readback in any test of `08` on the User Port or on Windows |
| Verified on | macOS Live Port, Live or Dual mode (effects in the table below). Windows User Port, User mode (S11 block b): 126 `08` messages sweeping 0-100 %, including two `08 00 00`. Brightness 0 was never judged on the glass. Sent by Ableton Live: `08 7F 01` at start and paired fades |
| Evidence | Measured |
| Sessions | S03, S11 |
| Source | `R063:247-250,435,1064`, `R046:2,200-241` |

| Field | Value |
|---|---|
| Observed effect (owner) | macOS Live Port, Live or Dual mode: 20 darker (owner yes), 255 brighter (owner yes). Windows User Port, User mode (S11 block b): the owner saw the change over the 0-100 % sweep |
| Evidence | Reported |
| Sessions | S03, S11 |
| Source | `R093`, `R096:271` |

#### `09` Get display brightness

| Field | Value |
|---|---|
| Purpose | Read the display backlight |
| Request | `F0 00 21 1D 01 01 09 F7` (8 bytes) |
| Reply | `F0 00 21 1D 01 01 09 lo hi F7` (10 bytes) |
| Value encoding | As `08` |
| Verified readback | Reply received; `7F 01` (255) every time (see [Readback status](#readback-status)) |
| Verified on | macOS Live Port, Live or Dual mode, our output-test script. Not sent in User mode, on the User Port or on Windows |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R093` (`orig.disp`, `restored.disp`) |

### Touch strip

#### `17` Set touch strip configuration and `18` Get

| Field | Value |
|---|---|
| Purpose | Choose whether the host or the unit drives the strip LEDs, and how (SysEx or pitch bend) |
| Request | `17`: `F0 00 21 1D 01 01 17 f F7` (9 bytes). `18`: `F0 00 21 1D 01 01 18 F7` (8 bytes) |
| Reply | `18` answers `F0 00 21 1D 01 01 18 f F7` (9 bytes). `17` has no reply |
| Value encoding | `f` is a 7-bit flag byte. Default read 0x68 (104). Live writes `17 78` at start. The output test set `f OR 03` (host drives the LEDs by SysEx) and `(f OR 01) AND NOT 02` (host drives by pitch bend), then restored 0x68. The bytes sent for the two test values are derived from the code and the value read, not logged. Other bits are undecoded |
| Verified readback | Restore only: after writing 0x68 back, `18` returned 104 in all four runs. The test values themselves were not read back |
| Verified on | macOS Live Port, Live or Dual mode, our output-test script. Not tested on Windows |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R093`, `R097:223-246,291-295`, `R046:23` |

| Field | Value |
|---|---|
| Observed effect and notes | The owner answered yes to `f OR 03` (strip LEDs driven by SysEx) and to `(f OR 01) AND NOT 02` (strip driven by pitch bend). Notes state `17` and `19` work on the User Port in User mode (macOS; no raw log) |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R093`, `R001:154` |

#### `19` Set touch strip LEDs

| Field | Value |
|---|---|
| Purpose | Set the 31 strip LEDs individually (when `17` selects SysEx control) |
| Request | `F0 00 21 1D 01 01 19` + 16 data bytes + `F7` (24 bytes) |
| Reply | None |
| Value encoding | 31 levels `v[0..30]` of 0-7, bottom LED first. Data byte `i` (0-14) = `((v[2i+1] << 3) OR v[2i]) AND 3F`; data byte 15 = `v[30] AND 07`. Two patterns were tried: every fourth LED at 7, and a bottom-to-top gradient `i x 7 / 30` (owner yes to both) |
| Verified readback | No (visual only). The cited records hold no log of the request bytes; they are taken from the script |
| Verified on | macOS Live Port, Live or Dual mode, our output-test script. Not tested on Windows. The strip's centre dot stays lit through an all-off of notes and CCs (Windows, notes only); whether `19` reaches it was not recorded |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R097:104-107,223-229`; `R093`; `R001:222` |

### Pad configuration and reads

#### `1E` Set pad pressure mode and `1F` Get

| Field | Value |
|---|---|
| Purpose | Select what the pads send as pressure |
| Request | `1E`: `F0 00 21 1D 01 01 1E m F7` (9 bytes). `1F`: `F0 00 21 1D 01 01 1F F7` (8 bytes) |
| Reply | `1F` answers `F0 00 21 1D 01 01 1F m F7` (9 bytes). `1E` has no reply of its own |
| Value encoding | `m` = `00` channel pressure (one shared pressure stream; note on channel 1), `01` poly pressure, `02` MPE (channels 2-16, channel pressure, CC 74, pitch bend). The resulting input forms are in [02-controls.md](02-controls.md). Mode `01` input was never tested |
| Verified readback | Partly. macOS: `1F` returned 2 after Live had left the unit in MPE. Windows User Port (S11 block a, first run): `1E 00` was followed within 0.3 ms by a `1F` reply of `02` (stale or late); in the three later runs `1F` returned `00` at once, but the earlier `1E 00` had already been sent, so the later reads do not show how quickly `1E` applies |
| Verified on | Windows User Port, User mode, sender: the working mock (S11). macOS Live Port: `1F` read only (S03 block c); `1E` writes by a test tool on macOS are not logged. Sent by Ableton Live: `1E 01, 00, 01, 02` twice as its Expression Mode control was cycled, and `1E 02` three times at start |
| Evidence | Measured |
| Sessions | S03, S11 |
| Source | [1], [2], [3], [4], [5] |

Sources for this table:

1. `R060:20-22`
2. `R063:22-23`
3. `R105` (`1F`)
4. `R106:65,130,195,260,325,390,455,520`
5. `R046:199,243-244`

Note. The working mock sends the `1F` request right after `1E` and does not log it (`R070:425-433`). The `1F` replies in the S11 logs therefore answer a request that the logs do not show: the request itself is Inferred, and the replies are not unprompted reports.

#### `26 07` Set pad parameter and `27` Get parameter

| Field | Value |
|---|---|
| Purpose | Write and read the pad and expression parameters (sensitivity curve inputs, glide, in-tune location and width, slide height) |
| Request | Write: `F0 00 21 1D 01 01 26 07 id lsb msb F7` (12 bytes). Read: `F0 00 21 1D 01 01 27 grp id F7` (10 bytes) |
| Reply | `F0 00 21 1D 01 01 27 grp id lsb msb F7` (12 bytes); undefined entries read `7F 7F` (16383). `26 07` has no reply |
| Value encoding | 14-bit, `lsb` first. Live's values: id `01` = `36 00` (54), `02` = `68 07` (1000), `05` = `2F 00` (47), `06` = `5C 03` (476), `0C` = `02 00`, `1A` = `01 00`, `0D` = `01 00`, `1B` = `4A 00`, `0E` = `01 00`, `1C` = `00 00`, `14` = `60 00`, `24` = `13 00`. Id `08` reads `02 00` in the scan but is not in Live's captured traffic. The names Threshold (`01`), Range (`02`), Compand (`05`), Drive (`06`) are inferred from the order of Live's UI operations; `0E` is In Tune Location, `14` In Tune Width, `24` Slide Height. Group `07` holds the pad parameters; group `08` is the velocity curve, group `09` the per-pad glide group (`27 09 pad` = group, 0-7 in the snapshot), group `04` 64 per-pad values of 313-497 in the 10:50 scan (meaning unknown; never write) |
| Verified readback | Reads: all 128 groups x 128 ids answered; defined values were found in groups `00`-`0A` and `7F` (669 values) and 58 `38` ids, 727 in all. The values of group `07` equal the values Live wrote. Writes by a test tool: none in a logged run (see the table below) |
| Verified on | macOS Live Port, mode not queried, reads by our scan script; Windows User Port, User mode: `27 07 01` answered `27 07 01 36 00` (S11 block b). Writes: sent by Ableton Live only, on the macOS Live Port. Not verified: `26` from a test tool on any port in a logged run |
| Evidence | Measured |
| Sessions | S03, S11 |
| Source | [1], [2], [3], [4], [5], [6], [7], [8] |

Sources for this table:

1. `R046:24-29,168-175,185-198`
2. `R107`
3. `R047:1-6`
4. `R108:1-131`
5. `R069`
6. `R105` (group `04`)
7. `R009:22-24`
8. `R001:44-57,103-106`

| Field | Value |
|---|---|
| Test-tool writes (notes only) | A Threshold write, read and restore, and DrivenByMoss-style writes `26 07 08 02 00` / `26 07 08 00 00` that switched glide on and off, are described in the notes. No raw log |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R001:44-57,103-106` |

#### `26 09` Set pad glide group

| Field | Value |
|---|---|
| Purpose | Assign each pad to a glide group, so that a slide across pads in one group continues one note |
| Request | `F0 00 21 1D 01 01 26 09 pad grp 00 F7` (12 bytes); `pad` = `00`-`3F` |
| Reply | None |
| Value encoding | With MPE and Note Pitch Bend active, `grp` = pad row (0-7); otherwise `grp` = pad index. Live sends 64 of them at start (`26 09 00 00 00` through `26 09 3F 07 00`) |
| Verified readback | By `27 09 pad`: the snapshot holds groups 0-7 in rows of eight. |
| Verified on | Sent by Ableton Live on the macOS Live Port; no test tool sent it in a logged run |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R046:245-308`, `R106:1-3`, `R069` (`27.09.xx`) |

| Field | Value |
|---|---|
| Effect on input | Bend continuing across a row was observed in S03 (notes) |
| Evidence | Reported |
| Sessions | S03 |
| Source | `R001:44-47` |

#### `43` Get velocity curve

| Field | Value |
|---|---|
| Purpose | Read the 128-entry velocity curve the unit computed from the sensitivity parameters |
| Request | `F0 00 21 1D 01 01 43 F7` (8 bytes; no arguments) |
| Reply | 136 bytes: header, `43`, 128 values, `F7`. Example from the 10:50 scan: 1, 1, 1, 1, 10, 18, 24, 28 ... |
| Value encoding | One byte per input value, 1-127, non-decreasing |
| Verified readback | Not applicable (a read). The same table is returned by `21 i` and by `27 08 i` for all 128 `i` (scan at 2026-10-02 10:50, equal) |
| Verified on | macOS Live Port, our scan script (S03 block c). Live sends `43` after most `26 07` writes (153 `43` requests against 168 `26 07` writes in the sensitivity capture). A `43` followed by 128 values as a write was never sent by any tool; it is a third-party claim only |
| Evidence | Measured |
| Sessions | S03 |
| Source | [1], [2], [3], [4] |

Sources for this table:

1. `R108:131`
2. `R105`
3. `R103:64-65`
4. `R107:1-12`

#### `21` Get velocity curve entry (Push 2 command)

| Field | Value |
|---|---|
| Purpose | Read entry `i` of the velocity curve |
| Request | `F0 00 21 1D 01 01 21 i F7` (9 bytes) |
| Reply | One value per request, equal to entry `i` of the `43` reply. |
| Value encoding | 7-bit value |
| Verified readback | Equal to `43` for all 128 entries |
| Verified on | macOS Live Port, our scan script only. The command comes from the Push 2 interface (Third-party) |
| Evidence | Measured |
| Sessions | S03 |
| Source | `R109:48`; `R108:131`; `R105` |

| Field | Value |
|---|---|
| Reply layout | The scan stores the third body byte, so the layout is taken to be `21 i v`; no raw reply line was kept |
| Evidence | Inferred |
| Sessions | — |
| Source | `R109:48` |

### Settings (audio and pedals)

#### `37` Set device setting and `38` Get settings

| Field | Value |
|---|---|
| Purpose | Write and read the audio and pedal settings that Live's Setup tabs expose |
| Request | Write: `F0 00 21 1D 01 01 37 id lsb msb` + four filler triples `00 00 00` + `F7` (23 bytes; up to five `id lsb msb` triples fit). Read: `F0 00 21 1D 01 01 38 id1 id2 id3 id4 id5 F7` (13 bytes, unused slots `00`) |
| Reply | `38` answers 23 bytes: `38` then five `id lsb msb` triples; an undefined id reads `7F 7F`. `37` has no reply |
| Value encoding | 14-bit, `lsb` first. `37 1A t` input type IN 1, `37 1B t` IN 2: `00` / `01` / `02` (levels in the table below). `37 02 g` gain IN 1, `37 03 g` IN 2: `g = (20 - dB) x 2`, so `28` = 0 dB, `27` = +0.5 dB, `1C` = +6.0 dB, `00` = +20 dB (scale in the table below). 58 ids are valid for `38`: `00`-`05`, `08`-`0D`, `0F`-`11`, `15`-`3F`. Rest values on 2026-10-08: `1A` = 0, `1B` = 0, `02` = `28`, `03` = `28` |
| Verified readback | Yes, for `37 1A` and `37 02` on the User Port: 18 writes, 18 matching reads (see [Readback status](#readback-status)). Not for IN 2 in that check |
| Verified on | Windows User Port, User mode, python-rtmidi/WinMM (S11 block b): writes and reads; reads of `1A`, `1B`, `02`, `03` answered on the User Port only (one reply each, none on the Live Port). macOS Live Port, mode not queried: reads of all 58 ids (S03). Sent by Ableton Live on the macOS Live Port: `37 1A/1B` (UI cycled `01 02 01 00`), `37 11` (`02 03 02 00`), `37 26` (see next table) |
| Evidence | Measured |
| Sessions | S03, S11 |
| Source | [1], [2], [3], [4] |

Sources for this table:

1. `R063:2777-2986`
2. `R009:10-21`
3. `R108:130`
4. `R110:1-12`

| Field | Value |
|---|---|
| Level measurements (prose only) | Measured with a stereo programme, relative to input type `00`: `01` = +6.0 dB and `02` = +25.7 dB, which fits Line, Instrument, High in the manual's order. The gain scale `g = (20 - dB) x 2` measured exact: +5.5, +6, +7 and +20 dB gave +5.5, +6.0, +7.0 and +20.0 dB |
| Evidence | Reported |
| Sessions | S11 |
| Source | `R099:311-317` |

Further `37` ids seen in Live's traffic only (sent by Ableton Live, macOS Live Port, S03):

| Id | Bytes | Observation | Evidence | Sessions | Source |
|---|---|---|---|---|---|
| `11` | `37 11 v` | Output routing; the UI cycled `02 03 02 00`; start value `00` | Measured | S03 | `R110:9-12`, `R046:180` |
| `26` | `37 26 v` | Pedal and CV selection; start value `50`; the UI produced `43`, `50`, `1C`, `50`. The meaning (`50` both footswitch, `43` CV1 and switch 2, `1C` switch 1 and CV2, `0F` both CV) is DrivenByMoss's, not measured. Pedal input as MIDI was never observed | Third-party | — | `R083:1-4`, `R064:137` |
| `04` and `05` | `37 04 75 00 05 75 00 ...` (two triples, both 117) | Written at start and re-written; read back 117 each. Meaning unknown | Measured | S03 | `R046:163,191`, `R069` (`38.04`, `38.05`) |
| `1C` and `1D` | `37 1C 75 00 1D 75 00 ...` | As above | Measured | S03 | `R046:164,192`, `R069` (`38.1C`, `38.1D`) |

### Seen only in Live's traffic (sent by Ableton Live)

These four commands were never sent by a test tool and no reply to them was ever logged. Their meaning is unknown. Sessions S03; macOS Live Port; Live 12.4.5; evidence Measured (bytes only).

| Command | Bytes seen | Count in the Live start capture | Reply seen | Source |
|---|---|---|---|---|
| `28` | `F0 00 21 1D 01 01 28 00 00 01 F7` (twice), `... 28 00 00 00 F7` (once) | 3 | None | `R046:30,183,309` |
| `33` | `F0 00 21 1D 01 01 33 00 00 01 F7` | 3 | None | `R046:31,184,310` |
| `39` | `F0 00 21 1D 01 01 39 02 F7` and `... 39 05 F7` | 2 | None | `R046:165-166` |
| `3A` | Request `F0 00 21 1D 01 01 3A F7`; reply `F0 00 21 1D 01 01 3A 07 00 F7` or `3A 27 00` | Polled about every 1.2 s while Live runs; 115 replies of `3A 07 00` (captures of 08:03-08:12) and 190 of `3A 27 00` (11:10-15:46) in the raw spy captures | Yes: 10 bytes. Our scan (10:50) sent `3A` twice (the script asks twice) and stored `3A 27 00` | `R103`, `R111`, `R105` (`3A`) |

Push 2's `28` is "pad settings"; the link to this `28` is Inferred and untested.

Live also polls `38` about every 0.3 s with `38 18 19 00 00 00` and `38 0D 3A 3B 3C 00`; the replies are 23 bytes of zeros apart from the echoed ids, and their meaning is unknown (Measured, S03, `R103:1-12` for the format; `R020:210-216` for the reply bytes).

## Measured traffic: what Ableton Live sends

Live was the sender of every message in this section. The captures are `R112`, taken with MIDI Monitor on macOS in S03; all lines are "To Ableton Push 3 Live Port". The unit was not driven by a test tool.

| Event | Messages, in order | Evidence | Sessions | Source |
|---|---|---|---|---|
| Live start (about 2.2 s, 336 host-to-unit SysEx lines) | `06 00`; `08 7F 01`; 20 steps of `06` rising from `06 06` to `06 7F` (6-7 per step, about 230 ms); `17 78`; six `26 07` (`0C 02`, `1A 01`, `0D 01`, `1B 4A`, `0E 01`, `1C 00`); `28 00 00 01`; `33 00 00 01`; Device Inquiry; `42 00`; 128 x `03` and `05`; `37 04`, `37 1C`; `39 02`, `39 05`; `43`; eight `26 07`; `37 1A`, `37 1B`, `37 02 28`, `37 03 28`, `37 11 00`, `37 26 50`; `0A 00`; `28 00 00 01`; `33 00 00 01`; six `26 07`; `37 04`, `37 1C`; six `26 07`; `1E 02`; paired `06`/`08` fade down to `06 00`, `08 00 00`; `43`; `1E 02` twice; 64 x `26 09`; `28 00 00 00`; `33 00 00 01`; final paired `06`/`08` ramp up, ending `06 7F`, `08 7F 01` | Measured | S03 | `R046:1-336` |
| Live quit | Six `26 07` messages (`0C 02`, `1A 01`, `0D 01`, `1B 4A`, `0E 01`, `1C 00`); no `0A`, `1E`, `06`, `08`, `17`, `37` and no palette write. Pad mode, palette and `37` values stay as Live left them | Measured | S03 | `R047:1-6` |
| Live MIDI-tab changes | No SysEx after the start burst | Measured | S03 | `R046:336`, `R113` |
| Live Expression tab | `1E 01`, `00`, `01`, `02` and `26 07` and `26 09` batches for each control | Measured | S03 | `R106:1-884` |
| Live Sensitivity tab | A `26 07 id lsb msb` followed by `43` for each step; the unit answers each `43` with the 136-byte curve | Measured | S03 | `R107:1-474` |
| Live Audio and Pedals tabs | `37 1A`, `37 1B`, `37 11`, `37 26` as listed above | Measured | S03 | `R110:1-12`, `R083:1-4` |
| Live's start in the hold runs | About 2.5-3 s after Live's helper appears, `0A 00`, then the same palette overwrite; the unit's `0A 00` report reaches both ports | Measured | S03 | `R020:196-210`, `R041:53-64` |

## Push 2 commands never sent to this unit

The Push 2 interface defines the commands below. Neither Live, nor DrivenByMoss's Push 3 code, nor any test in this archive sent them to the Push 3 unit, so their behaviour on Push 3 is unknown. They are not counted among the 26 entries.

| Command (Push 2) | Role in the Push 2 interface | Evidence | Sessions | Source |
|---|---|---|---|---|
| `14`, `15`, `23` | White balance, 11 colour groups, 0-1024 | Third-party | — | `R064:115` |
| `0B` | LED PWM frequency | Third-party | — | `R064:117` |
| `1B`, `20` | Aftertouch threshold; velocity curve in 16 x 8 values (a write form) | Third-party | — | `R064:141` |
| `30`, `31`, `32` | Pedal configuration (a different scheme from `37 26`) | Third-party | — | `R064:137` |
| `43` + 128 values | DrivenByMoss intends it as a curve write, but its code omits the `43` byte; a measured `43` with no arguments is a read trigger | Third-party | — | `R064:141,152`, `R103:64-65` |

## Not yet verified

- Palette write (`03`, `05`) on the User Port and on Windows, whether `05` is required, and what the white component does.
- Why the palette changes outside Live's writes (see Conflicting records).
- `07` and `09` in User mode and on Windows, with a value other than 127 and 255.
- `17`, `18`, `19` in User mode and on Windows, and the individual flag bits of `17`.
- `26`, `27` writes from a test tool on any port; `1E 01` input; `1E`-then-`1F` timing.
- `37` ids other than `1A`, `02` (written) and `1B`, `03` (reads): `11`, `26`, `04`, `05`, `1C`, `1D`; `38` ids `18`, `19`, `0D`, `3A`, `3B`, `3C`.
- The commands `28`, `33`, `39`, `42`, `3A` and the nine unexplained Device Inquiry bytes.
- Every command in Standalone Mode (never used) and on the External Port with a device attached.

Full list, with the device test that would close each: [08-gaps.md](08-gaps.md#sysex).

## Conflicting records

| Topic | Records | Which wins and why |
|---|---|---|
| "18/18" scope | `R096:271-274` and `R114:106-110` give "18 of 18" for the `38` readbacks and verify `06` and `08` by sight only | No conflict: the raw log shows 18 of 18 for type and gain and no `06`/`08` readback (above), as the drafts say. Read "18 of 18" as type and gain only, never as including brightness |
| `37` untested on the User Port | `R090` open item 5 | Superseded the same day: `37` writes and `38` reads on the User Port are in the device-check log |
| `07` readable? | `R100:258` says the Push cannot report its LED brightness; `R001:66` lists `07` | Both stand for their scope. A direct measurement shows `07` answers on the macOS Live Port; whether it answers in User mode on Windows was never tried |
| `43` as a write | DrivenByMoss intends `43` + 128 values; `R064:141,152` | Measurement shows `43` with no arguments is a read trigger; no write was ever sent |
| Palette persistence | `R001:71,128` say the palette survives power cycles and that Live's start explains the drift of entry 100 | Not established. Live's own write equals the 2026-10-01 dump in all 128 entries and does not explain the later readings. The evidence and the 78 differing entries between the two dumps are in [03-leds.md](03-leds.md#conflicting-records) |
