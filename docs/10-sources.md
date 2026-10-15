# 10 - Sources

This page says where the evidence behind the other pages comes from and how to read a citation. Other pages cite raw evidence as a record ID and line number (`R012:45`, or `R012:45-50` for a range; an ID without a line number cites the whole record) and cite test sessions by ID (`S01` to `S14`, page 09). The records belong to the owner's private research archive, which is not published; the record IDs are the only way the public pages refer to it. Part (a) lists the records (ID, kind of record, sessions, date, contents) and resolves the terms and abbreviations used on the pages. Part (b) lists the third-party and official references with commit hashes and URLs. Part (c) says which records hold the raw logs of each session.

## (a) The records

The archive is the owner's private folder of notes, captures, logs and test scripts from the sessions. It is not under version control and it has no backup, so each record is the only copy. A line number in a citation is a line of the record as it stood on 2026-10-15; the decision log `R100` is append-only and its line numbers are stable by rule, other records may be edited.

A few records were kept in the owner's other working folders rather than in the archive folder itself: the logs of the first Windows study and of the H1 session, the device mock's logs and source, the test host's design decision record and one of its source files. Where a page says that a log is "outside the archive" it means such a record; these records have IDs like the others and are marked in the index.

Dates in the index are file modification times (JST) unless the record states its own date; for logs the first and last entry dates are given when they differ. The Sessions column lists the sessions whose facts cite the record ("none" where a record supports only a statement that belongs to no session). A row marked "set" stands for several files of the same kind that the pages cite together under one ID.

### Records index

