# Lessons learned — C0828 shorts run (Sep 8–10, 2026)

The first full run: install premiere-pro-mcp → find 11 clips → cut dead air →
grade → 9:16 tracked verticals → captions → 11 exported MP4s. It worked, but took
three sessions and ~600 tool calls. This file is why the pipeline looks the way it
does, so the next run doesn't re-litigate it.

## Process lessons (the expensive ones)

1. **One assistant turn per API call is the enemy.** The vertical build alone was
   ~110 round trips (11 sequences × 10 steps). The fix is `build-shorts.mjs`:
   the same MCP server driven from a Node script over stdio — one Bash call per
   phase. Spend model turns on decisions and verification, not repetition.

2. **Pilot → verify → rollout. Every time.** Auto Reframe was run on all 11
   sequences before looking at a single frame; all 11 analyses (plus 11 caption
   attaches to those sequences) were thrown away when frame 1 showed it tracking
   the audience. The custom framing rig was piloted on the shortest clip first,
   caught the normalized-coordinates bug on frame 1, and rolled out clean.
   "The tool returned ok" is not verification for anything visual.

3. **Two unexplained failures of the same approach = stop and run a decisive
   diagnostic, not variant #3.** The Lumetri copy/paste saga burned half a session:
   four paste-attributes variants, a Premiere restart, a focus-stealing hunt —
   when one Edit-menu check ("is Paste grayed?") pointed at the clipboard from the
   start. The eventual answer (UI drag to add + color_correct to set values) took
   ~15 calls once the dying path was abandoned.

4. **Check memory and known constraints before diagnosing from scratch.**
   `detect_silence` returning zero silences was already explained by a saved
   memory (Sony XAVC audio undecodable by ffmpeg) and the fix already existed on
   disk (`Scripts/mp4_pcm_fallback.py`). Two analysis passes were wasted
   rediscovering it.

5. **Durable state never goes in the session scratchpad.** `cut-plan.json` was
   wiped by scratchpad cleanup between sessions and had to be reconstructed from
   conversation history. Pipeline state lives with the job in `outDir`.

6. **Verify cheap, verify the right thing.** The wrong-sequence paste (double
   Lumetri on SF11, none on SF01) happened because `set_active_sequence` said
   `active:true` while the UI still fronted another tab. A 200-token zoom of the
   tab bar after each switch would have caught it; instead it cost a 40-call
   cleanup. Conversely: full-res screenshots everywhere cost ~4× what scale-0.4
   or a zoom region does — match the capture to the question.

7. **UI automation is the last resort, and when used, one gesture per batch with
   a screenshot between state changes.** Menu positions drift, tabs close when a
   click lands 8px off (the SF01 tab ✕), windows minimize, focus wanders. The
   API-only phases were boringly reliable; every incident happened in UI-land.

## Domain findings (why the pipeline is shaped like this)

- **Dead air = whisper word-gap ≥1.2s AND audio level ≤ threshold.** Level alone
  cuts far-mic speech; gaps alone cut laughter/applause. Threshold is per-room:
  calibrate against a known pause and a known reaction (C0828: −27.9 vs −24.1 dB,
  chose −26). Cuts keep ~0.6s of pause so rhythm survives.
- **Speakers walk during silences, not while talking.** Motion tracking across
  ~790s of kept footage showed every real relocation had already been cut with
  the dead air. Per-segment static framing with reframes hidden on the cuts is
  both cheaper and better-looking than continuous tracking — no wobble.
- **Adobe Auto Reframe is saliency-based and untunable via API**; on a wide room
  shot it locks onto foreground audience and the slide screen, and it never
  punches in. The frame-diff tracker works because the room is static: exclude
  ceiling/audience bands, down-weight the slide screen ×0.12 (terminal scrolling
  otherwise hijacks the centroid), centroid of what moves = the speaker.
- **Grade travels by value, not by copy.** Read the 9 Basic Correction numbers
  off the master clip once (`get_effect_properties`), write them to every clip
  (`color_correct`). Copy/paste-attributes is fragile (clipboard, cross-sequence
  lookups, effect stacking — paste ADDS a second Lumetri instead of replacing).
- **1215×2160 sequences beat 1080×1920**: full source-height 9:16 crop, no
  quality loss, platforms downscale on upload. Scale 170% ≈ chest-up framing for
  a mid-distance speaker in 4K; clamp positions so the crop stays inside media.
- **Captions**: 3-word cues from whisper word timings, remapped through the cuts
  (per-chunk offset arithmetic — see make-srts.mjs). Attach as caption tracks
  (editable, styleable) + keep sidecar SRTs; burning happens at export after
  styling, which is a taste decision, not a pipeline step.

## Environment facts worth remembering

- premiere-pro-mcp v1.15.0, CEP bridge, Premiere Pro 2025 (25.x), Windows 11.
- Registry writes and some PowerShell one-liners get blocked by the permission
  classifier — split installers into file ops (allowed) + a user-clickable
  command block for the gated part.
- The full API landmine list lives in CLAUDE.md; the auto-memory copies are
  `premiere-pro-mcp-setup` and `premiere-mcp-quirks`.
