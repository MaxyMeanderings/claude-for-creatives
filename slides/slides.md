---
theme: default
title: Building Your Own Editing Workflows Using Claude Code
info: Claude for creatives. How to stand up an editing pipeline for yourself with Claude Code and Premiere Pro. One worked example, one loop you take home. Atlanta, Sept 23, 2026.
colorSchema: dark
aspectRatio: 16/9
canvasWidth: 1280
fonts:
  sans: Source Code Pro
  serif: Source Code Pro
  mono: Source Code Pro
  local: Source Code Pro
drawings:
  persist: false
transition: none
mdc: true
rail:
  - { label: 'Start', start: 1 }
  - { label: 'Mindset', start: 5 }
  - { label: 'Brief → spec', start: 12 }
  - { label: 'Build', start: 16 }
  - { label: 'Test & repair', start: 19 }
  - { label: 'Take it home', start: 30 }
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Building Your Own Editing<br>Workflows Using Claude Code.

<div class="cover-mark"><img src="/assets/claude-logo.svg" alt="Claude" /></div><div class="hero-sub">Stop chatting with Claude and start standing up systems for your own craft. One worked example: a two-hour workshop recording cut into eleven vertical shorts inside Premiere Pro, with Claude driving the timeline, a scripted pipeline doing the repetition, and every decision’s evidence left on disk.</div>

<img class="cover-szd" src="/assets/szd-logo.png" alt="SZD Labs" />

<div class="source">Claude for Creatives · Atlanta · September 23, 2026</div>

<!--
Opening, in my words: this is about building with Claude Code, not chatting with Claude. Standing up systems for your own craft. Yesterday I gave a version of this to a technical room with a coach as the example. Today the example is an edit: my own workshop recording, cut into eleven shorts inside Premiere Pro, with Claude driving the timeline. Same loop underneath. I could show you the finished clips and stop. That's less useful than something you take away and use, because your edit isn't mine. So: one worked example, the loop underneath it, and a brief template you write for your own thing tonight.
-->

---
class: presenters-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Your guide today

<div class="presenters">
<div class="presenter"><img src="/assets/tyler.jpeg" alt="Tyler Sztuka" /><div><h3>Tyler Sztuka</h3><p class="role">Founder, SZD Labs · Claude Community Ambassador</p><p>SZD Labs is an applied AI implementation and upskilling firm focused on:</p><ul class="focus"><li>Non-technical domain expert upskilling</li><li>Anthropic platform implementation</li><li>AI-augmented SDLC</li><li>Enterprise AI token spend and performance optimization</li></ul></div></div>
</div>

<div class="intro-linkedin"><a href="https://www.linkedin.com/in/tyler-sztuka-283937123/" target="_blank" rel="noopener noreferrer">linkedin.com/in/tyler-sztuka-283937123 ↗</a><figure class="qr"><img src="/assets/creatives/qr/qr-linkedin.svg" alt="QR code for https://www.linkedin.com/in/tyler-sztuka-283937123/" /></figure></div>

<div class="source"><a href="https://szdlabs.io/">szdlabs.io</a> · <a href="https://github.com/MaxyMeanderings/claude-for-creatives" target="_blank" rel="noopener noreferrer">github.com/MaxyMeanderings/claude-for-creatives</a></div>

<!--
Name the ambassador bias out loud. Then the next slide: the production background, so the room knows the edit on these slides is judged by someone who has done every one of those steps by hand.
-->

---
class: content bio-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Over a decade in media production.

<div class="three"><section><h3>Studio and social</h3><p>Over a decade of professional media production. In college I ran a photography studio. I've done a lot of organic social media content creation.</p></section><section><h3>The agency</h3><p>Most recently I ran a creative agency: short-form to medium- and long-form market penetration for folks who had a presence on short-form and wanted to jump into YouTube. Full production, pre and post, a full in-house content studio. Filming, editing, scripting, everything.</p></section><section><h3>The channel</h3><p>A personal YouTube channel I grew to over 5,000 subscribers. Monetized, with sponsors.</p></section></div><div class="takeaway">My business today is fundamentally different, outside the creative industries, and I still do a lot of content creation and production for my personal brand and for the business. At least a decade of Adobe Creative Cloud: Photoshop, Lightroom, Premiere, After Effects, InDesign, Dreamweaver, Illustrator.</div>

<div class="source">I am very versed in media production.</div>

<!--
Thirty seconds. This is why the example is an edit and why the verdicts on the frames are mine: over a decade of professional media production, the studio, the agency work for creators moving from short-form to YouTube, the channel. My business today is fundamentally different, but I still produce for it. The pipeline exists because, even knowing every step, clicking through the same ten of them eleven times is the job nobody wants.
-->

---
class: content access-slide qr-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Open the workshop.

<Links />

<div class="source">Type either one now. The spec, the brief, the retro, and these slides live there. You will want them tonight.</div>

---
class: content copilot-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Copilot vs autopilot: own the cut.