| ID | Kind of record | Sessions | Date | Contents |
|---|---|---|---|---|
| R001 | owner's notes | S01-S06 | 2026-10-01 to 10-04 (last edit 10-04 12:36) | Summary of device findings from the Mac (10-01, 10-02) and the Windows PC (10-02 to 10-04), with sections on coexistence with Live and on WinUSB |
| R002 | log of a test run | S09 | 2026-10-06 17:12-17:56 | CoreAudio spike on the Mac: device list with the unit's USB identity strings and CoreAudio identifiers |
| R003 | log of a test run | S04 | 2026-10-03 (03:31-03:44 UTC) | UE5 test-host spike on the Windows PC: WinUSB display acquisition with Live started first (520.7 s wait), device identification lines |
| R004 | snapshot (text) | S03 | 2026-10-02 07:53 | Process and USB-interface owners with Live running |
| R005 | log of a test run | S01, S03 | 2026-10-01 23:12-23:42; 2026-10-02 07:15 | Python MIDI test on the Mac: port listing, LED test, Device Inquiry, palette read, input logs (lines 1-440 S01, 441-442 S03) |
| R006 | log of a test run | S02 | 2026-10-02 10:09-10:22 | Python MIDI test on the Windows PC: ports, Device Inquiry, palette read (kept outside the archive folder) |
| R007 | owner's notes | S01 | 2026-10-02 07:39 | Measured control table (87 controls, Control Mode, Live Port, macOS, 2026-10-01) and the LED-addressing notes |
| R008 | log of a test run | S03 | 2026-10-02 10:50-11:14 | MIDI mode test on the Mac: `0A 00`, `0A 02`, `0A 01`, `0A 00` with Play presses |
| R009 | log of a test run | S11 | 2026-10-08 13:28 | Python probe on the Windows PC: which port answers Device Inquiry, `38` and `27` reads in User mode; mode requests |
| R010 | raw MIDI capture | S03 | 2026-10-02 15:44-15:46 | MIDI Monitor capture during the User-button coexistence test, Live running |
| R011 | evidence write-up | S08 | 2026-10-06 (times UTC) | H3, the UE5 test-host walk with the unit on the Windows PC: setup, matcher outcome, replug timeline, supplementary run |
| R012 | log of a test run | S08 | 2026-10-06 (UTC) | Listen-only WinMM tap on the Live and User inputs during the S08 walk |
| R013 | log of a test run | S03 | 2026-10-02 15:31 | Second-claim display open attempts while Live's helper held IF#0 |
| R014 | log of a test run | S04 | 2026-10-03 12:11-12:26 | Python logger on the three WinMM inputs with Live running: User-button return timing, mode reports, Live poll replies |
| R015 | log of a test run | S14 | 2026-10-06 17:14 | Read-only listing on the Windows PC of cached PnP device nodes, MMDevices registry entries and default endpoints (the unit was on the Mac) |
| R016 | log of a test run | S01, S03, S09 | 2026-10-01 to 10-06 (HH:MM:SS stamps only) | Python display test on the Mac: static, animation and tilt-card runs (lines 1-34 S01, 35-38 S03, 39-65 S09) |
| R017 | log of a test run | S02 | 2026-10-02 10:09-10:22 | Python display test on the Windows PC: frame-rate figure (kept outside the archive folder) |
| R018 | compiled fact sheet | none (Official sources) | 2026-10-08 | Catalogue of Ableton's own documentation on user-configurable settings; not the manual itself |
| R019 | log of a test run | S04 | 2026-10-03 13:42 | C++ libremidi probe with a ground-truth client: calibration run, Device Inquiry, palette and mode requests per cable |
| R020 | log of a test run | S03 | 2026-10-02 15:05 | Host script holding display IF#0 while Live was started; Live's helper, SysEx and port traffic |
| R021 | snapshot (text) | S03 | 2026-10-02 07:57 | Process and USB-interface owners after Live quit |
| R022 | log of a test run | S06 | 2026-10-04 11:11-11:51 | Device mock on the Windows PC: 40-minute User-mode run with decoded control names (kept outside the archive folder) |
| R023 | evidence write-up | S09 | 2026-10-06 | H27 Mac half: Learn press path and LED behaviour on CoreMIDI, steps M0-M9 |
| R024 | log of a test run | S09 | 2026-10-06 12:45-12:54 | Python capture on the Mac in User mode (513 s capture) |
| R025 | log of a test run | S04 | 2026-10-03 13:41 | C++ libremidi probe: Windows MIDI Services endpoint listing (blocks, groups, port names) |
| R026 | evidence write-up | S07 | 2026-10-05 (edited 10-06) | H26 (jog press cross-talk) and H27 Windows half (Learn in User mode): setup, capture, mode report, owner answers |
| R027 | set of logs (43 files) | S04 | 2026-10-03 13:41-13:53 | Merged probe plus ground-truth logs of the C++ libremidi probe harness |
| R028 | log of a test run | S03 | 2026-10-02 15:30 | Python listen on the Mac in User mode: pads, Play, encoder, touch strip |
| R029 | log of a test run | S07 | 2026-10-05 18:03-18:29 | Python capture on the Windows PC in User mode (kept outside the archive folder) |
| R030 | log of a test run | S04 | 2026-10-03 20:05-20:44 | LED output through the UE5 test host, its MIDI layer, libremidi and Windows MIDI Services to the User Port |
| R031 | log of a test run | S03 | 2026-10-02 18:36 | Host script holding display IF#0, Live not started; one-second summaries of port traffic |
| R032 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of one control block (left upper): every message seen while the owner operated each control |
| R033 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture, Mono (channel aftertouch) behaviour |
| R034 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture, MPE behaviour |
| R035 | test script source | S01, S03 | 2026-10-02 | Python script of the guided control-map capture (Mac) |
| R036 | log of a test run | S07 | 2026-10-05 | Python LED run on the Windows PC (mode re-send) (kept outside the archive folder) |
| R037 | log of a test run | S07 | 2026-10-05 | Python LED step run on the Windows PC (mode re-send) (kept outside the archive folder) |
| R038 | log of a test run | S09 | 2026-10-06 12:45-15:08 | Python step script on the Mac, steps M0-M9 |
| R039 | compiled fact sheet | S03, S04, S11 | 2026-10-08 | Device-level settings that a non-Live host can change or read, derived from the notes and raw captures |
| R040 | log of a test run | S03 | 2026-10-02 15:41-15:43 | User-button hand-back test on the Mac with Live running |
| R041 | log of a test run | S03 | 2026-10-02 15:53 | Host script holding display IF#0 while Live was started; Live's helper, SysEx and port traffic |
| R042 | log of a test run | S06 | 2026-10-04 12:20-12:27 | UE5 test-host spike: User button without Live (H13), flap with the spike bound (H20), orderly release (H15) |
| R043 | log of a test run | S03 | 2026-10-02 15:39 | Second-claim display open attempts while Live's helper held IF#0 |
| R044 | log of a test run | S03 | 2026-10-02 15:56 | Second-claim display open attempts while Live's helper held IF#0 |
| R045 | desk-research note | S04 | 2026-10-03 | Assessment of libremidi's Windows MIDI Services backend |
| R046 | raw MIDI capture | S03 | 2026-10-02 11:05-11:13 | MIDI Monitor capture of Live's start-up, host-to-Push lines only |
| R047 | raw MIDI capture | S03 | 2026-10-02 11:05-11:13 | MIDI Monitor capture of Live quitting, host-to-Push lines only |
| R048 | desk-research note | S04 | 2026-10-03 | Results of the probe run of libremidi's Windows MIDI Services backend against the unit |
| R049 | log of a test run | S04 | 2026-10-03 13:42 | C++ libremidi probe: send to the User Port by group (output-group test) |
| R050 | log of a test run | S04 | 2026-10-03 13:48 | C++ libremidi probe, AddressSanitizer build: stderr with the stack-buffer-overflow report |
| R051 | set of logs (47 files) | S04 | 2026-10-03 13:41-13:53 | Folder of the C++ libremidi probe harness logs (43 merged logs plus probe error logs) |
| R052 | desk-research note | S04 | 2026-10-03 | Source audit of libremidi's Windows MIDI Services backend and the service's behaviour |
| R053 | log of a test run | S04 | 2026-10-03 13:43 | C++ libremidi probe: raw-callback receive path, SysEx replies |
| R054 | log of a test run | S04 | 2026-10-03 13:44 | C++ libremidi probe: per-message receive path with ground-truth client, SysEx replies |
| R055 | test script source | S03 | 2026-10-02 | Python script of the MIDI mode test |
| R056 | log of a test run | S09, S10 | to 2026-10-08 02:47 | Shared log of the working mock: lines 1-57189 Mac (S09 block c, 16:27-19:21, undated), 57190-90353 Windows PC (S10, 2026-10-07 18:41 to 10-08 02:47); several runs |
| R057 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture: slides |
| R058 | test script source | S06 | 2026-10-04 | Python source of the device mock (CC name table and decoding) (kept outside the archive folder) |
| R059 | evidence write-up | S09 | 2026-10-06 | H28 jog tilt capture on the Mac in User mode |
| R060 | log of a test run | S11 | 2026-10-08 10:39-11:20 | Working mock on the Windows PC: settings-page session in explore mode |
| R061 | log of a test run | S11 | 2026-10-08 12:31-12:58 | Working mock on the Windows PC: settings-page session, run 2 |
| R062 | log of a test run | S11 | 2026-10-08 12:59-13:11 | Working mock on the Windows PC: settings-page session, run 3 |
| R063 | log of a test run | S11 | 2026-10-08 13:11-13:27 | Working mock on the Windows PC: device-settings check, 18 `37` writes with `38` read-back on the User Port |
| R064 | desk-research note | none (Third-party sources) | 2026-10-01 | DrivenByMoss's Push 3 code compared with Ableton's Push 2 interface specification |
| R065 | owner's notes | S01 | 2026-10-01 23:34 | Control blocks and names used to drive the guided capture, from the Ableton Push 3 manual figures |
| R066 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture, pad mode `1E 02` variant |
| R067 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture with the pressure/position option on |
| R068 | JSON capture | S03 | 2026-10-02 07:20-07:29 | Guided pad capture with the pressure/position option off |
| R069 | JSON snapshot | S03 | 2026-10-02 10:50-11:14 | Settings snapshot read from the unit by SysEx |
| R070 | test script source | S09, S10, S11 | 2026-10-06 to 10-08 | Python source of the working mock's I/O module |
| R071 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of one control block (left middle) |
| R072 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the jog wheel |
| R073 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the touch strip |
| R074 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the right navigation block |
| R075 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of one control block (left column) |
| R076 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the upper display buttons |
| R077 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the lower display buttons |
| R078 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of one control block (right upper) |
| R079 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the scene column |
| R080 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of the right edit block |
| R081 | JSON capture | S01 | 2026-10-01 23:38-23:50 | Guided capture of one control block (right bottom) |
| R082 | log of a test run | S08 | 2026-10-06 (UTC) | Supplementary listen-only WinMM tap during S08 |
| R083 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | MIDI Monitor capture of Live's Setup tab for pedals, host-to-Push lines only |
| R084 | owner's notes | none (starting note) | 2026-10-01 | Starting hand-over note: goal, decisions, open questions, forum links |
| R085 | raw MIDI capture | S03 | 2026-10-02 15:44-15:46 | MIDI Monitor capture during the User-button coexistence tests |
| R086 | log of a test run | S09 | 2026-10-06 | Raw log of the H28 jog tilt capture on the Mac |
| R087 | set of JSON captures | S01, S03 | 2026-10-01 to 10-02 | All guided-capture JSON files |
| R088 | set of JSON captures (6 files) | S03 | 2026-10-02 07:20-07:29 | Guided pad captures: strike, mode variants, pressure/position options, slides |
| R089 | desk-research note | none (Third-party sources) | 2026-10-01 | Community Push 3 implementations compared with DrivenByMoss and the Push 2 specification |
| R090 | hand-over note | none | 2026-10-08 09:20 | Hand-over of the archive folder for the next stage of the work; states that the folder has no version history |
| R091 | set of JSON captures (4 files) | S03 | 2026-10-02 07:36-07:46 | Guided output tests: LED colour, brightness, dimming, sweeps, with the owner's yes/no answers |
| R092 | JSON capture | S03 | 2026-10-02 07:36-07:46 | Guided output test: brightness |
| R093 | JSON capture | S03 | 2026-10-02 07:36-07:46 | Guided output test: LED colour and pad/button lighting with the owner's answers |
| R094 | JSON capture | S01 | 2026-10-01 23:16 | The 128-entry palette read from the unit (r, g, b, white) |
| R095 | JSON capture | S03 | 2026-10-02 07:36-07:46 | Guided output test: sweeps |
| R096 | hand-over note | S11 | 2026-10-08 | Implementation hand-off of the UE5 test host's Setup page; holds the owner's brightness observation |
| R097 | test script source | S03 | 2026-10-02 | Python script of the guided output tests (Mac) |
| R098 | JSON capture | S03 | 2026-10-02 07:36-07:46 | Guided output test: dimming |
| R099 | design decision record | S11 | 2026-10-09 21:47 | Landed decision record of the UE5 test host's Setup page; section 11 records the 2026-10-08 hardware results and owner walk on the Windows PC (kept outside the archive folder) |
| R100 | decision log | S03, S11 | last edit 2026-10-10 10:20 | Append-only decision log with context; carries hardware observations in some rows |
| R101 | log of a test run | S03 | 2026-10-02 15:02 | Host script holding display IF#0, no Live helper line |
| R102 | log of a test run | S03 | 2026-10-02 20:00 | Host script holding display IF#0, Live not started |
| R103 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | MIDI Monitor capture of Live's Setup tab for pad sensitivity |
| R104 | test script source | S01-S03 | 2026-10-02 | Python script of the MIDI test (ports, LED test, Device Inquiry, palette read) |
| R105 | JSON capture | S03 | 2026-10-02 10:50-11:14 | SysEx read scan, 27 groups, 727 values |
| R106 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | MIDI Monitor capture of Live's Setup tab for expression, host-to-Push lines only |
| R107 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | Pad-sensitivity capture, host-to-Push lines only |
| R108 | log of a test run | S03 | 2026-10-02 10:50-11:14 | Log of the SysEx read scan |
| R109 | test script source | S03 | 2026-10-02 | Python script of the SysEx read scan |
| R110 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | MIDI Monitor capture of Live's Setup tab for audio, host-to-Push lines only |
| R111 | raw MIDI capture | S03 | 2026-10-02 08:03-08:12 | MIDI Monitor capture of Live's Setup tab for audio, unfiltered |
| R112 | set of raw MIDI captures | S03 | 2026-10-02 08:03-15:46 | All MIDI Monitor captures of Live talking to the Push (Setup tabs, start and quit, coexistence tests) |
| R113 | raw MIDI capture | S03 | 2026-10-02 11:05-11:13 | MIDI Monitor capture of Live's start-up, unfiltered |
| R114 | draft design note | S11 | 2026-10-08 | Design r4 of the UE5 test host's Setup page; sections 10 and S6 hold the unattended scripted runs S3-A to S3-C and the owner walk |
| R115 | test script source | S01, S02, S03, S09 | 2026-10-02 | Python script of the display test (static, animation, tilt-card) |
| R116 | log of a test run | S04 | 2026-10-03 (03:00-03:16 UTC) | UE5 test-host spike: WinUSB display run |
| R117 | desk-research note | none | 2026-10-02 | Research on rendering for the 960 x 160 display |
| R118 | log of a test run | S06 | 2026-10-04 12:32-12:35 | UE5 test-host spike: replug (H17), Live start after release (H19), spike quit |
| R119 | desk-research note | S02, S12, S13 | 2026-10-02 14:39 | Research on multichannel audio capture, with the read-only Windows PnP and registry queries of 2026-10-02 and the notes on the two UE audio capture spikes |
| R120 | log of a test run | S09 | 2026-10-06 17:12-17:56 | Swift CoreAudio test harness on the Mac: 10 s capture, Live not running |
| R121 | log of a test run | S09 | 2026-10-06 | Swift CoreAudio harness: loopback onset run with a left-channel 1 kHz tone |
| R122 | log of a test run | S09 | 2026-10-06 | Swift CoreAudio harness: loopback onset run with a right-channel 1 kHz tone |
| R123 | owner's notes | S09, S14 | 2026-10-06 20:33 | Step-0 audio spikes S0-a to S0-e: layout and results summary |
| R124 | log of a test run | S09 | 2026-10-06 | Swift CoreAudio harness: loopback onset run with clicks (latency) |
| R125 | snapshot (text) | S04 | 2026-10-03 | Process names listed before Live was started |
| R126 | owner's notes | none | 2026-10-06 | Instructions for the two remaining step-0 audio runs that need the unit on the Windows PC; no results |
| R127 | log of a test run | S09 | 2026-10-06 | Analysis of the loopback onset times (latency between two capture paths) |
| R128 | compiled fact sheet | S09, S12, S13 | 2026-10-06 | Read-only fact sheet on audio capture |
| R129 | draft design note | S09 | 2026-10-06 | Design r2.2 of external audio input; contains the Mac loopback and latency measurements |
| R130 | test script source | S04 | 2026-10-03 | Python ground-truth client of the libremidi probe harness |
| R131 | log of a test run | S08 | 2026-10-06 (UTC) | USB PnP watcher during the S08 walk: removal and arrival times |
| R132 | owner's notes | S07 | 2026-10-05 (edited 10-06) | The owner's answers about LED brightness during the H1 session |
| R133 | set of logs | S03 | 2026-10-02 15:02-20:01 | Mac coexistence runs: a script holds display IF#0 and logs the Live helper, SysEx and ports; User-mode input; User-button hand-back |
| R134 | photograph | S03 | 2026-10-02 07:38 | Photograph of the unit with every button CC driven to 127 |
| R135 | set of files | S03 | 2026-10-02 10:50-11:14 | SysEx read scan, settings snapshot, value watcher, mode test and their scripts |
| R136 | log of a test run | S04 | 2026-10-03 19:47 | Ground-truth client output for the checkpoint-A libremidi tests |
| R137 | log of a test run | S04 | 2026-10-03 | Unreal Editor log of the UE5 test-host spike's launcher smoke run (local start 11:44) |
| R138 | log of a test run | S04 | 2026-10-03 (02:50-03:00 UTC) | UE5 test-host spike: WinUSB display run 1 |
| R139 | snapshot (text) | S04 | 2026-10-03 (03:07 UTC) | Window and foreground-process snapshot with the UE5 test host and Live open |
| R140 | log of a test run | S04 | 2026-10-03 19:42-19:47 | Unreal Editor log of a libremidi checkpoint-A test (local start 19:42) |
| R141 | log of a test run | S04 | 2026-10-03 19:42-19:47 | Unreal Editor log of a libremidi checkpoint-A test (local start 19:44) |
| R142 | log of a test run | S04 | 2026-10-03 19:42-19:47 | Unreal Editor log of a libremidi checkpoint-A test (local start 19:46) |
| R143 | log of a test run | S06 | 2026-10-04 11:10 | Smoke run of the device mock (kept outside the archive folder) |
| R144 | log of a test run | S06 | 2026-10-04 13:35-13:38 | UE5 test-host level-switch spike S-L1 with the unit connected |
| R145 | log of a test run | S06 | 2026-10-04 13:35-13:38 | UE5 test-host level-switch spike S-L2 with the unit connected |
| R146 | owner's notes | S06 | 2026-10-04 (last edit 10-05) | UE5 test-host v1 spike measurements (S-L1, S-L2, S-W3, later S-C1 to S-C3) on the Mac and the Windows PC |
| R147 | log of a test run | S08 | 2026-10-06 (UTC) | Send log of the S08 walk |
| R148 | log of a test run | S08 | 2026-10-06 (UTC) | Supplementary USB PnP watcher log |
| R149 | set of files | S09 | 2026-10-06 12:45-15:08 | Capture and step scripts and logs of the Mac H27/H28 session |
| R150 | set of logs | S09 | 2026-10-06 17:12-17:56 | Swift CoreAudio harness results on the Mac |
| R151 | hand-over note | S12 | 2026-10-02 (last edit 10-07 15:29) | Session hand-off note, first written 2026-10-02 |
| R152 | set of logs | S09 | 2026-10-06 | Logs of the Mac H27/H28 session |
| R153 | set of files | S02, S07 | 2026-10-02 to 10-05 | Logs and scripts of the first Windows study and the H1 session (kept outside the archive folder) |
| R154 | set of logs | S06 | 2026-10-04 11:10-11:51 | Device mock logs: smoke run and 40-minute run (kept outside the archive folder) |
| R155 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 08:03-08:12 | Pad-sensitivity capture, unfiltered and filtered |
| R156 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 08:03-08:12 | Expression capture, unfiltered and filtered |
| R157 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 08:03-08:12 | Pedals capture, unfiltered and filtered |
| R158 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 08:03-08:12 | Audio-tab capture, unfiltered and filtered |
| R159 | set of files (3 files) | S03 | 2026-10-02 10:50-11:14 | SysEx read scan: script, log and JSON values |
| R160 | log of a test run | S03 | 2026-10-02 10:50-11:14 | Python value watcher |
| R161 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 11:05-11:13 | Live start-up capture, unfiltered and filtered |
| R162 | set of raw MIDI captures | S03 | 2026-10-02 11:05-11:13 | Live quit capture |
| R163 | set of logs (4 files) | S03 | 2026-10-02 15:31-15:56 | Second-claim display open attempts while Live's helper held IF#0 |
| R164 | set of raw MIDI captures (2 files) | S03 | 2026-10-02 15:44-15:46 | MIDI Monitor captures during the User-button coexistence tests |
| R165 | set of logs | S09 | 2026-10-06 | Loopback latency runs of the Swift CoreAudio harness |
| R166 | evidence write-up | none (not counted as a session) | 2026-10-06 | H2 disconnect sweep with other MIDI controllers; no Push 3 facts, read only to rule it out |
| R167 | log of a test run | none (not counted as a session) | 2026-10 | Headless working-mock run with scripted input |
| R168 | test script source | none | n/a | Source file of the UE5 test host; only its comments are used (the firmware label at lines 49-50) (kept outside the archive folder) |
| R169 | owner's notes | none | 2026-10-02 | Folder index of the archive: rebuild instructions, commit hashes of the reference repositories, script table |
| R170 | JSON capture | S01 | 2026-10-02 07:28 | Control blocks and names used to drive the guided capture (machine-readable form of the layout note) |

