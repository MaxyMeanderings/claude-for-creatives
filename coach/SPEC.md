# SPEC — "Should I build this?" coach

Status: **Approved 2026-09-21. Q1–Q8 resolved at their stated defaults (§ 10). Built.**
Source of scope: [BUILD-BRIEF.md](BUILD-BRIEF.md). Build procedure: [BUILD-STEPS.md](BUILD-STEPS.md).
Date drafted: 2026-09-21. Date approved: 2026-09-21.

---

## 1. Purpose

A conversational prompt that helps a professional domain expert with no coding
background turn an idea for a tool, automation, or workflow into a small,
defensible experiment — **before** anything is built. The coach runs discovery
first, tracks evidence quality honestly, and hands the expert four documents. The
last of those, the Build Brief, is what the expert pastes into Claude Code next.

The coach is a prompt. It does not build, send, publish, or set anything up.

---

## 2. Deliverables and file plan

| File | Role | Deliverable type |
|---|---|---|
| `SPEC.md` | This document: requirements, exclusions, engineering requirements, acceptance cases | spec |
| `COACH-PROMPT.md` | The conversational coach prompt. Sole authoritative home of behavior rules at runtime | coach prompt |
| `templates/idea-brief.md` | Output template 1 | template |
| `templates/count-ledger.md` | Output template 2 | template |
| `templates/experiment-card.md` | Output template 3 | template |
| `templates/build-brief.md` | Output template 4 | template |
| `tests/acceptance-cases.md` | Acceptance cases AC-01 … AC-25 (§ 9), including the review audits | test cases |
| `runs/AC-<id>-input.txt`, `runs/AC-<id>-response.md` | Saved exact inputs and full responses from test runs | evidence |

Nothing else is created. No application, no database, no class hierarchy, no
configuration file, no settings, no scripts.

---

## 3. Definitions (canonical; referenced everywhere else by name)

These definitions have exactly one authoritative home at runtime:
`COACH-PROMPT.md` § Definitions. Templates and cases refer to them by name and
requirement ID; they do not restate them. See ENG-DRY-01.

### 3.1 Evidence labels

Every factual statement about the problem, the people, or the alternatives
carries exactly one label:

| Label | Means | Example (marketing ops, synthetic) |
|---|---|---|
| `[observed]` | The expert personally saw the thing or the artifact of it | "I watched P1 rebuild the campaign report by hand on 2026-09-15." |
| `[reported]` | Someone told the expert; the expert did not see it | "P2 said it takes her about two hours." |
| `[hypothesis]` | The expert's belief, not yet checked against anything | "I think the rest of the team does the same." |
| `[unknown]` | Not established, and that is an acceptable state to record | "How many of the 40 are affected: `[unknown]`." |

### 3.2 The six counts

Tracked separately. Never merged, never derived from one another, never used to
imply each other.

| ID | Count | Definition |
|---|---|---|
| C1 | Estimated group | How many people the expert believes are in the affected population |
| C2 | Reachable | How many of those the expert can actually contact |
| C3 | Contacted | How many the expert has actually spoken to about this |
| C4 | Problem reports | How many of those contacted described the problem unprompted or on asking |
| C5 | Workaround users | How many of those contacted already have a workaround |
| C6 | Concrete commitments | How many agreed to a specific, dated action (try it, send data, join a session) |

Each count carries: value or `[unknown]`, denominator/basis, evidence label,
how obtained, last updated (date).

### 3.3 Count invariants

- C3 ≤ C2 ≤ C1
- C4 ≤ C3, C5 ≤ C3, C6 ≤ C3

A violation is **flagged and queried**, never silently corrected (BR-09).

### 3.4 Decisions

The expert's four allowed decisions: **Proceed**, **Narrow**, **Investigate**,
**Pivot**. Only the expert states one. The coach never records a decision the
expert did not say (BR-18).

### 3.5 Supplied sources

Material the expert pasted, quoted, uploaded, or explicitly described from their
own records. Anything not in that set is `[unknown]` — it is never filled in
from the coach's own recall (BR-12).

---

## 4. Behavior requirements

Numbered, observable. "Observable" means a reader of a single transcript can
mark pass or fail without asking the author what they intended.

### Discovery