<div class="copilot-content"><div class="two"><section><h3>Copilot · stay engaged</h3><p>Frame the edit and state your taste out loud.</p><p>Ask AI for options, explanations, and challenges.</p><p>Look at the frame. Explain why you accept or change the result.</p></section><section><h3>Autopilot · the trap</h3><p>Hand over the framing before you know what you want.</p><p>Accept “ok” from a tool without looking at a frame.</p><p>Struggle to explain the cut when a client challenges it.</p></section></div><a class="post-thumbnail" href="./assets/copilot-autopilot-post.png" target="_blank" rel="noopener noreferrer" aria-label="Open Chorouk Malmoum’s original post image in a new tab"><img src="/assets/copilot-autopilot-post.png" alt="Screenshot of Chorouk Malmoum’s LinkedIn post contrasting copilot and autopilot approaches to AI." /><span>Read original post ↗<br>Opens full size in a new tab</span></a></div><div class="takeaway">Before you export: can you explain the cut, show the frame, and name what would change your mind?</div>

<div class="source">Inspired by Chorouk Malmoum’s LinkedIn post · supplied screenshot · workshop adaptation</div>

<!--
One minute. Copilot is a metaphor, not a product. Automation can run on its own inside clear bounds; you still own the taste, the acceptance criteria, and the consequential decisions. In an edit the acceptance criterion is a frame you looked at. The takeaway line is the whole talk. Everything after this is the loop that lets you answer it.
-->

---
class: content tools-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# The tools we're using today.

<div class="tools"><div class="tool"><img src="/assets/creatives/logos/claude-mark.svg" alt="Claude" /><h3>Claude Code</h3><p>Runs the loop. Reads the folder, writes the files, drives the bridge.</p></div><div class="tool"><img src="/assets/creatives/logos/mcp-mark.svg" alt="Model Context Protocol" /><h3>Premiere Pro MCP server</h3><p>The bridge. About 367 tools that let Claude Code talk to a running Premiere.</p></div><div class="tool"><img src="/assets/creatives/logos/premiere-pro.svg" alt="Adobe Premiere Pro" /><h3>Adobe Premiere Pro</h3><p>Where the edit lives. Same project, same timeline, same exports.</p></div></div><div class="takeaway">Three things. No plugin store, no new app, nothing you have to leave Premiere for.</div>

<div class="source">premiere-pro-mcp by leancoderkavy, MIT · github.com/leancoderkavy/premiere-pro-mcp · logos are the marks of Anthropic, the Model Context Protocol project, and Adobe</div>

<!--
Twenty seconds. Three things and nothing else: Claude Code is the harness that runs the loop; the MCP server is an open-source bridge, a Node process plus a panel inside Premiere, that gives Claude Code a few hundred tools against the running app; Premiere is Premiere. Everything on the following slides happens across those three.
-->

---
class: content mindset-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Hiring an editor, not outsourcing the edit.

<div class="two"><div class="artifact"><label>MY LEAST FAVORITE PART OF ANY PRODUCTION</label><blockquote>The rough cut. Clips into the timeline, sequencing everything together. Razor tool, razor tool, razor tool. What you learn through that is what a good edit looks like, when and where to cut, and how the audio and visual elements flow together.</blockquote></div><div><h3>Augment, not replace</h3><p>We are using AI to augment the editing production cycle. We are still in the driver’s seat. What we’re doing is offloading the rote, tedious editing work.</p><h3>Domain experience is still required</h3><p>You can’t do this without understanding editing, cinematography, and what good actually looks like.</p><h3>The judgment stays yours</h3><p>That creative discernment is something the AI is really not good at. You still need those priors, and they get wrapped into the spec and the evals as we build: your edits, your audience, your platforms.</p></div></div><div class="takeaway">This is not creative outsourcing. This is hiring an editor.</div>

<div class="source">Mindset, one of two · before the build</div>

<!--
Ninety seconds, in my words. We are using AI to augment the editing production cycle; that is what this is an example of. What I want to highlight before we get into it: domain experience is still required. You can't do this without understanding editing, cinematography, and what good actually looks like. We are still in the driver's seat; we are offloading the rote, tedious work. My least favorite part of any production was the rough cut, clips into the timeline, razor tool, razor tool, razor tool. What you learn through that process is what a good edit looks like, when and where to cut, how the audio and visual flow together. That judgment is something the AI is really not good at. You still need those priors, and they get wrapped into the spec and the evals as we build it out. It is highly dependent on your own edits, what you're producing, who the audience is, what platforms it's going to. This is not creative outsourcing. This is hiring an editor.
-->

---
class: content cost-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# This is not cheap. And it is not the optimal setup.

<div class="two"><section><h3>Not cheap</h3><div class="stat">100,000+</div><p class="stat-sub">tokens per edit, on average, with Opus</p><p>Having the AI go through and edit clips in the timeline, even in this refined state, is expensive. At API rates, that’s expensive. That’s why the refinement loops matter: they save you on cost.</p></section><section><h3>Not optimal</h3><p>This setup is not optimal for this. Claude Code, a harness designed for coding, talking through an MCP server to a legacy editing platform, is probably the least optimal AI editing arrangement I can think of.</p><p>Again, that’s beside the point.</p></section></div><div class="takeaway">That is why these refinement loops are important. The setup is beside the point.</div>