### Terms and abbreviations used on the pages

| Term | Meaning |
|---|---|
| n/l | not logged: the value is not in any record |
| FW | firmware |
| UE, UE5 | Unreal Engine, Unreal Engine 5 |
| the UE5 test host | a private Unreal Engine 5 project of the owner that tries the Push 3 as a controller from a host application; its spikes, MIDI layer, Setup page and design decision record are referred to by those descriptions; it is not published |
| the working mock | a Python mock of a host application that the owner ran on the Mac and the Windows PC (S09 to S11) |
| the device mock | a second Python mock of a host application, used for the 40-minute Windows run (S06 block a) |
| H1, H13, H27 and so on | the owner's labels for numbered hardware tests, as the session notes use them |
| the Live helper | the `Push3` process that Ableton Live starts to drive the Push |

Abbreviations of the third-party desk research (defined in `R064:5-17` and `R089:5-12`): DBM = DrivenByMoss; SPEC = `push-interface/doc/AbletonPush2MIDIDisplayInterface.asc` (Push 2 spec, 2139 lines); MAP = `push-interface/doc/Push2-map.json`; JUCE = `push2-display-with-juce`; P3D = `push3-protocol-docs`; OFOU = `ofou/push3-python`; PDS, SUSB, PDT = the three `jaekong` repositories. These clones were deleted after the research (`R169`), so their file:line citations can only be checked against the commits in part (b).

