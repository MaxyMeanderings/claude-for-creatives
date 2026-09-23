# Build brief: the shorts pipeline

> Written September 23, 2026, after the first run (September 8 to 10) and from its retro, `LESSONS.md` (September 11). Run one had no brief; it had "cut out the dead air for each clip" and "have it track me as I walk" typed into Claude Code, about six hundred tool calls across three sessions, and eleven shipped shorts. Every line below was paid for in that run. Run two starts here.

## Who it helps, and the moment it helps them

A solo creator or a speaker who does their own editing, the week after a two-hour talk or workshop was recorded on one wide camera. They have a transcript, a Premiere Pro project with the recording on the timeline, and eleven moments they already know they want to post. They do not have an afternoon to click through the same ten steps eleven times.

## What it must do

- Take a list of clips (source in and out points, a name each) from a job file. Selection stays human.
- Remove dead air inside each clip and keep every laugh and reaction. A pause is dead only when the transcript shows a word gap of at least 1.2 seconds **and** the audio in that gap reads as room tone for this room. Keep about 0.6 seconds of each cut pause so the rhythm survives.
- Match the grade on every clip to the master sequence by carrying the nine Basic Correction values, not by copying and pasting effects.
- Produce a 9:16 version of every clip with the speaker framed chest-up and no wobble: one static position per kept segment, reframes landing on the cuts, positions clamped so the crop never leaves the media.
- Produce captions from the transcript's word timings, remapped through the cuts, three words per cue, attached as editable caption tracks with sidecar SRT files.
- Export every 9:16 sequence with one preset to one output folder, and leave every intermediate file (cut plan, framing table, SRTs) next to the exports.
- Run one phase at a time, in order, and be re-runnable from any phase after a Premiere restart.

## What it must never do

- Cut a gap on audio level alone or on transcript gap alone.
- Trust a crop, a caption track, or a grade it has not shown in a captured frame. Pilot one sequence at two or three timestamps before any batch.
- Use Adobe Auto Reframe on a wide room shot.
- Copy or paste effects between clips, or build any step on the Premiere clipboard.
- Burn captions into the export. Styling is a taste decision made once in Essential Graphics, then re-exported.
- Keep state in a session scratch folder. Everything durable lives in the job's output folder.
- Run the scripted driver and the native Premiere tools at the same moment.

## What it produces

- `cut-plan.json`: per clip, the kept chunks in source seconds and the trimmed duration. The record of every cut.
- `framing-table.json`: per clip, per segment, the measured speaker position and the Motion Position written to Premiere.
- `srt/<id>.srt`: one caption file per clip, timings already remapped through the cuts.
- `<name> 9x16.mp4`: one export per clip, 1215×2160, in the job's output folder.
- The job file itself, `jobs/<name>/job.config.json`, which is the only thing that changes for the next recording.

## How you will know it worked

| Input | Pass looks like | Visible failure |
|---|---|---|
| "Cut the dead air out of SF09. There is a laugh in it." | The laugh survives; only gaps the transcript and the audio both call dead are shortened, to about 0.6 s | The laugh is gone, or a far-mic sentence is cut because it was quiet |
| "Make SF11 vertical and keep me in frame." | Frame 1, the midpoint, and the last frame each show the speaker chest-up with no black | The audience or the slide screen in frame; a black frame; a drifting crop |
| "Match every clip to the master grade." | The same nine Basic Correction values on all eleven, one Lumetri instance each | A clip with no grade, a clip with two Lumetri instances, values that differ |
| "Caption SF01. It has nine cuts in it." | Cue times shift by exactly the removed seconds at each cut; three words per cue | Captions drift after the first cut; captions burned into the export |

## Out of scope

- Picking the clips. The transcript is read and the moments are chosen by a person (or by Claude in conversation), then written into the job file.
- Multi-camera, multi-speaker, or moving-camera footage. The tracker assumes a static wide shot.
- Caption styling, thumbnails, titles, music, and posting.
- Anything the Premiere 25.x API cannot do. Where it cannot (adding an effect), the pipeline names the clips and stops so a person can do the one gesture.