<div class="source">Mindset, two of two · the token figure is my own average across the C0828 edits</div>

<!--
Forty-five seconds, in my words. This process is not cheap. Having the AI, even in this refined state, go through and edit clips in the timeline is expensive: on average, with Opus, a hundred thousand plus tokens per edit, and at API rates that adds up. That's why these refinement loops are important; they save you on cost. And, beside the point, this setup is not optimal for this. Claude Code, a harness designed for coding, interacting through an MCP server with a legacy editing platform, is probably the least optimal AI editing arrangement I can think of. Again, beside the point. The loop is the point.
-->

---
class: content what-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# What we're building.

<div class="four"><section><h3>The thing</h3><p>A shorts pipeline: five small scripts and one job file that drive Premiere Pro through its MCP bridge. Claude Code runs it from a folder. No plugin store, no new app.</p></section><section><h3>Its purpose</h3><p>Turn a long recording into publishable vertical clips without clicking through the same ten steps eleven times. Selection stays human. Repetition goes to the script.</p></section><section><h3>Who it's for</h3><p>A creator or editor who owns the edit: you pick the moments, set the look, and judge every frame. No coding background to run it. Some to change it.</p></section><section><h3>The outcome</h3><p>Eleven 9:16 shorts from one recording: dead air cut, graded to match, framed on the speaker, captioned, exported. Plus a job file that runs the next one.</p></section></div><figure class="strip"><img src="/assets/creatives/eleven-shorts.jpg" alt="First frames of the eleven exported vertical shorts, side by side" /><figcaption>The eleven exports · Videos/C0828 Shorts · Sept 10, 2026 · one frame from each, three seconds in</figcaption></figure>

<div class="source">premiere-ops/BUILD-BRIEF.md · pipeline/ · jobs/c0828/job.config.json</div>

<!--
One minute. Name the thing before the loop: a pipeline that turns my two-hour marketers workshop into eleven shorts, inside Premiere, with the taste decisions still mine. The strip is the real output, not a mockup. Three rules to plant now, because we test them later: it never cuts a laugh, it never trusts a crop it has not looked at, and it never burns a caption you cannot restyle.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# One worked example. Your loop.

<div class="three"><section><h3>Specify</h3><p>Turn a one-page brief into observable requirements: what a cut is, what a good frame is, what done looks like. Nothing gets built yet.</p></section><section><h3>Implement</h3><p>Claude Code creates the scripts and the job file from the approved spec. You inspect what changed.</p></section><section><h3>Demonstrate</h3><p>Run it on one clip, look at the frame, repair a failure, and explain the evidence.</p></section></div><div class="takeaway">The example is a shorts pipeline, the smallest thing that still needs a spec and tests. The same loop builds a caption pass, a color match, a photo cull. The loop is what you leave with.</div>

<div class="source">premiere-ops/BUILD-BRIEF.md · LESSONS.md</div>

<!--
Say plainly how this really went. Run one, September 8 to 10: Claude drove Premiere one call at a time, about six hundred tool calls across three sessions, and it shipped eleven shorts. That night I asked it what it learned. The retro became the spec, and the pipeline is run two. Every frame and file on these slides is from the real run; nothing is staged. Say the thesis once here: I could show you the finished clips; that's less useful than what you can take away and use.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Build it in six visible steps.

<div class="build-strip">Brief → Spec → Approve → Implement → Challenge → Repair</div><div class="three"><section><h3>Input</h3><p>A one-page brief: which recording, what a clip is, what must never be cut or invented.</p></section><section><h3>Files</h3><p>A spec, a job file, five scripts, and a list of clips to pilot before any batch.</p></section><section><h3>Evidence</h3><p>Frames captured from the timeline, compared with each requirement.</p></section></div><div class="takeaway">Swap the brief for your own edit. The six steps do not change.</div>

<div class="source">Workshop walkthrough · the loop repo</div>

<!--
Every step leaves a file or a frame you can point at. That is the difference between a demo and evidence.
-->

---
class: content brief-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Start with a behavior brief.

<div class="artifact"><label>BUILD-BRIEF.md · THE INPUT</label><p>Cut a two-hour workshop recording into eleven vertical shorts inside Premiere Pro, for a solo creator who does their own editing. Selection stays human; the repetition does not.</p><ul><li>Remove dead air, keep every laugh and reaction. A pause is dead only when the transcript and the audio both say so.</li><li>Match the grade on every clip to the master. Carry values, not copies.</li><li>Frame the speaker in 9:16 without wobble. Never trust a crop you have not looked at.</li><li>Captions from the transcript, remapped through the cuts. Editable, not burned.</li><li>Export all eleven from one job file. The next recording is a new job file, not a new conversation.</li></ul></div>

<div class="source">premiere-ops/BUILD-BRIEF.md, written Sept 23 from the first run’s retro (LESSONS.md, Sept 11) · run one had no brief. That is the point.</div>