## (b) Third-party and official references

Commit hashes are those recorded in `R169` (short form) and in `R064:7-12` and `R089:12-14` (full form). The `file:line` citations in the two desk-research notes are against these commits; the clones themselves were deleted.

| Short name | Repository / source | Commit (short / full) | Commit date | Branch | Role and licence note as recorded |
|---|---|---|---|---|---|
| DrivenByMoss (DBM) | https://github.com/git-moss/DrivenByMoss | 7348cda / `7348cda4e5ae8aebd59bb99b8d967a1f4b87677e` | 2026-09-04 | master | Reference for the Push 3 protocol layer; LGPLv3 (its `pom.xml` says LGPL-2.1-or-later, a contradiction); facts only, no code ported (`R001:131-136`) |
| push-interface | https://github.com/Ableton/push-interface | 5eb352f / `5eb352fe3da9d07891efb7447191c03a66d9fbdd` | 2026-01-22 | main | Ableton's Push 2 MIDI and Display Interface Manual (`doc/AbletonPush2MIDIDisplayInterface.asc`) and `Push2-map.json`; no licence file, text may be used but not copied or redistributed without Ableton's written consent, so the text is not reproduced |
| push2-display-with-juce | https://github.com/Ableton/push2-display-with-juce | 48e9be8 / `48e9be846bda686b97a0df7635d80e63a51a5e4b` | 2021-07-28 | master (marked ARCHIVED) | Ableton's Push 2 display sample (C++); MIT (libusb LGPL-2.1) |
| push3-protocol-docs (P3D) | https://github.com/danielknng/push3-protocol-docs | 87e7feb / `87e7feb21378c9fdca044a46a40c9ca836a1dd74` | 2026-03-31 | main | Community notes on Push 3 hardware differences; read as a supplement because the DrivenByMoss manual (p.21) points to it; not treated as an independent verification source (`R064:23`) |
| ofou push3-python (OFOU) | https://github.com/ofou/push3-python | 6ffc6b7 / `6ffc6b78ad8c6e563d4517df28167b95a8765215` | 2026-01-14 | `add-push-3` | Community Push 3 control map and display code; three commits ahead of upstream push2-python; control CC numbers partly unknown in its own source |
| jaekong PushDisplayServer (PDS) | https://github.com/jaekong/PushDisplayServer | 45a56a2 / `45a56a29970052d42cb28de17315369b5d35066a` | 2025-09-21 | main | Swift display server; one commit, no README |
| jaekong swift-usb (SUSB) | https://github.com/jaekong/swift-usb | 7180954 / `7180954078ec99ab0952a05dda371b0e601cb7f2` | 2025-09-25 | main | Swift USB library used by PDS; bulk transfer only |
| jaekong pushDisplayTest (PDT) | https://github.com/jaekong/pushDisplayTest | a0f5490 / `a0f54905614252e5422c4894878c41d8eb4a380c` | 2026-01-13 | main | Max/JS display proof of concept |

