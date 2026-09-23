# Claude for Creatives · Building Your Own Editing Workflows Using Claude Code

A thirty-minute session for Claude for Creatives, Atlanta, September 23, 2026, by Tyler Sztuka (SZD Labs, Claude Community Ambassador).

The point: stop chatting with Claude and start standing up systems for your own craft. One worked example: a two-hour workshop recording cut into eleven vertical shorts inside Premiere Pro, with Claude Code driving the timeline through an MCP bridge, a scripted pipeline doing the repetition, and every decision's evidence left on disk as a frame or a file. The example is an edit. The loop is what you take home.

- **Slides:** https://maxymeanderings.github.io/claude-for-creatives/
- **Slides as PDF:** https://maxymeanderings.github.io/claude-for-creatives/slides.pdf (also attached to the [latest release](https://github.com/MaxyMeanderings/claude-for-creatives/releases/latest))
- **The spec:** [`SPEC.md`](SPEC.md)
- **The brief and the retro it was drafted from:** [`BUILD-BRIEF.md`](BUILD-BRIEF.md), [`LESSONS.md`](LESSONS.md)
- **Yesterday's example, same loop, different domain:** [`coach/SPEC.md`](coach/SPEC.md)
- **The loop itself, with the build steps and the one-page brief template:** https://github.com/MaxyMeanderings/claude-code-build-loop

## The loop

```
Brief → Spec → Approve → Implement → Challenge → Repair
```

Every step leaves a file or a frame you can point at. In an edit, the challenger is a captured frame at named timestamps. A tool returning `ok` is not evidence for anything visual.

## What is in here

| File | What it is |
|---|---|
| `SPEC.md` | The spec for run two of the shorts pipeline. Drafted headlessly by Claude Code on September 23, 2026 from the brief, the retro and the pipeline as it stood, using the workshop's Specify prompt adapted to a scripted editing pipeline. Evidence rules, numbered behavior requirements, exclusions, eleven acceptance clips with frames as evidence, an engineering bar, an editor's bar (rhythm, reactions, grade, framing, captions), human steps, traceability, a known-gaps list and thirteen open questions with proposed defaults. **Not approved, not implemented.** |
| `BUILD-BRIEF.md` | The one-page behavior brief for the pipeline. Written September 23 from the retro. Run one had no brief. |
| `LESSONS.md` | The retro written the night run one shipped (September 11): seven process lessons and six domain findings, each with the cost it carried. |
| `coach/SPEC.md` | The spec from the September 22 session, a "should I build this?" coach for domain experts, copied from the loop repo so the two specs can be read side by side. |
| `slides/` | The Slidev deck: `slides.md`, its stylesheet, components, and the frames and QR codes it uses. `npm install` then `npm run dev` in that folder. |

## What is real and what is labeled

- **Run one (September 8 to 10)** was done by hand: Claude Code driving Premiere Pro 2025 through [premiere-pro-mcp](https://github.com/leancoderkavy/premiere-pro-mcp) one call at a time, about six hundred tool calls across three sessions. It shipped eleven shorts. Its failures on the slides are real and quoted verbatim from `LESSONS.md` and the session: Adobe Auto Reframe tracking the audience, a normalized-coordinate black frame, the clipboard dying four ways, a wiped scratch folder.
- **The retro** became five scripts and one job file the same night. Those scripts have not yet been run end to end on the original recording; `SPEC.md` section 10 says exactly where they fall short of their own spec.
- **The brief and the spec** were written on September 23, after the fact, and the slides say so where they appear.
- **Every frame on the slides** is extracted with ffmpeg from the real source recording or the real exported shorts. The discarded Auto Reframe renders were never saved.

## Tools

Claude Code, the [premiere-pro-mcp](https://github.com/leancoderkavy/premiere-pro-mcp) server (MIT), and Adobe Premiere Pro 2025. Logos on the tools slide are the marks of Anthropic, the Model Context Protocol project and Adobe; the first two are from Simple Icons (CC0), the third from Wikimedia Commons.

## License

MIT. Everything here is yours to copy, including the spec's shape. Swap the brief for your own edit.