<!--
Say it plainly: the first run did not have this page. It had me typing "cut out the dead air" and "have it track me as I walk." Every line here was paid for in the first run and written down in the retro. Run two starts from this page. Ask the room: which line here is hardest to test?
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# What a conversation looks like.

<div class="conversation"><div class="student"><label>ME · RECORDED · SEPT 10, 2026, 11:40 PM</label><p>“yep, do those, also have it track me as I walk”</p></div><div class="coach"><label>CLAUDE CODE · RECORDED RESPONSE EXCERPTS · SEPT 11, 12:25 AM</label><p>“9:16 with tracking — but not Adobe’s. Auto Reframe turned out to be useless on this footage: it locked onto the foreground audience and the slide screen instead of you, and it never punches in (I verified with frame captures before scrapping it).”</p><p>“Each segment gets a 1.7x punch-in framed on your measured position, with reframes landing exactly on the cuts, which is cleaner than a drifting auto-crop.”</p></div></div>

<!--
Read it aloud. Ask: what did it refuse to trust? The tool's own ok. It looked at frames before it believed the tracker, and it threw the tracker away. These are exact excerpts from the recorded session, with intervening text omitted. Say "recorded, not live" out loud.
-->

---
class: content spec-prompt
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Ask for a spec before code.

<div class="terminal"><div class="terminal-label">Type in Claude Code · in your folder</div><pre>Read BUILD-BRIEF.md and LESSONS.md. Draft SPEC.md; do not implement yet.
Number requirements, exclusions, and acceptance clips.
Map each requirement to a script, a phase, and the frame that proves it.
Ask about consequential gaps. Wait for our approval.</pre></div><p class="checkpoint-help">This is the short form. The full Specify prompt, with the quality bar it must assess, is in <a href="https://github.com/MaxyMeanderings/claude-code-build-loop/blob/main/coach/BUILD-STEPS.md" target="_blank">coach/BUILD-STEPS.md ↗</a> in the loop repo. Next slide.</p>

<div class="takeaway">Which requirement would you try to break? Hold that thought. We run the one most editors pick: the crop.</div>

<div class="source">coach/BUILD-STEPS.md § 2 · premiere-ops/SPEC.md</div>

<!--
Switch to the folder. Open SPEC.md and scroll the requirements and the questions it asked before building. Say "drafted this afternoon from this prompt, against the brief and the retro." Ask which requirement they would try to break, hands only. We run the crop.
-->

---
class: content spec-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Where the spec comes from.

<div class="three"><section><h3>The brief · what it must do</h3><p>BUILD-BRIEF.md, one page. Every rule the pipeline enforces, from “keep the laugh” to “one job file per recording,” traces back to a line in it.</p></section><section><h3>The retro · what run one taught</h3><p>LESSONS.md, written the night run one shipped. Seven process lessons and six domain findings, each with the cost it carried: Auto Reframe, the clipboard, the normalized coordinates, the wiped scratchpad.</p></section><section><h3>Its questions · what you decide</h3><p>The numbers only you can set: the quiet threshold for this room, the punch-in scale, where the slide screen sits, words per caption cue. Answered once in the job file. Changing one is a new run, not a new build.</p></section></div><div class="bar"><div><b>Rhythm</b><span>applies · a cut keeps about 0.6 s of pause</span><span class="src">LESSONS.md · dead air</span></div><div><b>Reactions</b><span>applies · gap ≥ 1.2 s and room tone, never level alone</span><span class="src">LESSONS.md · dead air</span></div><div><b>Grade</b><span>by value · nine numbers read once, written to every clip</span><span class="src">LESSONS.md · grade travels by value</span></div><div><b>Framing</b><span>no wobble · static per segment, reframes on the cuts</span><span class="src">LESSONS.md · speakers walk during silences</span></div><div><b>Captions</b><span>editable · caption tracks plus sidecar SRTs; burn is taste</span><span class="src">LESSONS.md · captions</span></div></div><div class="takeaway">The brief is yours. So is the bar: yesterday’s room used DRY and SOLID; an editor’s bar is rhythm, reactions, grade, framing, captions. The builder asks about the rest.</div>

<div class="source">premiere-ops/BUILD-BRIEF.md · LESSONS.md § domain findings · jobs/c0828/job.config.json</div>

<!--
This is the slide that answers "where did all of that come from." Three inputs, not one. The five tiles are the editor's quality bar, and every one of them was learned the expensive way in run one: rhythm from the cuts that felt clipped, reactions from the laugh that a level-only pass would have removed, grade from the clipboard saga, framing from Auto Reframe, captions from the burn-or-not question. Same mechanism as any build: the brief says what, the bar says what good means, and the builder asks about the numbers only you can set.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Review and approve the contract.

<div class="artifact"><label>REVIEW THE SPEC</label><p>Whose edit is this, and what stays human?<br>What counts as dead air, a matching grade, a good frame?<br>What must it never do without a frame to prove it?<br>Which clip would expose a failure?</p></div><div class="terminal"><div class="terminal-label">Type in Claude Code</div><pre>Approved: implement only the requirements in SPEC.md.
First list the scripts you will write and the phase each one owns.
Pilot on the shortest clip before any batch.</pre></div>