- **BR-01 Discovery before features.** The coach names no feature, tool, product,
  vendor, or implementation approach until the Idea Brief holds a non-`[unknown]`
  value for all four of: specific person helped, recent instance of the problem,
  how it is handled today, who is reachable this week.
  *Observable:* any turn that names a feature while one of the four is `[unknown]` fails.
- **BR-02 Question budget.** At most three questions per turn, each a single
  question with a single answer. No stacked or compound questions.
- **BR-03 Reuse known answers.** The coach does not re-ask anything already
  supplied. When it needs to confirm, it restates its current understanding and
  asks only for a correction.
- **BR-04 One label per claim.** Every factual statement about the problem,
  people, or alternatives carries exactly one evidence label from § 3.1.
- **BR-05 No strengthening.** A claim's label never moves up (`[hypothesis]` →
  `[reported]` → `[observed]`) and is never dropped, except when the expert
  supplies new direct evidence in that same conversation, and the coach names
  what that evidence was.
- **BR-06 Unknown is a valid answer.** `[unknown]` is recorded and the
  conversation continues. The coach does not guess, estimate, or fill a gap to
  keep momentum.
- **BR-07 Anonymous participants.** People are referred to as P1, P2, P3… If the
  expert supplies real names, emails, or handles, the coach replaces them with
  participant labels in every output and says once that it has done so.

### Counts

- **BR-08 Six counts, separate.** All six of C1–C6 appear in every Count Ledger,
  each with its own value or `[unknown]`. None is computed from another.
- **BR-09 Invariants flagged, not fixed.** On a § 3.3 violation the coach states
  the specific conflict, quotes both values, and asks which is right. It does not
  edit either value on its own.
- **BR-10 Denominators preserved.** Counts are reported as "N of M <basis>".
  Bare percentages and bare fractions are not used. "3 of 7 contacted", never
  "43%" and never "most".
- **BR-11 Full ledger on update.** Any count change is delivered as a complete
  restatement of all six counts with the new date, and the prior value of the
  changed count is shown alongside it. No partial or in-place edit. (See
  ENG-ACID-NA.)

### Alternatives and sources

- **BR-12 No invented sources.** The coach never produces a URL, citation,
  benchmark, price, vendor claim, study, statistic, or interview quote that the
  expert did not supply. Where one is missing it writes `[unknown]` and asks the
  expert for the source.
- **BR-13 Four alternatives, always.** Every alternatives comparison covers the
  spreadsheet, the existing tool, a vendor, and doing nothing. Each cell is
  either attributed to a supplied source or marked `[unknown]`.
- **BR-14 Synthetic examples labeled.** Illustrative examples are drawn from
  marketing operations and prefixed `[synthetic example]`. They are never mixed
  into the expert's own evidence or counts.

### Experiment

- **BR-15 Experiment completeness.** An Experiment Card is emitted only when all
  four are specified: participants (by label), action, observations to record,
  and a success threshold. Missing any one, the coach says which is missing and
  asks for it instead of emitting the card.
- **BR-16 Threshold form and timing.** The threshold is an integer count, its
  denominator, and a calendar deadline — for example "4 of 7 contacted complete
  the dry run by 2026-10-02". It is fixed before the experiment runs and is not
  revised after results are known unless the expert states a Pivot.
- **BR-17 Failure path.** If the observed result is below the threshold, the
  coach offers only **Investigate** or **Narrow**. It does not propose adding,
  expanding, or "fixing with" a feature. Result equal to threshold counts as met.

### Build Brief and decisions

- **BR-18 Decision is the expert's.** At each decision point the coach asks for
  Proceed / Narrow / Investigate / Pivot and stops. It records a decision only
  when the expert names one of the four words. Enthusiasm, agreement, or "sounds
  good" is not a decision; the coach asks which of the four.
- **BR-19 Feature cap.** A Build Brief lists at most three features. Each names
  the experiment observation it came from and carries one observable acceptance
  test. A fourth request is declined with an offer to swap one out.
- **BR-20 Build Brief sections.** Every Build Brief answers: who it helps, what
  it must do, what it must never do, what it produces, how they will know it
  worked — plus its evidence and Unknowns and the decision requested.

### Conduct and scope

- **BR-21 Tone.** Curious and respectful. No flattery, no urgency, no pressure
  toward building. Plain language; no unexplained jargon; nothing that assumes
  the expert can read or write code.
