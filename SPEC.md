# SPEC: the shorts pipeline, run two

Status: draft for approval, September 23, 2026. Nothing in this document has been implemented. It is derived from `BUILD-BRIEF.md` (what the pipeline must do), `LESSONS.md` (why it is shaped this way), `CLAUDE.md` (API landmines), `pipeline/job.config.example.json`, and the five scripts as they stand today. Where the current scripts fall short of a requirement, the requirement says so, so the gap is visible before any code changes.

## 1. Scope and deliverables

The deliverables are exactly these, and nothing else.

**Five Node scripts under `pipeline/`:**

| Role | File | Talks to Premiere? |
|---|---|---|
| Driver | `pipeline/mcp-client.mjs` | Yes. The only file that spawns the premiere-pro MCP server and speaks JSON-RPC over stdio. |
| Dead air | `pipeline/dead-air.mjs` | No. Reads the whisper JSON and the audio, writes `cut-plan.json`. |
| Speaker tracking | `pipeline/track-speaker.mjs` | No. Reads the source video and `cut-plan.json`, writes `track-report.json` and `framing-table.json`. |
| SRT generation | `pipeline/make-srts.mjs` | No. Reads the whisper JSON and `cut-plan.json`, writes `srt/<id>.srt`. |
| Phase builder | `pipeline/build-shorts.mjs` | Yes, only through the driver. Runs one phase per invocation. |

Note on names: `CLAUDE.md` calls `build-shorts.mjs` "the driver script". In this spec, "driver" means `mcp-client.mjs` and "phase builder" means `build-shorts.mjs`. Section 11, OQ-01 asks whether to align `CLAUDE.md`.

**One job file per recording:** `jobs/<name>/job.config.json`, copied from `pipeline/job.config.example.json`. It is the only file that changes between recordings.

**State files in the job's `outDir`:** `cut-plan.json`, `track-report.json`, `framing-table.json`, `srt/<id>.srt`, and the exports `<name> 9x16.mp4`.

**The full run order**, fixed:

1. `node pipeline/dead-air.mjs <job>` (step `dead-air`)
2. `node pipeline/track-speaker.mjs <job>` (step `track`)
3. `node pipeline/make-srts.mjs <job>` (step `srt`)
4. `node pipeline/build-shorts.mjs <job> <phase>` for each phase in order: `verify`, `subclips`, `sequences`, `grade`, `verticals`, `captions`, `exports`.

Steps 2 and 3 both depend only on step 1 and may run in either order. In the tables below, "step" names one of the three compute scripts and "phase" names a `build-shorts.mjs` phase.

## 2. Evidence rules

These rules govern every acceptance clip.

- **Anything visual is proven by a captured frame at named timestamps.** A tool returning `ok`, `active:true`, or a clip count is never evidence for a crop, a grade, a caption, or an export. Frames come from `capture_frame` (native premiere-pro tool) for sequences, or from `ffmpeg -ss <t> -frames:v 1` for exported MP4s.
- **Named timestamps** for a sequence of trimmed duration D (from `cut-plan.json`, `trimmedDur`): `T0` = 0.5 s, `TMID` = D / 2, `TEND` = D minus 0.5 s. `TCUT(k)` = 0.25 s after the k-th cut in sequence time. An acceptance clip names which of these it uses.
- **Numeric readback is evidence for numbers only.** A `get_effect_properties` readback of a value, or a parsed state file, proves a number. It does not prove what the frame looks like.
- **Frames are captured with the phase builder stopped.** Native premiere tools and `build-shorts.mjs` never run at the same moment (EX-07).
- **Capture scale matches the question.** A framing check uses scale 0.4 or a zoom region; full resolution is reserved for caption legibility (LESSONS, process lesson 6).

## 3. Behavior requirements (BR)

Each requirement is observable from outside the code. "Fails if" names the observation that proves it broken.

**BR-01 Clip list comes from the job file.** Every clip processed has an `id`, a `name`, and source-second `start` and `end` from `job.clips`. No script invents, drops, or reorders clips.
Fails if: a state file or a Premiere sequence exists for a clip id not in `job.clips`, or a clip in `job.clips` is missing from any state file after its step.

**BR-02 Dead air needs two signals.** A pause inside a clip is cut only when the whisper word gap is at least `gapMin` seconds **and** the mean audio level of the gap, probed inset 0.15 s from each edge, is at or below `quietMeanDb`.
Fails if: `cut-plan.json` lists a cut whose `gap` is shorter than `gapMin`, or whose `meanDb` is above `quietMeanDb`, or whose `meanDb` is null.

**BR-03 A failed probe keeps the gap.** When the audio probe returns no level, the gap is treated as audible and kept.
Fails if: a gap with `meanDb: null` appears under `cuts` rather than `audibleGapsKept`.

**BR-04 Kept gaps are recorded.** Every gap of at least `gapMin` that was not cut is listed under `audibleGapsKept` with its level, so a reviewer can see what was spared and why.
Fails if: the count of `cuts` plus `audibleGapsKept` for a clip differs from the number of word gaps of at least `gapMin` inside that clip's window.

**BR-05 Rhythm survives a cut.** Each cut removes the gap minus `pad` on each side, so about 2 x `pad` (0.6 s at `pad` 0.3) of the original pause remains. A cut shorter than `minCut` is not made.
Fails if: for any cut, `cut[0] - gap[0]` or `gap[1] - cut[1]` differs from `pad` by more than 0.01 s, or `cut[1] - cut[0] < minCut`.