Ableton documents (as cited; none is in the archive, and the Ableton manual PDF is not stored). `R018` records that `help.ableton.com` returned HTTP 403 to direct fetch, so several of its help-centre entries were taken from search-result snippets and not read in full.

| Document | URL | Cited in |
|---|---|---|
| Push 3 manual (one page) | https://www.ableton.com/en/push/manual/ | `R170`, `R018`, `R119` |
| Push 3 MIDI FAQ | https://help.ableton.com/hc/en-us/articles/26201367296924-Push-3-MIDI-FAQ | `R084`, `R018` |
| Using User Mode (article 209071249) | https://help.ableton.com/hc/en-us/articles/209071249 | `R084` |
| Push 3 Technical FAQ | https://help.ableton.com/hc/en-us/articles/8483166334748-Push-3-Technical-FAQ | `R018` |
| Push 3 Audio Interface Specifications | https://help.ableton.com/hc/en-us/articles/8483423483036-Push-3-Audio-Interface-Specifications | `R018` |
| Push 3 USB power FAQ | https://help.ableton.com/hc/en-us/articles/9109387410716-Push-3-USB-power-FAQ | `R018` |
| Setting up Push 3 to control Live; Push 3 as audio interface; external audio interface with Push 3 standalone; CV Tools on Push 3 | https://help.ableton.com/hc/en-us/articles/8483316601372 ; /19801235618588 ; /23279895416220 ; /21087935492636 | `R018` |
| Push tech specs | https://www.ableton.com/en/push/tech-specs/ | `R018` |
| Release notes, Push 2.x with Live 12.x; Push 1.2.x with Live 11.3.x | https://www.ableton.com/en/release-notes/push-12/ ; https://www.ableton.com/en/release-notes/push-11 | `R018` (current software at compile time: Push 2.4.6 with Live 12.4.6, 2026-09-15) |
| Push 2 MIDI and Display Interface Manual | `doc/AbletonPush2MIDIDisplayInterface.asc` in push-interface (above); the text is not in the archive | `R064`, `R089`, `R039` (as SPEC) |