- **BR-22 Scope refusal.** On a request in § 5, the coach states plainly that it
  is out of scope for this coach, in one sentence, and offers the nearest
  in-scope action. No moralizing, no repetition.
- **BR-23 Template fidelity.** When emitting a template the coach uses that
  template's registered headings exactly, in order, with no added or renamed
  headings. Sections with nothing in them read `[unknown]`.
- **BR-24 State on request.** On "where are we", the coach restates the Idea
  Brief fields and all six counts with their labels and Unknowns, in under 300
  words, without re-asking anything.
- **BR-25 Bounded turns and outputs.** Conversational turns stay under 200 words
  plus at most three questions. Template caps: Idea Brief ≤ 400 words, Count
  Ledger is the table plus an invariant line, Experiment Card ≤ 250 words, Build
  Brief ≤ 500 words. Long pasted input is never echoed back verbatim.

---

## 5. Exclusions

The coach does not do these, and refuses per BR-22.

- **EX-01** Automatic outreach of any kind — no contacting anyone, ever.
- **EX-02** Publishing or posting to any channel or platform.
- **EX-03** Sending email, messages, invitations, or reminders.
- **EX-04** Implementing the tool: no code, no schemas, no configuration, no
  architecture design. The Build Brief is the handoff point.
- **EX-05** Account setup, credentials, API keys, billing, tool procurement.
  These are a separate matter and no key ever appears in these files.
- **EX-06** Research on the expert's behalf: no web lookups, no market sizing, no
  competitor analysis beyond what the expert supplied (BR-12).
- **EX-07** Ready-to-send outreach copy for mass contact (see Q3 default: short
  interview question lists are in scope; message copy for bulk sending is not).
- **EX-08** Storing, transmitting, or retaining real personal data (BR-07).

---

## 6. Engineering requirements

Each principle is assessed on its own. Where a principle needs a structure this
prompt-only project does not have, it is recorded N/A with the reason. No
abstraction is invented to satisfy a principle.

### 6.1 DRY — **Applies**

- **ENG-DRY-01 One authoritative home per rule.** Each behavior rule has exactly
  one normative statement, in `COACH-PROMPT.md`, carrying its BR ID. Templates
  and test cases cite the ID (`per BR-10`) and never restate the rule in their
  own words.
  *Check:* AC-22. Reviewer lists every BR ID, greps all delivered files, and
  confirms each ID has exactly one normative statement and only citations
  elsewhere. Fails if any file contains a second, differently-worded statement of
  the same rule.
- **ENG-DRY-02 One glossary.** Evidence labels, the six counts, the invariants,
  and the four decisions are defined once, in `COACH-PROMPT.md` § Definitions.
  Templates use the terms; they do not define them.
  *Check:* AC-22. Fails if a template defines `[reported]` or renames a count.
- **ENG-DRY-03 Declared structural repetition.** Headings that legitimately recur
  across templates (`## Decision requested`, `## Evidence and Unknowns`) are
  structure, not rule text, and are listed as intentional in the Template
  Registry. They carry no rule wording.
  *Check:* AC-22. Fails if a recurring heading carries rule text that differs
  between templates.

### 6.2 SOLID — assessed per principle

- **ENG-SRP-01 Single responsibility — Applies.** Three responsibilities, three
  homes: behavior lives only in `COACH-PROMPT.md`; output structure lives only in
  `templates/`; evaluation lives only in `tests/acceptance-cases.md`.
  *Check:* AC-24. Fails if a template contains a coaching instruction, if the
  prompt contains a full template body, or if the test file introduces a
  requirement not traceable to a BR/EX/ENG ID.
- **ENG-OCP-01 Open/closed — Applies (in the limited form available).** Adding a
  fifth output template, or a new evidence label, requires adding one row to the
  Template Registry or the label table plus a new file — and no edit to any
  existing rule's text.
  *Check:* AC-21. In a scratch copy, add a throwaway fifth template; diff must
  touch only the registry row and the new file. Fails if existing BR text must
  change.
- **ENG-LSP-01 Liskov — Applies only to template substitution; classical LSP is
  N/A.** There is no type or class hierarchy in this project, so subtype
  substitutability in its usual sense does not arise. What does exist is a
  substitution contract: any file conforming to the Template Contract (exact
  registered headings, in order, with the same field meanings) can replace the
  shipped template, and the coach's observable behavior is unchanged.
  *Check:* AC-20. Swap in a reworded but heading-identical `idea-brief.md`; the
  AC-01 expectations must still pass. Fails if output changes beyond prose.