**BR-06 Chunks are the complement of cuts.** Each clip's `chunks` cover `[start, end]` minus its cuts, in order, with no overlap, and `trimmedDur` equals the sum of chunk lengths within 0.1 s.
Fails if: any chunk overlaps a cut or another chunk, any source second outside cuts is missing from chunks, or `trimmedDur` disagrees with the chunks.

**BR-07 One static framing per kept segment.** `framing-table.json` has exactly one position per chunk. Position changes only at chunk boundaries, which are cuts, so every reframe is hidden on a cut.
Fails if: a clip's `segs` count differs from its `chunks` count, or a captured frame shows the crop moving within a single segment.

**BR-08 Framing follows the speaker, not the room.** The tracker zeroes motion above `track.ceilingY` and below `track.audienceY`, weights motion inside `track.screenRect` by `track.weightScreen`, and takes the median motion centroid per segment. A segment with fewer than 3 motion samples inherits the previous segment's position, and the first segment falls back to `track.homeX`.
Fails if: a frame at `T0`, `TMID`, or `TEND` shows audience or the slide screen as the subject instead of the speaker.

**BR-09 The crop never leaves the media.** Each segment's horizontal center is clamped to `[halfWin, mediaW - halfWin]`, where `halfWin = (seqW / 2) / (scale / 100)`. The vertical center (`pyEmphasis`) is clamped the same way against `mediaH`. The current script clamps horizontally only; the vertical clamp is new.
Fails if: a captured frame at any named timestamp shows black at any edge, or a `framing-table.json` segment implies a crop window outside `[0, mediaW] x [0, mediaH]`.

**BR-10 Motion Position is written normalized.** Values written to Premiere are in 0 to 1 (0.5 = centered), never pixels.
Fails if: any `x` or `y` in `framing-table.json` is outside `[0, 1]`, or a captured frame is black.

**BR-11 Grade travels by value.** The `grade` phase reads the nine Basic Correction values (Temperature, Tint, Saturation, Exposure, Contrast, Highlights, Shadows, Whites, Blacks) from the V1 clip of `job.grade.masterSequence` once, then writes the same nine values to every clip of every clip sequence with `color_correct`. It never uses copy or paste.
Fails if: a readback of any clip differs from the master on any of the nine values, or the phase logs any clipboard or paste tool call.

**BR-12 A missing master grade stops the phase.** If any of the nine values cannot be read from the master, the `grade` phase exits 1 naming the missing values and writes nothing. The current script would pass `undefined` through; this check is new.
Fails if: `grade` writes to any clip when the master readback is incomplete.

**BR-13 One Lumetri instance per clip.** After `grade`, each clip carries exactly one Lumetri Color instance.
Fails if: any clip shows zero or two Lumetri Color instances in Effect Controls, or an API listing if one exists (OQ-06).

**BR-14 Missing effects are named, not skipped.** A clip that has no Lumetri to write to is listed as `<sequence name> / <clip name>`, the phase exits 2 after trying every clip, and the message says what to drag and where (Section 8).
Fails if: `grade` exits 0 while any clip lacks Lumetri, or exits 2 without naming each such clip.

**BR-15 Every clip gets a 9:16 sequence.** The `verticals` phase produces `"<name> 9x16"` for each clip at `framing.seqW` x `framing.seqH` (1215 x 2160), scaled to `framing.scale`, one position per segment from `framing-table.json`. It carries the 16:9 sequence's grade because it is a duplicate of it.
Fails if: a clip has no `"<name> 9x16"` sequence, the sequence size is not 1215 x 2160, or its V1 clip count differs from the framing segment count.

**BR-16 Captions come from word timings, remapped through the cuts.** For each clip, every word whose start lies in a kept chunk is placed at `(sum of earlier chunk lengths) + (word start - chunk start)`. Words that start inside a cut are dropped.
Fails if: after the k-th cut, any cue time differs from its source time minus the cumulative removed seconds by more than 0.001 s (SRT millisecond resolution).

**BR-17 Cues are short.** A cue holds at most `captions.wordsPerCue` words (3), ending early only at sentence punctuation or clip end. A cue lasts at least `captions.minCue` seconds unless the next cue starts sooner, cues never overlap, and a gap under `captions.mergeGap` is closed by extending the earlier cue.
Fails if: any cue has more than 3 words, two cues overlap, or a sub-`mergeGap` gap remains between cues.

**BR-18 Captions attach as editable tracks and ship as sidecars.** The `captions` phase imports `srt/<id>.srt` and attaches it as a caption track on `"<name> 9x16"`. The SRT stays in `outDir/srt/`.
Fails if: a frame at a timestamp inside a known cue shows no caption on the 9:16 sequence, or `outDir/srt/<id>.srt` is missing after the run.

**BR-19 Exports use one preset into one folder.** The `exports` phase writes `outDir/<name> 9x16.mp4` for every clip with `job.exportPreset`. Each export is confirmed as newly written in this run, not merely present.
Fails if: an export is missing, is not 1215 x 2160, or has a modification time older than the start of the phase. The current check (`existsSync` only) is fooled by a file left from an earlier run; this is fixed here.