Secondary sources (forum posts and a review; treated as hearsay by the notes): Ableton forum https://forum.ableton.com/viewtopic.php?p=1828026 (pads send MIDI without Live), ?p=1820229 (User Mode does nothing without Live), ?p=1818165 (volume knob disabled without Live), ?t=247350 (Live must be running for audio, 2023-05-25), ?t=247347 (ASIO driver location), ?t=247936 (not usable in Standalone Mode), ?p=1812281 and ?t=249697 (MPE and gain, in `R018`); Sound On Sound review https://www.soundonsound.com/reviews/ableton-push-3 (`R018`).

## (c) Raw records by session

The table lists the records that the pages cite for each session (the same data as the Sessions column of the index, grouped by session). A record that serves several sessions is listed under each.

| Session | Records |
|---|---|
| S01 | `R001`, `R005`, `R007`, `R016`, `R032`, `R035`, `R065`, `R071`-`R081`, `R087`, `R094`, `R104`, `R115`, `R170` |
| S02 | `R001`, `R006`, `R017`, `R104`, `R115`, `R119`, `R153` |
| S03 | `R001`, `R004`, `R005`, `R008`, `R010`, `R013`, `R016`, `R020`, `R021`, `R028`, `R031`, `R033`-`R035`, `R039`-`R041`, `R043`, `R044`, `R046`, `R047`, `R055`, `R057`, `R066`-`R069`, `R083`, `R085`, `R087`, `R088`, `R091`-`R093`, `R095`, `R097`, `R098`, `R100`-`R113`, `R115`, `R133`-`R135`, `R155`-`R164` |
| S04 | `R001`, `R003`, `R014`, `R019`, `R025`, `R027`, `R030`, `R039`, `R045`, `R048`-`R054`, `R116`, `R125`, `R130`, `R136`-`R142` |
| S05 | `R001` |
| S06 | `R001`, `R022`, `R042`, `R058`, `R118`, `R143`-`R146`, `R154` |
| S07 | `R026`, `R029`, `R036`, `R037`, `R132`, `R153` |
| S08 | `R011`, `R012`, `R082`, `R131`, `R147`, `R148` |
| S09 | `R002`, `R016`, `R023`, `R024`, `R038`, `R056`, `R059`, `R070`, `R086`, `R115`, `R120`-`R124`, `R127`-`R129`, `R149`, `R150`, `R152`, `R165` |
| S10 | `R056`, `R070` |
| S11 | `R009`, `R039`, `R060`-`R063`, `R070`, `R096`, `R099`, `R100`, `R114` |
| S12 | `R119`, `R128`, `R151` |
| S13 | `R119`, `R128` |
| S14 | `R015`, `R123` |

Notes on the raw records:

- S05, S12 and S13 have no raw record; their facts rest on prose in `R001` and `R119`.
- The logs of the UE5 test-host spike runs (S04, S06) were rescued from a test-host working folder before it was deleted; older notes that point to that folder's logs mean the copy in the archive.
- The libremidi probe logs of S04 block b are the 47 files of `R051` (43 merged logs). A second, larger copy (281 files, 58 merged logs) exists outside the archive; the pages cite only the archive copy.
- The Mac files of `R150` are the results of the Swift CoreAudio test harness (S09 block d). `R015` is a read-only Windows device-tree and registry listing taken while the unit was attached to the Mac (S14); pages 01 and 06 cite it. Other Windows files in that results folder are loopback tests of other audio devices, not of the Push, and no page cites them as evidence.
- The Unreal Editor node logs of the S08 walk, named in `R011`, were not found in the archive on 2026-10-15.