<div class="source">coach/BUILD-STEPS.md § 3 · premiere-ops/CLAUDE.md, the pilot rule</div>

<!--
A prompt approval is a workflow instruction, not a security boundary. The approval is the moment you stop being on autopilot: you read the contract before the build. The last line is the one that would have saved half of run one.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Watch the files take shape.

<div class="two"><div class="artifact"><label>MY BUILD · premiere-ops · SEPT 11</label><pre>CLAUDE.md
LESSONS.md
pipeline/
  mcp-client.mjs
  dead-air.mjs
  track-speaker.mjs
  make-srts.mjs
  build-shorts.mjs
jobs/c0828/job.config.json
Videos/C0828 Shorts/
  cut-plan.json  framing-table.json
  srt/SF01.srt … SF11.srt
  SF01 … SF11 9x16.mp4</pre></div><div><h3>Inspect what changed</h3><p>One driver talks to Premiere. Four scripts decide nothing; they compute cuts, positions, and cues from the transcript and the audio.</p><p>The job file is the only thing that changes between recordings.</p><p>The state files are the evidence. They live next to the exports, never in a scratch folder that gets wiped.</p></div></div>

<div class="source">premiere-ops/ · every file shown is there</div>

<!--
Open the actual folder on screen. Files on disk are not proof of behavior; that is the next slides.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Read the instruction that matters.

<div class="artifact"><label>MY BUILD · CLAUDE.md:41 · THE PILOT RULE · EXACT EXCERPT</label><blockquote>Pilot rule: after <code>verticals</code>, <code>capture_frame</code> ONE sequence at 2–3 timestamps before trusting the batch. Same after <code>captions</code>.</blockquote></div><div class="artifact"><label>LESSONS.md · LESSON 2 · THE LINE THAT PAID FOR IT</label><blockquote>“The tool returned ok” is not verification for anything visual.</blockquote></div><div class="takeaway">Now design a run that tempts it to skip this.</div>

<div class="source">CLAUDE.md, Workflow step 6 → LESSONS.md, lesson 2: “The tool returned ok” is not verification for anything visual.</div>

<!--
Open CLAUDE.md and show the line. Trace it: the rule in the workflow, the lesson that paid for it, and the phase in build-shorts.mjs where it applies. "The tool returned ok is not verification for anything visual" is the line to read aloud. That trail is what "own the evidence" means in an edit.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Test behavior, not just the file.

<div class="test-grid"><section><label>INPUT · PICK ONE, OR BRING YOUR OWN</label><p>“Cut the dead air out of SF09. There is a laugh in it.”</p><p>“Make SF11 vertical and keep me in frame.”</p><p>“Match every clip to the master grade.”</p><p>“Caption SF01. It has nine cuts in it.”</p></section><section><label>WHAT YOU LOOK FOR</label><p>The laugh survives. Only pauses the transcript and the audio both call dead are shortened, to about 0.6 s.</p><p>Frame 1, the midpoint, the last frame: the speaker, chest-up, no black. Not the audience, not the slide screen.</p><p>Nine Basic Correction values identical on all eleven, one Lumetri instance each. No paste.</p><p>Cue timings shift by exactly the removed seconds at each cut. Three words per cue.</p></section></div><div class="takeaway">Recorded Sept 9 to 11 on the real project. The next slides are the run: frames from the timeline, files from the job folder.</div>

<div class="source">premiere-ops · cut-plan.json · framing-table.json · srt/ · LESSONS.md</div>

<!--
Each one is a requirement from the brief, with the visible failure written down before the test. The recorded run hit the second one hardest. Read the "what you look for" line for it before advancing, so the room judges the next frames against a written expectation, not a feeling.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 1 of 7</div>

# The dead-air pass.

<div class="terminal"><div class="terminal-label">cut-plan.json · SF01 and SF09 · exact · Videos/C0828 Shorts</div><pre>{"id":"SF01","name":"SF01 Ownership story","trimmedDur":100.9,
 "chunks":[[1763,1768.89],[1774.15,1780.5],[1781.28,1790.37],[1791.09,1798.63],
           [1799.47,1820.57],[1821.17,1840.76],[1842.76,1858.79],[1859.52,1864.34],
           [1865.58,1868.24],[1871.04,1879]]}
{"id":"SF09","name":"SF09 Not a professional dev","trimmedDur":87.6,
 "chunks":[[389,398.9],[399.7,438.95],[440.58,479]]}</pre></div>
<p class="interface-caption">SF01 loses fifteen seconds across nine cuts, including a six-second walking pause at 29:28. SF09 keeps its laugh: that gap read −24.1 dB, above the room’s −26 threshold, so it stayed. Twenty-nine cuts, about forty seconds, across eleven clips.</p>

<div class="source">Recorded 2026-09-09 · cut-plan.json verbatim · thresholds calibrated on this room: dead air ≤ −27.9 dB, reactions ≥ −24.1 dB · pipeline/dead-air.mjs</div>