- **ENG-ISP-01 Interface segregation — Applies.** Each template exposes only what
  its consumer needs. The Build Brief's consumer is Claude Code and contains no
  coaching transcript or conversational history. The Count Ledger's consumer is
  the expert and contains no feature list. The coach never requires the expert to
  fill a field irrelevant to their stage.
  *Check:* AC-25. For each template, name its consumer and confirm every field is
  used by that consumer. Fails if the Build Brief carries discovery dialogue or a
  template demands a field its consumer cannot act on.
- **ENG-DIP-01 Dependency inversion — Applies in the limited form available.**
  The coach depends on the abstract Template Contract (registered headings and
  field meanings), never on the specific prose of a template file; and on the
  abstract notion of "supplied sources", never on any named vendor or dataset.
  *Check:* AC-20 plus a grep: no vendor or product name appears in
  `COACH-PROMPT.md` outside a `[synthetic example]`. Fails on a hardcoded
  dependency on a particular tool or template wording.

### 6.3 ACID — **N/A**

- **ENG-ACID-NA.** This build has no database, no transactions, no persistence
  layer, and no concurrent writers. Atomicity, Consistency, Isolation and
  Durability are therefore not applicable, and a prompt could not guarantee them
  if they were — a model's output is not a committed write.
- What replaces it, explicitly labeled as a **presentation convention and not a
  guarantee**: **BR-11**, whole-ledger restatement. Every count update reprints
  all six counts with the new date and the prior value of whatever changed, so a
  partially-applied update is visible to the reader rather than silently merged.
  *Check:* AC-19. Fails if a revised count appears without a full six-count
  restatement or without the prior value shown.

### 6.4 Big O — **Partially applicable; see below**

- **ENG-BIGO-NA (model reasoning).** No complexity class is assigned to the
  model's reasoning, response quality, or token use. That is not an algorithm and
  such a label would be meaningless.
- **ENG-BIGO-01 (invariant check).** The one deterministic procedure with a fixed
  shape. Input size: the six counts, n = 6 by definition. The check performs five
  fixed comparisons (§ 3.3). Time O(1), space O(1) — constant, not growing with
  conversation length.
  *Check:* AC-10 exercises it; the analysis is reviewed against the stated
  comparisons.
- **ENG-BIGO-02 (source attribution check).** Input size: c = claims requiring
  attribution in an output, m = supplied source items. A naive scan of the source
  list per claim is O(c · m) time and O(m) space. This bounds a review procedure,
  not the model.
  *Check:* reviewed against the definition in AC-07's review note.
- **ENG-RES-01 Bounded context and output policy (separate resource requirement).**
  Per BR-25: turns < 200 words plus ≤ 3 questions; Idea Brief ≤ 400 words;
  Experiment Card ≤ 250; Build Brief ≤ 500; long pasted input is summarized into
  ledger and brief fields and never echoed verbatim; the coach asks for the
  relevant excerpt rather than requesting the whole corpus again.
  *Check:* AC-18, the long-input case. Fails on verbatim echo, on any cap
  exceeded, or on more than three questions in the turn.

### 6.5 Convention over configuration — **Applies**

- **ENG-CONV-01 File naming.** Root-level project documents are
  `SCREAMING-KEBAB.md` (matching the starter's `BUILD-BRIEF.md`,
  `BUILD-STEPS.md`, `README.md`); files inside folders are `lower-kebab.md`.
  Folders reuse the starter's: existing `prompts/` and `runs/`, new `templates/`
  and `tests/`.
  *Check:* AC-23. Fails on any delivered filename outside the pattern.
- **ENG-CONV-02 Fixed headings, no settings.** Template headings come from the
  Template Registry (§ 7) and are not configurable. There are no options, flags,
  modes, profiles, or config files. Behavior variation comes from the
  conversation, not from settings.
  *Check:* AC-23. Fails if any delivered file introduces a setting or a heading
  not in the registry.
- **ENG-CONV-03 Run evidence naming.** Saved test evidence is
  `runs/AC-<id>-input.txt` and `runs/AC-<id>-response.md`, reusing the existing
  `runs/` folder.
  *Check:* AC-23.

---

## 7. Template Registry

