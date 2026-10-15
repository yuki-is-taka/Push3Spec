# Push3Spec

Push3Spec records the measured behaviour of one Ableton Push 3 unit used as a computer controller without Ableton Live: how it presents itself over USB and MIDI, what its controls send, how its LEDs, SysEx commands, display and audio interface behave, and how fast it answers. The measurements were recorded from 2026-10-01 to 2026-10-08 on a Mac and a Windows PC. Where Live was running in a session, the page says so, and the coexistence with Live is recorded in the connection page. This is not Ableton's documentation (Ableton's own statements appear only in rows labelled Official) and it is not a guide for any particular host software: it states what the unit did, not how to build on it.

## The unit and its firmware

"The owner" on these pages is the person who owns the unit and ran the tests; "the notes" are the owner's written notes from the sessions. All records concern one Push 3 unit. One USB serial number (not published) is on record and no record names a second unit, so the use of the same unit throughout is the working assumption, not a verified fact. The notes label the unit "firmware 1.5.4"; the label is stated for S01 and S03 only, and no record shows where it was read. The only value read from the unit is the Device Inquiry reply, software 1.0 and build 91, byte-identical on every read: 2026-10-01 (S01), 2026-10-02 (S02, S03), 2026-10-03 (S04) and 2026-10-08 (S11). It was not read in the other sessions, so firmware is not logged for them. No record relates "1.5.4" to "sw 1.0 build 91" or to Ableton's published "Push 2.4.6", and whether the firmware changed between the read dates is not recorded. Details: [docs/09-sessions.md](docs/09-sessions.md#firmware).

## Evidence labels

Every fact row on the pages carries an Evidence label, the session IDs and a source. The six labels:

| Label | Meaning |
|---|---|
| Measured | The result itself is in a raw record from this unit (log, capture, dump, JSON snapshot). A record the host cached while the unit was attached (registry, device listing) is also Measured and says "cached host record". |
| Reported | Observed on this unit, but the result is written down only in notes or prose, with no raw record of the result. Visual checks, prose summaries of runs whose logs are missing and owner observations go here. |
| Inferred | Deduced, not directly captured, including values derived by arithmetic from a measured value and requests a script sends but does not log. |
| Third-party | DrivenByMoss, community repositories, and Push 2 documents applied to Push 3. |
| Official | Ableton documentation for Push 3. |
| Not tested | Nothing was tried. A row that says "no message arrived" in a run that did happen is Measured, not Not tested. |

## How to read the citations

`S01` to `S14` are test sessions. [docs/09-sessions.md](docs/09-sessions.md) defines each one: host and operating system, firmware, hardware mode, MIDI mode, ports, tools, whether Live was running, and the raw records. A fact that cites a session inherits every column of that row. Sources are given as record IDs into the owner's private research archive, which is not published and not version-controlled: a source is written `R012:45` (record `R012`, line 45) or `R012:45-50` (lines 45 to 50), and an ID without a line number cites the whole record. A record ID can therefore be resolved only by the owner, and line numbers are those of the records as they stood on 2026-10-15. [docs/10-sources.md](docs/10-sources.md) lists every record ID with its kind, session, date and a one-line description, expands the abbreviations and lists the third-party and official references. MIDI channels are written 1-16, note and CC numbers are decimal, and SysEx bytes are hexadecimal.

## Pages

- [docs/01-connection.md](docs/01-connection.md): USB identity and interfaces, MIDI port names per OS, hardware modes, MIDI modes and switching, the User button, sharing with Ableton Live.
- [docs/02-controls.md](docs/02-controls.md): every physical control and the MIDI message it sends, per mode and port.
- [docs/03-leds.md](docs/03-leds.md): lighting pads, buttons and the touch strip: palette, blink and pulse, brightness.
- [docs/04-sysex.md](docs/04-sysex.md): byte-level SysEx reference with readback status and where each command was verified.
- [docs/05-display.md](docs/05-display.md): the display's USB interface, frame format, rates and stop-sending behaviour.
- [docs/06-audio.md](docs/06-audio.md): the audio interface as measured: devices, channels, levels, input type and gain.
- [docs/07-timing.md](docs/07-timing.md): reply times, rates, message ordering and loss.
- [docs/08-gaps.md](docs/08-gaps.md): open questions, the device test that would close each, and unresolved conflicts between records.
- [docs/09-sessions.md](docs/09-sessions.md): the session register S01 to S14, time zones and firmware.
- [docs/10-sources.md](docs/10-sources.md): record index, abbreviations and external references.

## Caveats to know first

- The full control map rests on one recording: Control Mode, macOS, Live not running, MIDI mode not queried (S01). Only 32 of the 70 plain buttons were seen again in User mode, and 38 never were. See [docs/02-controls.md](docs/02-controls.md).
- Standalone Mode was never used in any session, and only S01 names Control Mode; for the other sessions it is inferred from the unit being driven over USB. See [docs/01-connection.md](docs/01-connection.md#hardware-modes).
- Firmware is only partly logged: a note-taker's label for S01 and S03, and the Device Inquiry build in five sessions. See [docs/09-sessions.md](docs/09-sessions.md#firmware).
- How an LED or the display looked is the owner's eye, not a measurement, and the palette was not stable between dumps (78 of 128 entries differ). See [docs/03-leds.md](docs/03-leds.md#conflicting-records) and [docs/05-display.md](docs/05-display.md).
- Nothing was ever played to the Push's audio outputs, and message loss was never counted. See [docs/06-audio.md](docs/06-audio.md) and [docs/07-timing.md](docs/07-timing.md).

## License

The text and tables in this repository are licensed under the [Creative Commons Attribution 4.0 International License](LICENSE) (CC BY 4.0). Ableton, Push and Live are trademarks of Ableton AG. This project is independent and is not affiliated with or endorsed by Ableton.