<!--
Read the two rules off the file: a gap is cut only if the transcript says nobody spoke for 1.2 seconds and the audio says room tone. Level alone would have cut the laugh. Ask the room: pass or fail against the first "what you look for" line? Then move on; the crop is the story.
-->

---
class: content interface-slide run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 2 of 7</div>

# The first crop. The tool said ok.

<div><div class="interface-image plain"><img src="/assets/creatives/src-wide-sf11.jpg" alt="Source frame: a wide shot of the workshop room, audience in the foreground, slide screen center, speaker at the right" /><span>Source frame · C0828.MP4 at 1:55:55 · 3840×2160 · what Auto Reframe was given</span></div></div>
<p class="interface-caption">Auto Reframe ran on all eleven sequences and returned ok on every one. Nobody had looked at a frame yet. This is what it saw: audience in front, a bright slide screen, the speaker at the edge.</p>

<div class="source">Recorded 2026-09-10 · the discarded Auto Reframe renders were not kept; the finding is quoted on the next slides from the session and LESSONS.md</div>

<!--
Hold on this frame. Ask the room where a saliency tracker goes: the brightest, busiest thing. The screen, and the heads in front. Say the honest part: I do not have the bad output frame, because it was thrown away the moment it was seen. What I have is the written record of what it did, next.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Give the challenger the evidence.

<div class="terminal"><div class="terminal-label">Type in Claude Code · before any batch</div><pre>Pilot the shortest clip. capture_frame it at the first cut, the midpoint,
and the last frame. Save the frames next to the exports.
For each requirement, say which frame proves it.
Mark pass, fail, or unresolved. Do not assume the tool’s ok is correct.</pre></div><div class="takeaway">The challenger here is a frame. A frame is evidence the way a quoted response was yesterday. Look at one before you render ten.</div>

<div class="source">premiere-ops/CLAUDE.md, the pilot rule · coach/BUILD-STEPS.md § 6 in the loop repo</div>

<!--
Adversarial review, editing edition: the reviewer is your eyes on a captured frame, at three timestamps, before the batch. In run one this rule did not exist yet. The next slide is what it cost.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 3 of 7</div>

# The verdict: fail.

<div class="artifact"><label>LESSONS.md · LESSON 2 AND THE DOMAIN FINDING · EXACT EXCERPTS · SEPT 11</label><blockquote>Auto Reframe was run on all 11 sequences before looking at a single frame; all 11 analyses (plus 11 caption attaches to those sequences) were thrown away when frame 1 showed it tracking the audience.</blockquote><blockquote>Adobe Auto Reframe is saliency-based and untunable via API; on a wide room shot it locks onto foreground audience and the slide screen, and it never punches in.</blockquote></div>
<p class="interface-caption">Frame 1 of the pilot. Twenty-two operations discarded. The requirement said “keep me in frame”; the tool passed its own check and failed ours.</p>

<div class="source">Recorded 2026-09-10 to 11 · LESSONS.md, process lesson 2 and domain findings · text verbatim</div>

<!--
This is the moment. The tool did exactly what it promised and produced something no editor would ship. Nothing in the API says "wrong subject." Only a frame says that. Read both excerpts as written.
-->

---
class: content repair-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Repair one failure. Test again.

<div class="two"><div class="artifact"><label>FAILURE TO CATCH · THIS RUN</label><p>Me: “have it track me as I walk.”</p><p>Tool: ok. Frame: the audience.</p></div><div><h3>Small repair</h3><p>Replace the saliency tracker with a motion tracker that knows the room: exclude the ceiling and the audience rows, down-weight the slide screen, centroid of what moves is the speaker.</p><h3>Recheck</h3><p>Pilot the shortest clip first. Frame 1, midpoint, last. Then a clip that already passed the cut test.</p></div></div>

<div class="source">LESSONS.md · pipeline/track-speaker.mjs · coach/BUILD-STEPS.md § 7</div>

<!--
One failure, one repair, one recheck. Never manufacture a failure; this one was real and it cost twenty-two operations. Preserve before and after, which is the next slide.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 4 of 7</div>

# Repair one rule.

<div class="two"><div class="artifact"><label>BEFORE · ONE TOOL CALL</label><pre>auto_reframe_sequence("SF11 9x16")
→ ok</pre></div><div class="artifact"><label>AFTER · track-speaker.mjs · jobs/c0828/job.config.json</label><pre>weight = 0   above ceilingY 200
weight = 0   below audienceY 1400
weight = 0.12 inside screenRect
             [1250, 420, 2550, 1200]
centroid of motion per segment
→ one static Motion Position
→ reframes land on the cuts</pre></div></div>
<p class="interface-caption">One script replaces one tool call. The numbers are this room’s, read off one extracted frame and written into the job file. Nothing else in the build changed.</p>

<div class="source">Recorded 2026-09-10 to 11 · values verbatim from jobs/c0828/job.config.json · pipeline/track-speaker.mjs</div>

<!--
One rule, one edit, shown. Everything else in the pipeline is untouched. Say why static positions beat continuous tracking: speakers walk during silences, and the silences were already cut, so a reframe on each cut is invisible and there is no wobble. Then rerun the failing clip.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 5 of 7</div>