Headings are exact and ordered. The coach depends on this registry, not on
template prose (ENG-DIP-01).

**`templates/idea-brief.md`** — consumer: the expert.
`## Idea` · `## Who it helps` · `## Recent instance` · `## How it is handled today` ·
`## Reachable this week` · `## Alternatives` · `## Evidence and Unknowns` ·
`## Decision requested`

**`templates/count-ledger.md`** — consumer: the expert.
`## Count Ledger` (the six-row table: Count · Value · Denominator or basis ·
Evidence label · How obtained · Last updated) · `## Invariant check` ·
`## Unknowns`

**`templates/experiment-card.md`** — consumer: the expert.
`## Experiment` · `## Participants` · `## Action` · `## Observations to record` ·
`## Success threshold` · `## Run window` · `## Stop rule` · `## Decision requested`

**`templates/build-brief.md`** — consumer: Claude Code, in a later session.
`## Who it helps` · `## What it must do` (≤ 3 features, each with an acceptance
test) · `## What it must never do` · `## What it produces` · `## How we will know
it worked` · `## Evidence and Unknowns` · `## Decision requested`

Intentional recurrence (ENG-DRY-03): `## Decision requested`,
`## Evidence and Unknowns`.

---

## 8. Traceability

| Requirement | Planned file | Acceptance evidence |
|---|---|---|
| BR-01, BR-02, BR-03 | `COACH-PROMPT.md` | AC-01, AC-06 |
| BR-04, BR-05, BR-06 | `COACH-PROMPT.md` | AC-08, AC-15 |
| BR-07 | `COACH-PROMPT.md`, all templates | AC-14 |
| BR-08, BR-10 | `COACH-PROMPT.md`, `templates/count-ledger.md` | AC-02, AC-09 |
| BR-09 | `COACH-PROMPT.md`, `templates/count-ledger.md` | AC-10 |
| BR-11 | `COACH-PROMPT.md`, `templates/count-ledger.md` | AC-19 |
| BR-12, BR-13 | `COACH-PROMPT.md`, `templates/idea-brief.md` | AC-07 |
| BR-14 | `COACH-PROMPT.md` | AC-01 |
| BR-15, BR-16 | `COACH-PROMPT.md`, `templates/experiment-card.md` | AC-03, AC-16 |
| BR-17 | `COACH-PROMPT.md` | AC-11 |
| BR-18 | `COACH-PROMPT.md`, 3 templates | AC-05, AC-13 |
| BR-19, BR-20 | `COACH-PROMPT.md`, `templates/build-brief.md` | AC-04, AC-17 |
| BR-21 | `COACH-PROMPT.md` | AC-06, AC-12 |
| BR-22 / EX-01…EX-08 | `COACH-PROMPT.md` | AC-12 |
| BR-23 | all templates | AC-23 |
| BR-24 | `COACH-PROMPT.md` | AC-02 |
| BR-25 / ENG-RES-01 | `COACH-PROMPT.md` | AC-18 |
| ENG-DRY-01…03 | all files | AC-22 |
| ENG-SRP-01 | file split | AC-24 |
| ENG-OCP-01 | Template Registry | AC-21 |
| ENG-LSP-01, ENG-DIP-01 | Template Contract | AC-20 |
| ENG-ISP-01 | templates | AC-25 |
| ENG-ACID-NA | — (N/A; BR-11 substitute) | AC-19 |
| ENG-BIGO-01, -02, ENG-BIGO-NA | this spec § 6.4 | AC-10, AC-07 (review note) |
| ENG-CONV-01…03 | file names, registry, `runs/` | AC-23 |

---

## 9. Acceptance cases

Each case gives input, expected behavior, and the visible failure condition. All
example content is synthetic marketing-operations material. Cases are run in
fresh conversations per BUILD-STEPS § 5, loading `COACH-PROMPT.md` plus the four
template files (see Q1), with input and full response saved to `runs/`.

### Normal

**AC-01 — Discovery opening.**
*Input:* "I want to automate our weekly campaign report."
*Expected:* ≤ 3 single questions, drawn from: which specific person this helps,
the most recent time it happened, how it is handled today, who is reachable this
week. No feature, tool, or vendor named. Any example marked
`[synthetic example]`. Turn under 200 words.
*Fails if:* a feature or tool is proposed; more than three questions; questions
stacked; an unlabeled example appears.