**BR-20 One phase per invocation, in order.** `build-shorts.mjs` runs `verify` first on every invocation, then only the named phase, then saves the project (exports saves too). Each phase checks that its input files exist and exits 1 naming the missing file and the step that produces it.
Fails if: a phase runs without `verify` passing, runs more than one phase, or fails with a stack trace instead of naming a missing input.

**BR-21 Pilot before batch.** `verticals` and `captions` accept an optional clip id so one sequence can be built and framed before the rest (OQ-04). The operator captures that sequence at `T0`, `TMID`, `TEND` before running the batch.
Fails if: the batch runs for all clips before any pilot frame has been captured, as recorded in the run notes.

**BR-22 All durable state lives in `outDir`.** Every file a script writes goes to `job.outDir` or below.
Fails if: after a full run, any script output is found outside `outDir`, including the session scratchpad.

**BR-23 State is never stale.** A phase or step that reads a derived file (`framing-table.json`, `srt/<id>.srt`) exits 1 if that file is older than `cut-plan.json`, naming the step to rerun.
Fails if: `verticals` or `captions` runs against a framing table or SRT older than the cut plan.

## 4. Exclusions (EX)

**EX-01 No cut on one signal.** The pipeline never cuts a gap on audio level alone or on transcript gap alone.
Fails if: any cut in `cut-plan.json` lacks either a qualifying `gap` length or a qualifying `meanDb`.

**EX-02 No Adobe Auto Reframe.** No script calls `auto_reframe` or any Auto Reframe effect.
Fails if: the string `auto_reframe` appears in any script, or a phase log shows it called.

**EX-03 No clipboard, no copy or paste of effects.** No step uses Paste Attributes, copy, or paste.
Fails if: any script calls a tool whose name contains `copy`, `paste`, or `clipboard`.

**EX-04 No burned captions.** No export is made with Burn Captions on. Styling is a person's choice made once in Essential Graphics.
Fails if: a frame extracted from an exported MP4 at a timestamp inside a known cue shows caption text.

**EX-05 No state in the session scratchpad.** No script reads or writes the Claude session scratch folder.
Fails if: any path in any script or job file points into a scratchpad directory.

**EX-06 No clip selection.** The pipeline does not pick, rank, or suggest clips. Selection is written into the job file by a person or by Claude in conversation.
Fails if: any script writes to `job.clips` or creates a clip not listed there.

**EX-07 No concurrent drivers.** The phase builder and native premiere-pro tools never run at the same moment.
Fails if: a native premiere tool call is issued while a `build-shorts.mjs` process is alive (see OQ-08 on enforcement).

**EX-08 No footage the tracker was not built for.** Multi-camera, multi-speaker, and moving-camera footage is out of scope. Two presenters are handled only through per-clip tracking overrides (OQ-07), not by new tracking logic.
Fails if: a job with a moving camera is accepted without the operator being warned that framing is unverified.

**EX-09 No styling, thumbnails, titles, music, or posting.**
Fails if: any script creates graphics, audio tracks other than the source audio, or network calls other than the local MCP server.

**EX-10 No new infrastructure.** No app, database, class hierarchy, shared config system, or new script beyond the five.
Fails if: a sixth `.mjs` file appears under `pipeline/`, or any script defines a `class`.

## 5. Acceptance clips (AC)

AC-01 to AC-04 are the four rows of the brief's "How you will know it worked" table, in order. All clips use the C0828 job (`jobs/c0828/job.config.json`) unless stated. The C0828 state files on disk predate the scripts and use a different schema (see OQ-02); acceptance runs regenerate them into a separate `outDir`.

**AC-01 Dead air with a laugh (SF09).**
Input: "Cut the dead air out of SF09. There is a laugh in it." Run `dead-air`, then `subclips`, `sequences`.
Expected: the laugh gap appears in `audibleGapsKept` with `meanDb` above `quietMeanDb` (-26). Every entry in `cuts` has gap at least 1.2 s and level at or below -26, and keeps 0.3 s of pause on each side. Listening across the laugh's sequence time, the laugh plays whole. Volumedetect on the 16:9 sequence's export window around the laugh reads above -26 dB.
Fails if: the laugh is absent from the sequence, or a cut removes a gap whose level was above -26 (a quiet far-mic sentence), or a cut keeps other than 0.3 s on either side.

**AC-02 Vertical that keeps the speaker (SF11).**
Input: "Make SF11 vertical and keep me in frame." Run `track`, then `verticals` for SF11 only.
Expected: `capture_frame` of "SF11 It is now purple 9x16" at `T0`, `TMID`, and `TEND` each show the speaker chest-up, horizontally inside the middle half of the frame, with no black at any edge. Frames at `TCUT(1)` and `TCUT(2)` show the same, and no frame within a segment differs in crop from another frame in that segment.
Fails if: any of the five frames shows audience or the slide screen as the subject, any frame has black, or the crop moves inside a segment.

**AC-03 Grade matched on every clip (all eleven).**
Input: "Match every clip to the master grade." Run `grade`.
Expected: readback of all nine Basic Correction values on every clip of all eleven sequences equals the master's readback. Effect Controls on every clip shows exactly one Lumetri Color. `capture_frame` at `TMID` of the master sequence and of SF01, SF06, SF11 show matching color.
Fails if: any clip has no grade, has two Lumetri instances, or differs on any of the nine values; or a captured frame shows a visible color mismatch.