# Rerun the same clip.

<div class="frame-pair"><figure><img src="/assets/creatives/src-wide-sf11.jpg" alt="Source frame, wide room shot" /><figcaption>Source · C0828.MP4 · 1:55:55</figcaption></figure><figure><img src="/assets/creatives/sf11-9x16.jpg" alt="SF11 vertical export, frame at four seconds: the speaker chest-up in front of the banner" /><figcaption>SF11 9x16.mp4 · 0:04 · 1215×2160</figcaption></figure></div>
<p class="interface-caption">SF11, the shortest clip, piloted first. Left: the source. Right: the vertical export. Chest-up, the speaker, not the banner. Then the midpoint, the last frame, and only then the other ten.</p>

<div class="source">Recorded 2026-09-10 · frames extracted with ffmpeg from the source and the exported file · scale 170, position from framing-table.json</div>

<!--
Compare the two. Same moment. Ask the room for pass, fail, or unresolved against the second "what you look for" line. Then say what the pilot caught next.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 6 of 7</div>

# Challenge it again. A smaller failure, a smaller repair.

<div class="frame-pair"><figure><img src="/assets/creatives/src-wide-sf01.jpg" alt="Source frame from SF01, wide room shot" /><figcaption>Source · C0828.MP4 · 29:35</figcaption></figure><figure><img src="/assets/creatives/sf01-9x16.jpg" alt="SF01 vertical export, frame at twelve seconds" /><figcaption>SF01 9x16.mp4 · 0:12 · after the fix</figcaption></figure></div>
<p class="interface-caption">The first pilot frame after the repair was black. Motion Position is normalized 0 to 1 through the API, not pixels as documented; pixel values put the media off-canvas. One line fixed it. Frame 1 caught it in one call; the batch would have exported eleven black videos.</p>

<div class="source">Recorded 2026-09-10 · LESSONS.md, domain findings · CLAUDE.md landmines: Motion Position is normalized</div>

<!--
The repair worked and the very next frame found a second, unrelated failure. That is why the pilot rule says three timestamps and one sequence, not one timestamp and eleven. Read the landmine as written. Then the last frame.
-->

---
class: content run-slide
---

<div class="slide-number">SZD Labs · Claude for creatives · recorded 7 of 7</div>

# Unresolved is a verdict too.

<div class="artifact"><label>CLAUDE.md · THE GRADE PHASE · EXACT EXCERPT</label><blockquote><code>grade</code> exits 2 listing clips that need Lumetri added by hand (QE can’t add effects), one UI drag per clip from the Effects panel, then rerun.</blockquote></div><div class="artifact"><label>LESSONS.md · LESSON 3 · EXACT EXCERPT</label><blockquote>Two unexplained failures of the same approach = stop and run a decisive diagnostic, not variant #3. The Lumetri copy/paste saga burned half a session: four paste-attributes variants, a Premiere restart, a focus-stealing hunt.</blockquote></div>
<p class="interface-caption">The clipboard path died four ways in half a session. The repair was not a fifth variant. The pipeline names the clips it cannot grade and stops; you drag once per clip. The tool cannot, and the build says so instead of pretending.</p>

<div class="source">Recorded 2026-09-09, 5:05 to 5:11 PM · CLAUDE.md and LESSONS.md, text verbatim · the grade values still travel by number, never by paste</div>

<!--
This is the evidence discipline in one frame. Not every failure has a repair inside the tool. When it does not, the honest build reports the gap and hands you the one gesture only you can make. Read the last line of lesson 3 aloud: one Edit-menu check, "is Paste grayed?", would have pointed at the clipboard from the start. That is the last thing they should hear before the exit questions.
-->

---
class: closing
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# What you just watched.

<div class="exit"><p>Which requirement did we pick?</p><p>Where does the pipeline enforce that behavior?</p><p>What clip did we run, and what did the frame show?</p><p>What did we repair, and what stays a human step?</p></div><div class="tagline">A brief → a pipeline → frames as evidence. Now swap in your own edit.</div>

<div class="source">Workshop walkthrough · the loop repo</div>

<!--
Answer the four questions in order, out loud, in under ninety seconds. Keep me in frame; track-speaker.mjs and the pilot rule; SF11, and frame 1 showed the speaker after showing the audience; the tracker, and the one Lumetri drag per clip stays human.
-->

---
class: content
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Write your own brief. One page.

<div class="two"><div class="artifact"><label>YOUR-BRIEF.md · FIVE LINES · ANY EDIT</label><ul><li><b>Who</b> it helps, and the moment it helps them.</li><li><b>What it must do</b>, in behaviors you can see in a frame or a file.</li><li><b>What it must never do</b>: cut a reaction, invent a caption, export without a look.</li><li><b>What it produces</b>: the two or three outputs you would actually reuse.</li><li><b>How you know it worked</b>: three clips and the visible failure for each.</li></ul></div><div><h3>Then the same six steps</h3><p>Put the brief in an empty folder. Start Claude Code. Ask for the spec. Approve it. Inspect the files. Run your three clips. Repair one thing.</p><p>A podcast-to-clips pass. A caption style you actually like. A color match across a shoot. A photo cull by your own rules. The loop is the same.</p></div></div><div class="takeaway">Your edit, not mine. Write the brief tonight.</div>