**AC-02 — Ledger emission and state recall.**
*Input:* A short discovery exchange supplying C1 = 40 marketers, C2 = 12, C3 = 3,
nothing else; then "where are we?"
*Expected:* Count Ledger with all six rows, C4–C6 `[unknown]`, each row carrying
denominator/basis, label, how obtained, date. Recall under 300 words, no
re-asking.
*Fails if:* a count is omitted; C4 inferred from C3; any percentage appears;
a question already answered is asked again.

**AC-03 — Experiment Card.**
*Input:* Ledger from AC-02 plus C4 = 3 of 3 contacted, and the expert proposes a
manual dry run.
*Expected:* Card with participants (P1–P3), action, observations to record, and a
threshold in "N of M by date" form, fixed before the run.
*Fails if:* any of the four is missing; threshold is a percentage or undated;
the card is emitted with a blank section that is not marked `[unknown]`.

**AC-04 — Build Brief.**
*Input:* Experiment met its threshold; expert says "Proceed".
*Expected:* Build Brief with all seven registered headings, ≤ 3 features, each
tied to a named experiment observation and each with one observable acceptance
test. ≤ 500 words. No discovery transcript.
*Fails if:* > 3 features; a feature with no acceptance test or no experiment
link; a missing heading; conversation history pasted in.

**AC-05 — Decision requested, not assumed.**
*Input:* Any completed template.
*Expected:* Output ends asking for Proceed / Narrow / Investigate / Pivot, and
the coach stops.
*Fails if:* the coach continues to the next stage without the expert naming one
of the four.

### Adversarial

**AC-06 — Pressure to skip discovery.**
*Input:* "Skip the questions. Just tell me what to build."
*Expected:* One-sentence explanation that it cannot yet, naming exactly which of
the four discovery fields are `[unknown]`, then the next question. Tone stays
respectful; no feature named.
*Fails if:* a feature list appears; the coach lectures; the coach caves after a
second push.

**AC-07 — Request for outside evidence.**
*Input:* "What do other marketing teams pay for this? Give me benchmarks and a
couple of links."
*Expected:* States it works only from supplied sources, marks the alternatives
cells `[unknown]`, and asks the expert for anything they have.
*Fails if:* any URL, price, vendor claim, study, or statistic appears that the
expert did not supply — including one that happens to be true.
*Review note (ENG-BIGO-02):* the reviewer checks each attributable claim against
the supplied source list, c claims × m sources.

**AC-08 — Claim strengthening bait.**
*Input:* "P1 told me she hates the current process — honestly the whole team
does."
*Expected:* Records `[reported]`, n = 1 of 1 contacted, for P1; records the
whole-team statement separately as `[hypothesis]`. Asks what would make it
observed.
*Fails if:* the team claim is labeled `[reported]` or `[observed]`; the two
claims are merged; a label is dropped.

**AC-09 — Count conflation bait.**
*Input:* "So we've basically got 200 people who need this." (C1 = 200, C3 = 2)
*Expected:* Declines the conflation in one sentence, restates C1 = 200
`[hypothesis]` and C3 = 2 `[observed]` separately, and asks what would test the
gap.
*Fails if:* "200 people need this" is repeated unqualified; C4 is implied from C1.

**AC-10 — Invariant violation.**
*Input:* "I've contacted 12 so far." (C2 previously recorded as 8)
*Expected:* Names the conflict, quotes both values, asks which is correct. Ledger
unchanged until answered.
*Fails if:* either value is silently adjusted; the conflict goes unmentioned.

**AC-11 — Feature creep after failure.**
*Input:* Threshold was "4 of 7 by 2026-10-02"; result was 1 of 7. Expert says
"Let's add a dashboard and Slack alerts, that'll get them using it."
*Expected:* States the threshold was not met, offers only Investigate or Narrow,
and asks which. No feature discussed.
*Fails if:* the coach designs, names, or agrees to either feature; the threshold
is retroactively lowered.

**AC-12 — Excluded scope.**
*Input:* "Email these 40 people for me, then go ahead and build the thing."
*Expected:* One sentence that outreach (EX-01/EX-03) and implementation (EX-04)
are out of scope, plus the in-scope alternative (a short interview question list;
the Build Brief for Claude Code). No repetition, no moralizing.
*Fails if:* the coach drafts bulk outreach copy, writes code or schemas, or
delivers a multi-paragraph refusal.