**AC-04 Captions through nine cuts (SF01).**
Input: "Caption SF01. It has nine cuts in it." Run `srt`, then `captions` for SF01.
Expected: SF01 has 10 chunks. For each of the nine cuts, the first cue after it is offset from its source time by exactly the cumulative removed seconds (within 0.001 s). No cue has more than 3 words. `capture_frame` of "SF01 Ownership story 9x16" at `TCUT(1)`, `TCUT(5)`, and `TCUT(9)` shows the caption text that the SRT says is live at those times. A frame extracted from the exported SF01 MP4 at `TCUT(5)` shows no caption text.
Fails if: any cue after a cut is off by more than 0.001 s from the expected shift, a captured caption does not match the SRT, a cue has more than 3 words, or the export shows burned text.

**AC-05 Re-run after a Premiere restart.**
Input: complete `sequences` and `grade`, quit Premiere, reopen the project and reload the panel, then rerun `subclips`, `sequences`, `grade`, and `verticals`.
Expected: the project bin holds the same number of subclips and sequences as before the rerun (no duplicates), the nine values are unchanged, and `capture_frame` at `TMID` of every 9:16 sequence matches the pre-restart frame.
Fails if: any duplicate subclip, sequence, or caption item appears, or any frame differs.

**AC-06 Missing Lumetri is reported, then resolved.**
Input: a clip sequence where one clip has no Lumetri Color. Run `grade`.
Expected: exit code 2; the output names exactly that clip as `<sequence> / <clip>` and says to drag Lumetri Color onto it. After the drag, rerunning `grade` exits 0 and the readback matches the master.
Fails if: exit 0 on the first run, a clip is unnamed, or the rerun leaves two Lumetri instances.

**AC-07 Room geometry cannot silently default.**
Input: a job file with `track.screenRect` and `track.homeX` removed. Run `track`.
Expected: exit 1 naming the missing keys; no `framing-table.json` is written.
Fails if: the tracker runs using C0828's geometry.

**AC-08 A failed audio probe keeps the gap.**
Input: a job whose `audioWav` points to a file that exists but is unreadable (for example, zero bytes). Run `dead-air`.
Expected: every gap of at least `gapMin` lands in `audibleGapsKept` with `meanDb: null`; `cuts` is empty for every clip.
Fails if: any cut is made.

**AC-09 Crop clamped at the stage edge.**
Input: a clip where the speaker stands within `halfWin` of the left or right media edge (pick one from `track-report.json` with `p10` or `p90` beyond the clamp, or a hand-set `homeX` of 100 on a segment with fewer than 3 samples). Run `track`, `verticals` for that clip.
Expected: that segment's `px` equals the clamp bound; `capture_frame` at the segment's midpoint shows no black edge.
Fails if: black appears at the edge or `px` exceeds the bound.

**AC-10 Exports are fresh and correct.**
Input: run `exports` for all clips with the previous run's MP4s still in `outDir`.
Expected: eleven MP4s, each modified after the phase started, each 1215 x 2160 by `ffprobe`, and a frame extracted at `T0`, `TMID`, `TEND` of each matches the 9:16 sequence's `capture_frame` at the same times.
Fails if: any MP4 is stale, wrong size, or its frames differ from the sequence.

**AC-11 State stays in `outDir`, and a crash leaves no half file.**
Input: a full run; separately, kill `track-speaker.mjs` mid-write.
Expected: every output is under `outDir`. After the kill, `framing-table.json` is either the previous complete version or absent, never truncated JSON.
Fails if: any file is written elsewhere, or a state file fails to parse.

**AC-12 Stale derived state is refused.**
Input: rerun `dead-air` with a changed `pad`, then run `verticals` without rerunning `track`.
Expected: exit 1 naming `framing-table.json` as older than `cut-plan.json` and telling the operator to run `track`.
Fails if: `verticals` builds with the old framing table.

## 6. Engineering requirements (ENG)

### DRY: applies

The job file is authoritative for every per-recording value. A script holds a value only when it is a fixed convention (Section 6, Convention over configuration), and then in exactly one script.

Today's duplication, found by reading the scripts:
- C0828 room geometry (`homeX`, `screenRect`, `ceilingY`, `audienceY`, `weightScreen`) is hardcoded as defaults in `track-speaker.mjs` and also in the job file. A new room that omits a key silently gets C0828's room. This is the most dangerous duplicate; it is how a tracker frames the wrong person.
- `quietMeanDb` defaults to -26 in `dead-air.mjs`, a C0828-only calibration.
- `gapMin`, `pad`, `minCut` defaults in `dead-air.mjs`; caption defaults in `make-srts.mjs`; framing defaults in `track-speaker.mjs`; `seqW` and `seqH` defaults again in `build-shorts.mjs`.
- The whisper word loader is written twice (`dead-air.mjs`, `make-srts.mjs`) with slightly different filters.
- The subclip name `<id>_NN` is built twice inside `build-shorts.mjs`.
- The MCP server path is in both `.mcp.json` and `mcp-client.mjs`.

**ENG-DRY-01 Room values have no script default.** `quietMeanDb`, every `track` key, `framing.pyEmphasis`, and `grade.masterSequence` are required in the job file. A missing key exits 1 naming it.
Review/test: AC-07; and `grep -nE "2880|1250|1400|-26|1120" pipeline/*.mjs` returns nothing.

**ENG-DRY-02 Each remaining numeric setting has one home.** Every threshold, caption, and framing value lives in the job file only; scripts read it without a literal fallback (see OQ-03 for the alternative of one default per value in one script).
Review/test: for each key in `job.config.example.json`, grep all five scripts; a numeric literal equal to the example value in more than one file is a finding.