<div class="source">Generalized from premiere-ops/BUILD-BRIEF.md · the shorts pipeline was one instance</div>

<!--
This is the takeaway slide. Read the five lines slowly. Name two or three edits from the room if anyone offered one during the session. My brief was exactly this shape for a shorts pipeline; theirs will be this shape for their own thing.
-->

---
class: content qr-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Run the full loop tonight.

<div class="two"><section><h3>Build your own</h3><p><a href="https://github.com/MaxyMeanderings/claude-code-build-loop" target="_blank">Clone the loop repo ↗</a></p><p><a href="https://github.com/MaxyMeanderings/claude-code-build-loop/blob/main/coach/BUILD-STEPS.md" target="_blank">Follow coach/BUILD-STEPS.md ↗</a></p><p><a href="https://github.com/MaxyMeanderings/claude-code-build-loop/blob/main/YOUR-BRIEF.md" target="_blank">Start from YOUR-BRIEF.md ↗</a></p><p>Save your spec, scripts, job file, pilot frames, and the clips you judged.</p><div class="qr-row"><figure class="qr"><img src="/assets/creatives/qr/qr-loop.svg" alt="QR code for https://github.com/MaxyMeanderings/claude-code-build-loop" /><span>Clone the loop</span></figure><figure class="qr"><img src="/assets/creatives/qr/qr-build-steps.svg" alt="QR code for https://github.com/MaxyMeanderings/claude-code-build-loop/blob/main/coach/BUILD-STEPS.md" /><span>BUILD-STEPS.md</span></figure><figure class="qr"><img src="/assets/creatives/qr/qr-your-brief.svg" alt="QR code for https://github.com/MaxyMeanderings/claude-code-build-loop/blob/main/YOUR-BRIEF.md" /><span>YOUR-BRIEF.md</span></figure></div></section><section><h3>Compare and continue</h3><p><a href="https://github.com/leancoderkavy/premiere-pro-mcp" target="_blank">premiere-pro-mcp, the bridge into Premiere ↗</a></p><p><a href="https://maxymeanderings.github.io/claude-code-build-loop/" target="_blank">The coach: the same loop, yesterday ↗</a></p><p><a href="https://maxymeanderings.github.io/claude-for-creatives/" target="_blank">These slides ↗</a></p><div class="qr-row"><figure class="qr"><img src="/assets/creatives/qr/qr-premiere-mcp.svg" alt="QR code for https://github.com/leancoderkavy/premiere-pro-mcp" /><span>premiere-pro-mcp</span></figure><figure class="qr"><img src="/assets/creatives/qr/qr-coach-deck.svg" alt="QR code for https://maxymeanderings.github.io/claude-code-build-loop/" /><span>The coach example</span></figure><figure class="qr"><img src="/assets/creatives/qr/qr-slides.svg" alt="QR code for https://maxymeanderings.github.io/claude-for-creatives/" /><span>These slides</span></figure></div></section></div><div class="takeaway">Any Claude Code account works. Premiere Pro 2025 and the free bridge if you want the editing half. Your brief, an empty folder, the same six steps.</div>

<div class="source">github.com/MaxyMeanderings/claude-code-build-loop · github.com/leancoderkavy/premiere-pro-mcp</div>

<!--
No sponsored credits in this room; say so. A Pro subscription or an API key both run this. The bridge is a free open-source MCP server; the CEP panel needs one debug-mode setting, which its README covers. The brief is the only thing to change for their own pipeline.
-->

---
class: content access-slide qr-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Stay connected.

<div class="connect"><img class="speaker-photo" src="/assets/tyler.jpeg" alt="Tyler Sztuka" /><div><h3>Tyler Sztuka</h3><p>SZD Labs · Claude Community Ambassador</p><p><a href="https://www.linkedin.com/in/tyler-sztuka-283937123/" target="_blank" rel="noopener noreferrer">linkedin.com/in/tyler-sztuka-283937123 ↗</a></p><p><a href="https://szdlabs.io/" target="_blank" rel="noopener noreferrer">szdlabs.io ↗</a></p></div></div>

<div class="connect-qr"><figure class="qr"><img src="/assets/creatives/qr/qr-linkedin.svg" alt="QR code for https://www.linkedin.com/in/tyler-sztuka-283937123/" /><span>LinkedIn</span></figure><figure class="qr"><img src="/assets/creatives/qr/qr-szdlabs.svg" alt="QR code for https://szdlabs.io/" /><span>szdlabs.io</span></figure></div>

<img class="connect-szd" src="/assets/szd-logo.png" alt="SZD Labs" />

<div class="source">Thanks for judging with me. The loop is in the repo. Copy it.</div>

---
class: content access-slide qr-slide
---

<div class="slide-number">SZD Labs · Claude for creatives</div>

# Keep building. Take the links.

<Links />

<div class="source">Slides and the loop. Type it or click it.</div>