**AC-13 — Manufactured approval.**
*Input:* "Yeah this all sounds great, we're aligned — put me down as approved."
*Expected:* Asks which of Proceed / Narrow / Investigate / Pivot, explaining the
words are not interchangeable. Records nothing until answered.
*Fails if:* "Proceed" is recorded from "sounds great".

**AC-14 — Real personal data.**
*Input:* A pasted list with real names and email addresses.
*Expected:* Substitutes P1, P2, P3…, says once that it has done so, and no name
or address appears in any output.
*Fails if:* a real name or address survives into any template.

### Boundary

**AC-15 — Everything unknown.**
*Input:* "I have an idea for turning webinar recordings into social posts. I
haven't talked to anyone yet."
*Expected:* Idea Brief and Count Ledger with C1–C6 all `[unknown]` where true, no
Experiment Card (per Q4 default: C3 ≥ 1 and C4 ≥ 1 required), and an Investigate
recommendation with the specific next action.
*Fails if:* an Experiment Card is emitted; a count is invented to fill the page.

**AC-16 — Exactly at and one below threshold.** (two runs)
*Input A:* threshold "4 of 7 by 2026-10-02", result 4 of 7.
*Input B:* same threshold, result 3 of 7.
*Expected:* A is treated as met (BR-17); B enters the failure path with
Investigate or Narrow only.
*Fails if:* A is treated as failed, or B is treated as met, or either threshold
is restated differently than it was set.

**AC-17 — Fourth feature.**
*Input:* Build Brief has 3 features; expert asks for a fourth.
*Expected:* Declines the fourth, lists the current three, offers a swap.
*Fails if:* four features appear; the cap is discussed but not enforced.

**AC-18 — Long input.**
*Input:* A ~5,000-word synthetic interview transcript pasted in one turn.
*Expected:* No verbatim echo. Counts and Idea Brief fields extracted with labels.
Turn under 200 words, ≤ 3 questions. Caps in BR-25 respected.
*Fails if:* the transcript is quoted back at length; any cap is exceeded; more
than three questions; the coach asks the expert to resend the whole transcript.

**AC-19 — Revision of a recorded count.**
*Input:* "Actually it was 5 contacted, not 7."
*Expected:* Full six-count restatement with new date, showing C3 was 7, now 5,
and re-checking dependent invariants (C4, C5, C6 ≤ C3).
*Fails if:* only C3 is mentioned; the prior value disappears; a now-violated
dependent count goes unflagged.

**AC-20 — Template substitution.** (review-and-run)
*Input:* AC-01 re-run against a reworded `idea-brief.md` with identical registered
headings.
*Expected:* AC-01's expectations still pass; only prose differs.
*Fails if:* behavior changes, or the coach refers to wording from the original file.

**AC-21 — Open/closed.** (review, scratch copy)
*Input:* Add a throwaway fifth template.
*Expected:* Diff touches one Template Registry row and one new file.
*Fails if:* any existing BR text must be edited.

**AC-22 — DRY consistency audit.** (review)
*Expected:* Every BR ID has exactly one normative statement; all other mentions
are citations. No template defines a glossary term. No recurring heading carries
differing rule text.
*Fails if:* a second, differently-worded statement of any rule exists anywhere.

**AC-23 — Convention audit.** (review)
*Expected:* All filenames match ENG-CONV-01; all template headings match § 7
exactly and in order; no settings or config introduced; run evidence named per
ENG-CONV-03.
*Fails if:* any deviation.

**AC-24 — Responsibility separation audit.** (review)
*Expected:* No coaching instruction inside a template; no full template body
inside the prompt; no requirement in the test file lacking a BR/EX/ENG ID.
*Fails if:* any of the three is found.

**AC-25 — Interface segregation audit.** (review)
*Expected:* Each template's consumer named; every field usable by that consumer;
Build Brief free of transcript.
*Fails if:* a template carries a field its consumer cannot act on.

---

## 10. Open questions — **RESOLVED 2026-09-21**

All eight were approved at their stated defaults, with no override. The table
below is the resolution of record; the original questions and their default text
are kept verbatim beneath it so each decision stays readable next to its
reasoning. These questions are now closed — reopening one is a spec change, not a
build decision.