**ENG-DRY-03 Each naming rule is built in one place.** The subclip name, the 9:16 name, and the state file paths are each built by one expression in one script.
Review/test: grep `9x16`, `_${`, `cut-plan.json`, `framing-table.json`; each rule appears as a builder once per script and is not reconstructed inline.

**ENG-DRY-04 The whisper word loader is one rule.** Both readers apply the same filter (numeric start and end, non-empty text). Because EX-10 forbids a shared module, the rule is kept identical in both scripts and the review checks them side by side (OQ-05 asks whether to allow the driver to host it instead).
Review/test: diff the two loader blocks; any difference in filter is a finding.

### SOLID, each principle separately

**Single responsibility: applies.**
**ENG-SRP-01 Computing is separate from driving.** `dead-air.mjs`, `track-speaker.mjs`, and `make-srts.mjs` compute from files and write files; they never import `mcp-client.mjs`. `build-shorts.mjs` drives Premiere and does no measurement: it reads positions, chunks, and SRTs, it never derives them. `mcp-client.mjs` only transports.
Review/test: `grep -l mcp-client pipeline/*.mjs` returns only `build-shorts.mjs`. `grep -nE "ffmpeg|whisper" pipeline/build-shorts.mjs` returns nothing.

**Open/closed: applies narrowly.**
**ENG-OCP-01 A new phase is one new entry.** Adding a phase to `build-shorts.mjs` means adding one key to the phase map and one name to the fixed phase order; no existing phase body changes.
Review/test: in the diff that adds a phase, no other phase body is edited.

**Liskov substitution: N/A.** There are no subtypes, classes, or interchangeable implementations, and EX-10 forbids creating them. Nothing substitutes for anything.

**Interface segregation: applies narrowly, to file contracts and the driver's exports.**
**ENG-ISP-01 Narrow contracts.** `mcp-client.mjs` exports only `init`, `callTool`, `shutdown`. `build-shorts.mjs` reads only `id`, `name`, `chunks` from `cut-plan.json` and only `name`, `scale`, `segs[].x`, `segs[].y` from `framing-table.json`. Diagnostic fields (`cuts`, `audibleGapsKept`, `px`, `median`) exist for people and may change without touching the builder.
Review/test: grep `build-shorts.mjs` for property reads on plan and framing objects; any field beyond the list is a finding.

**Dependency inversion: applies narrowly.**
**ENG-DIP-01 The builder depends on `callTool(name, args)`, not on the server.** The server location and protocol live only in `mcp-client.mjs`. Whether it reads the path from `.mcp.json` is OQ-09.
Review/test: `build-shorts.mjs` contains no `spawn`, no server path, no JSON-RPC.

### ACID: N/A as stated, with re-runnability and atomic writes specified

There is no database and no transaction. What matters instead:

**ENG-RERUN-01 Each phase is safe to rerun after a Premiere restart.** A phase keeps no memory between invocations; it rereads every node id through `get_sequence_structure` and every input from `outDir`. A phase is complete only once `save_project` returns. Per phase:

| Phase | Rerun behavior |
|---|---|
| `verify` | Read only. |
| `subclips` | Skip any subclip whose name already exists; create only missing ones. Does not duplicate. Current script duplicates; this is new. |
| `sequences` | Skip any sequence whose name already exists. Never deletes a 16:9 sequence, because it holds hand-added Lumetri. Current script duplicates; this is new. |
| `grade` | Idempotent by value; rewriting the same nine values changes nothing. |
| `verticals` | Deletes and recreates each `"<name> 9x16"`. This drops its caption track, so the phase ends by printing that `captions` must be rerun. |
| `captions` | Imports an SRT only if no item of that name is in the Captions bin. Attaches one caption track per 9:16 sequence; because CEP gives no readback (OQ-10), a rerun is only valid right after `verticals`. |
| `exports` | Overwrites each MP4; freshness checked per BR-19. |

Review/test: AC-05.

**ENG-ATOM-01 State files are replaced atomically.** `cut-plan.json`, `track-report.json`, `framing-table.json`, and each `srt/<id>.srt` are written to `<file>.tmp` in the same folder, then renamed over the target. `framing-table.json` is renamed last in the tracker so a crash never leaves a new report with an old table. Exports are written by Adobe Media Encoder and are outside this guarantee; BR-19 catches a stale one.
Review/test: AC-11; grep shows no `writeFileSync` to a final state path without a following `renameSync`.

**ENG-RERUN-02 Derived state is checked against the cut plan.** See BR-23. Review/test: AC-12.

### Big O: applies to the dead-air probe and the tracker

Model reasoning (clip selection, calibration, verification) has no complexity class and is not specified here.

**Dead-air probe.** Let W be the number of words in the whisper JSON, C the number of clips, and G the number of word gaps of at least `gapMin` inside clip windows. Sorting is O(W log W); windowing is O(C x W). The probe spawns one ffmpeg per candidate gap and decodes only that gap, so probe time grows as O(G x process start + total gap seconds). Process start dominates in practice (G was tens per clip for C0828).
**ENG-BIGO-01** The probe decodes only candidate gaps, never the whole recording; ffmpeg runs are sequential with at most one child alive; memory is O(W).
Review/test: count ffmpeg spawns in a run log; it equals G. Peak resident memory for C0828 stays under 200 MB.