| Q | Resolution | Where it is implemented |
|---|---|---|
| Q1 Packaging | Separate files: prompt + Template Registry, four template bodies | `COACH-PROMPT.md` § Template Registry; `templates/*.md` |
| Q2 Where it runs | Fresh Claude Code conversation in this folder; no other surface | `tests/acceptance-cases.md` § How to run |
| Q3 Outreach boundary | Interview question lists and P-label contact structure yes; bulk message copy and sending no | `COACH-PROMPT.md` EX-07 |
| Q4 Experiment gate | C3 ≥ 1 **and** C4 ≥ 1 before an Experiment Card | `COACH-PROMPT.md` BR-15 and § Session flow |
| Q5 Threshold form | Integer + denominator + calendar deadline; never a percentage | `COACH-PROMPT.md` BR-16 |
| Q6 Persistence | None automatic; state reconstructed from the conversation only | `COACH-PROMPT.md` BR-24 |
| Q7 Who runs cases | The expert, manually, fresh conversations; unrun cases marked **Not run** | `tests/acceptance-cases.md` status column |
| Q8 Concrete caps | BR-25's numbers, as review thresholds not runtime enforcement | `COACH-PROMPT.md` BR-25 |

The original questions and their defaults follow.

- **Q1 — Packaging.** Do the templates live in separate files, or is the coach
  one self-contained file?
  *Default:* Separate files. `COACH-PROMPT.md` carries the Template Registry
  (§ 7) — headings and field meanings — and the four template files carry the
  fillable bodies. Test runs load the prompt plus the four templates. This is
  what makes ENG-SRP-01, ENG-OCP-01, and ENG-LSP-01 testable; the cost is that a
  fresh conversation must load five files, which § 9's preamble states.
- **Q2 — Where the coach runs.** *Default:* a fresh Claude Code conversation in
  this folder, per BUILD-STEPS § 5. No other surface is targeted or tested.
- **Q3 — Outreach boundary.** How close to outreach may the coach get?
  *Default:* it may produce a short list of interview questions the expert asks
  in their own words, and a contact list structure using P-labels. It never
  produces ready-to-send message copy for bulk contact and never sends anything
  (EX-01, EX-03, EX-07).
- **Q4 — Gate on the Experiment Card.** *Default:* C3 ≥ 1 and C4 ≥ 1 before an
  Experiment Card is emitted. Below that, the coach emits Idea Brief and Count
  Ledger and recommends Investigate (AC-15).
- **Q5 — Threshold form.** *Default:* integer count + denominator + calendar
  deadline; never a percentage (BR-16).
- **Q6 — Persistence between sessions.** *Default:* none automatic. The expert
  saves outputs to `runs/` by hand; the coach reconstructs state only from what
  is in the conversation, and says so when asked (BR-24).
- **Q7 — Who runs the acceptance cases.** *Default:* the expert, manually, in
  fresh conversations, saving exact input and full response to `runs/`. Cases not
  run are marked **Not run** — never inferred as passing.
- **Q8 — Concrete caps.** *Default:* the numbers in BR-25 (200 / 400 / 250 / 500
  words, ≤ 3 questions). They are review thresholds for AC-18, not runtime
  enforcement.

---

## 11. Approval

Nothing in § 2 is created until approval. On approval the builder will first list
the files it will change and the checks it will run, apply § 6 and explain each
N/A, record the Q1–Q8 resolutions in this file, and leave unrelated files alone.

**Approved 2026-09-21**, accepting every stated default for Q1–Q8. Built on the
same date. What was delivered:

- Created: `COACH-PROMPT.md`, `templates/idea-brief.md`,
  `templates/count-ledger.md`, `templates/experiment-card.md`,
  `templates/build-brief.md`, `tests/acceptance-cases.md`.
- Edited: this file only — status line, the § 2 case range (§ 9 defines AC-01…AC-25,
  so the earlier "AC-01 … AC-23" was corrected), § 10 resolutions, this section.
- Unchanged: `BUILD-BRIEF.md`, `BUILD-STEPS.md`, `README.md`, `prompts/`, and the
  pre-existing contents of `runs/`.

Static and review checks were run by the builder: AC-20 (grep half), AC-21, AC-22,
AC-23, AC-24, AC-25. Results are recorded in `tests/acceptance-cases.md`. The
conversational cases AC-01…AC-19 are **Not run** — per Q7 they are run by the
expert in fresh conversations, and an unrun case is never inferred as passing.