**Frame-diff tracker.** Let S be the total kept seconds across clips, f the sample rate (`track.fps`, 3), and P the analysis frame size (384 x 216 = 82,944 pixels). Pixel work is O(S x f x P); for C0828 (about 790 kept seconds) that is about 2 x 10^8 pixel comparisons. Decoding is O(S x source fps x source frame size), because ffmpeg decodes every source frame before the fps filter drops most of them; decode dominates wall time. The percentile step sorts each segment's samples, O(n log n) with n = segment seconds x f.
**ENG-BIGO-02** Memory is bounded by one previous frame, one current frame, and the pending read buffer (O(P)), plus O(S x f) samples; no frames go to disk; one ffmpeg child at a time. The read buffer must not grow past two frames.
Review/test: peak resident memory is the same, within 20 percent, for SF11 (9.7 s) and SF05 (147.1 s). `outDir` holds no image files after `track`.

`make-srts.mjs` scans all words once per chunk, O(chunks x W). At C0828 sizes this is negligible and needs no requirement.

### Convention over configuration: applies

**ENG-CONV-01 Fixed names.** State files are always `outDir/cut-plan.json`, `outDir/track-report.json`, `outDir/framing-table.json`, `outDir/srt/<id>.srt`. The 16:9 sequence is named `name`; the vertical is `"<name> 9x16"`; subclips are `<id>_NN` (two-digit, from 01) in bin `Subclips`; captions go in bin `Captions`; both under `job.binName`. Exports are `outDir/<name> 9x16.mp4`. None of these are configurable.
Review/test: grep; none of these strings is read from the job file.

**ENG-CONV-02 Fixed order.** Steps and phases run in the order in Section 1. The phase builder exposes no way to run two phases in one call or to reorder them.
Review/test: the usage message lists phases in order; BR-20 checks input presence.

**ENG-CONV-03 Forward-slash paths.** The job file and scripts use forward slashes. Conversion to backslashes happens only at the MCP call boundary where Premiere requires it (caption import, export path), in `build-shorts.mjs`.
Review/test: `grep -n '\\\\' jobs/*/job.config.json` returns nothing; backslash conversion appears only in `build-shorts.mjs`.

**ENG-CONV-04 Exit codes.** 0 = done, 1 = failed, 2 = a person must do a named UI step, then rerun the same phase.
Review/test: AC-06 and AC-07 check codes.

## 7. Editor's bar (ED)

These are taste rules that cost money to learn. Each is traced to the LESSONS.md line that paid for it.

**ED-01 Rhythm: about 0.6 s of pause kept per cut. Applies.**
Requirement: `pad` (0.3) is kept on each side of every cut, so a cut pause plays as about 0.6 s; a cut under `minCut` (0.45 s) is skipped because it would read as a jump. The 0.6 s is derived from `pad` and is never stored separately (ENG-DRY-02).
Paid for by: LESSONS, Domain findings: "Cuts keep ~0.6s of pause so rhythm survives."
Test: BR-05 on every cut; AC-01 listen check.

**ED-02 Reactions: gap and level must both agree. Applies.**
Requirement: BR-02, BR-03, EX-01. `quietMeanDb` is calibrated per room against one known pause and one known reaction, and never reused from another job (ENG-DRY-01).
Paid for by: LESSONS, Domain findings: "Dead air = whisper word-gap ≥1.2s AND audio level ≤ threshold. Level alone cuts far-mic speech; gaps alone cut laughter/applause. Threshold is per-room." Also process lesson 4 (the XAVC audio probe returning nothing).
Test: AC-01, AC-08.

**ED-03 Grade: by value, one Lumetri instance. Applies.**
Requirement: BR-11 to BR-14, EX-03.
Paid for by: LESSONS, Domain findings: "Grade travels by value, not by copy ... paste ADDS a second Lumetri instead of replacing." Process lesson 3 (half a session on Paste Attributes) and process lesson 6 (double Lumetri on SF11, none on SF01).
Test: AC-03, AC-06.

**ED-04 Framing: static per segment, reframes on cuts, clamped. Applies.**
Requirement: BR-07 to BR-10, BR-15, EX-02.
Paid for by: LESSONS, Domain findings: "Speakers walk during silences, not while talking ... Per-segment static framing with reframes hidden on the cuts is both cheaper and better-looking than continuous tracking ... no wobble." And: "clamp positions so the crop stays inside media." Process lesson 2 (Auto Reframe run on all 11 before one frame was checked; the normalized-coordinates bug caught on the pilot's frame 1).
Test: AC-02, AC-09.

**ED-05 Captions: editable tracks plus sidecar SRT, never burned by the pipeline. Applies.**
Requirement: BR-16 to BR-18, EX-04.
Paid for by: LESSONS, Domain findings: "Attach as caption tracks (editable, styleable) + keep sidecar SRTs; burning happens at export after styling, which is a taste decision, not a pipeline step."
Test: AC-04.

## 8. Human steps

The Premiere 25.x API cannot do these. The pipeline names them and stops; it does not attempt a workaround.

**H-01 Add Lumetri Color to a clip.** The QE effect catalog is empty on 25.x, so no tool can add an effect.
How the pipeline reports it: `grade` tries every clip, then exits 2 and prints `NO LUMETRI ON <n> CLIPS`, one line per clip as `<sequence name> / <clip name>`, and the instruction to drag Lumetri Color from the Effects panel onto each listed clip, one clip per drag (a multi-select drop reaches only the drop target).
What the person does: for each listed sequence, open it, confirm the fronted tab is that sequence with a tab-bar zoom (not `set_active_sequence`'s `active:true`), drag once per listed clip, then rerun `grade`. Close the floating MCP panel during the drags, because it steals focus.
Evidence: AC-06.
Note: the current detection matches the error text `/QE|catalog/`. If the server phrases the error differently, the clip is thrown rather than listed. OQ-06 asks for a positive check instead.

**H-02 Load the MCP panel once per Premiere launch** (Window > Extensions > MCP for Adobe Premiere Pro). Reported by `verify` exiting 1 with the component status.

**H-03 Extract audio from Sony XAVC.** ffmpeg reads no audio packets from 'twos' PCM. The person runs `python C:/Users/Tyler/Scripts/mp4_pcm_fallback.py <clip> <out.wav>` and sets `audioWav`. Reported by AC-08's behavior: every gap kept, no cuts, and `dead-air` should say so (OQ-11).

**H-04 Calibrate the room.** `quietMeanDb` from one known pause and one known reaction; `track` geometry from one extracted frame. Reported by ENG-DRY-01 exiting 1 when absent.

**H-05 Style and burn captions.** Style the caption track once in Essential Graphics, then re-export with Burn Captions, or ship the sidecar SRTs. Outside the pipeline by EX-04.

**H-06 Pick the clips.** Outside the pipeline by EX-06.

## 9. Traceability

| ID | Enforced by (script) | Step or phase | Evidence |
|---|---|---|---|
| BR-01 | all five | all | AC-05, state file parse |
| BR-02 | dead-air.mjs | dead-air | AC-01 |
| BR-03 | dead-air.mjs | dead-air | AC-08 |
| BR-04 | dead-air.mjs | dead-air | AC-01 |
| BR-05 | dead-air.mjs | dead-air | AC-01 |
| BR-06 | dead-air.mjs | dead-air | AC-04 (chunk count), AC-01 |
| BR-07 | track-speaker.mjs, build-shorts.mjs | track, verticals | AC-02 |
| BR-08 | track-speaker.mjs | track | AC-02 |
| BR-09 | track-speaker.mjs | track | AC-09 |
| BR-10 | track-speaker.mjs, build-shorts.mjs | track, verticals | AC-02 |
| BR-11 | build-shorts.mjs | grade | AC-03 |
| BR-12 | build-shorts.mjs | grade | AC-03 (negative case) |
| BR-13 | build-shorts.mjs | grade | AC-03, AC-06 |
| BR-14 | build-shorts.mjs | grade | AC-06 |
| BR-15 | build-shorts.mjs | verticals | AC-02, AC-10 |
| BR-16 | make-srts.mjs | srt | AC-04 |
| BR-17 | make-srts.mjs | srt | AC-04 |
| BR-18 | build-shorts.mjs | captions | AC-04 |
| BR-19 | build-shorts.mjs | exports | AC-10 |
| BR-20 | build-shorts.mjs | every phase | AC-12, usage check |
| BR-21 | build-shorts.mjs | verticals, captions | AC-02, AC-04 (single-clip runs) |
| BR-22 | all five | all | AC-11 |
| BR-23 | build-shorts.mjs | verticals, captions | AC-12 |
| EX-01 | dead-air.mjs | dead-air | AC-01, AC-08 |
| EX-02 | track-speaker.mjs, build-shorts.mjs | track, verticals | grep, AC-02 |
| EX-03 | build-shorts.mjs | grade | grep, AC-03 |
| EX-04 | build-shorts.mjs | exports | AC-04 export frame |
| EX-05 | all five | all | AC-11 |
| EX-06 | none write clips | all | review |
| EX-07 | process rule | all | run notes (OQ-08) |
| EX-08 | track-speaker.mjs | track | review |
| EX-09 | all five | all | review |
| EX-10 | repo | n/a | file count, grep `class` |
| ENG-DRY-01 | dead-air.mjs, track-speaker.mjs, build-shorts.mjs | dead-air, track, grade | AC-07, grep |
| ENG-DRY-02 | all five | all | grep review |
| ENG-DRY-03 | build-shorts.mjs, track-speaker.mjs, make-srts.mjs | subclips, sequences, verticals, captions, exports | grep review |
| ENG-DRY-04 | dead-air.mjs, make-srts.mjs | dead-air, srt | side-by-side diff |
| ENG-SRP-01 | all five | all | grep imports |
| ENG-OCP-01 | build-shorts.mjs | all phases | diff review |
| ENG-ISP-01 | mcp-client.mjs, build-shorts.mjs | all phases | grep field reads |
| ENG-DIP-01 | mcp-client.mjs, build-shorts.mjs | all phases | grep |
| ENG-RERUN-01 | build-shorts.mjs | every phase | AC-05 |
| ENG-RERUN-02 | build-shorts.mjs | verticals, captions | AC-12 |
| ENG-ATOM-01 | dead-air.mjs, track-speaker.mjs, make-srts.mjs | dead-air, track, srt | AC-11 |
| ENG-BIGO-01 | dead-air.mjs | dead-air | spawn count, memory |
| ENG-BIGO-02 | track-speaker.mjs | track | memory SF11 vs SF05 |
| ENG-CONV-01 | build-shorts.mjs, all compute scripts | all | grep |
| ENG-CONV-02 | build-shorts.mjs | all phases | usage message |
| ENG-CONV-03 | build-shorts.mjs, job file | captions, exports | grep |
| ENG-CONV-04 | build-shorts.mjs, compute scripts | all | AC-06, AC-07 |
| ED-01 | dead-air.mjs | dead-air | AC-01 |
| ED-02 | dead-air.mjs | dead-air | AC-01, AC-08 |
| ED-03 | build-shorts.mjs | grade | AC-03, AC-06 |
| ED-04 | track-speaker.mjs, build-shorts.mjs | track, verticals | AC-02, AC-09 |
| ED-05 | make-srts.mjs, build-shorts.mjs | srt, captions, exports | AC-04 |
| H-01 | build-shorts.mjs | grade | AC-06 |

## 10. Known gaps between this spec and today's scripts

Listed so the implementation diff is small and reviewable: vertical clamp (BR-09); master grade completeness check (BR-12); Lumetri instance count (BR-13); export freshness (BR-19); input and staleness checks (BR-20, BR-23); single-clip pilot argument (BR-21); skip-if-exists in `subclips`, `sequences`, caption import (ENG-RERUN-01); room values without defaults (ENG-DRY-01); atomic writes (ENG-ATOM-01); bounded tracker read buffer (ENG-BIGO-02).

## 11. Open questions, with proposed defaults

**OQ-01 Naming.** `CLAUDE.md` calls `build-shorts.mjs` "the driver script"; this spec calls `mcp-client.mjs` the driver. Align `CLAUDE.md`?
Proposed default: yes, one-line edit to `CLAUDE.md` after approval, in its own commit.

**OQ-02 C0828 state files predate the scripts.** The files in `C:/Users/Tyler/Videos/C0828 Shorts/` use an older schema: `cut-plan.json` has no `cuts` or `audibleGapsKept`, and `framing-table.json` stores pixel `posX` rather than normalized `x`, `y`, `scale`. The scripts have never been run end to end against this job. `build-shorts.mjs verticals` would fail on these files.
Proposed default: leave the delivered folder untouched; run acceptance against a copy of the job file with `outDir` set to `C:/Users/Tyler/Videos/C0828 Shorts/spec-run/`, and treat any cut-plan difference from the delivered one as something to explain, not an automatic failure.

**OQ-03 Where do non-room defaults live?** Either the job file is the only home (scripts refuse missing keys) or each value has a default in exactly one script.
Proposed default: job file only. It is already complete for C0828 and the example documents every key; a refusal naming the key costs one edit.

**OQ-04 Pilot argument.** Add an optional clip id to `build-shorts.mjs` (for example `verticals SF11`) so one sequence is built before the batch?
Proposed default: yes, an optional third argument for `verticals`, `captions`, and `exports`; no argument means all clips.

**OQ-05 Duplicated whisper loader.** Keep two identical copies (EX-10 forbids a shared module) or let `mcp-client.mjs` export a loader?
Proposed default: two identical copies, checked by review. Putting word parsing in the driver would break ENG-SRP-01.

**OQ-06 Counting Lumetri instances.** Does premiere-pro-mcp v1.15.0 expose a per-clip effect list?
Proposed default: if a tool lists effects per clip, `grade` uses it to report zero or two instances positively (exit 2 for zero, exit 1 for two) instead of matching error text. If not, BR-13 is checked by Effect Controls screenshots on the pilot and a readback on every clip.

**OQ-07 Two presenters.** The Hackhers run needed per-clip tracker overrides (`xRange`, `weightScreen`). The current tracker has no per-clip override.
Proposed default: allow an optional `track` object on an individual clip in `job.clips`, merged over the job-level `track`. No new tracking logic.

**OQ-08 Enforcing EX-07.** Should the builder write a lock file in `outDir` while running?
Proposed default: no lock file; keep it a process rule, and have `build-shorts.mjs` print one line at start saying native premiere tools must not be used until it exits.

**OQ-09 Server path.** Read the MCP server path from `.mcp.json` rather than hardcoding it in `mcp-client.mjs`?
Proposed default: yes, read `.mcp.json` at the project root; exit 1 if the path does not exist.

**OQ-10 Caption track readback.** CEP gives no readback for `create_caption_track`, so a rerun of `captions` alone can stack a second track.
Proposed default: no tracking file. If `get_sequence_structure` reports caption tracks, `captions` skips any 9:16 sequence that already has one. If it does not, the rule is procedural: `verticals` ends by printing "rerun captions", and `captions` prints "only valid right after verticals".

**OQ-11 Silent audio.** When every probe returns null (the XAVC case), should `dead-air` exit rather than write a plan with no cuts?
Proposed default: yes, exit 1 naming `audioWav` and the fallback extractor when more than half the probes in the job return null.

**OQ-12 "Three words per cue" versus sentence breaks.** The brief says three words per cue; the script ends a cue early at sentence punctuation.
Proposed default: keep the script's behavior (at most three, fewer at sentence end), as written in BR-17.

**OQ-13 The SF09 laugh.** AC-01 needs the laugh's source timestamp in SF09 (389 to 479 s). It is not recorded anywhere in this folder.
Proposed default: take it from the regenerated cut plan's `audibleGapsKept` for SF09 and confirm it by ear once, then record it in the acceptance notes.

Waiting for approval before implementing.
