# Exercise 01 — Interview Loop Redesign Under AI
> Module: `mod-108-hiring-performance-and-feedback-with-ai-in-the-loop`
> Exercise type: **design** (worked loop blueprint + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **named, five-stage interview loop** targeted at a mid-to-senior software engineer on a team that uses AI assistants daily. The artifact is three files glued together: a loop blueprint (stage-by-stage), a written decision rationale per stage, and a scored rubric that an interviewer can fill out in under 15 minutes post-debrief. It is explicit about which stages allow AI, which forbid it, what signal each stage produces, and how two different interviewers reach the same score on the same candidate.

The audience is a hiring manager redesigning an existing engineering loop, not a founder designing one from scratch. The output must be reviewable by legal, by senior engineers who will interview on it, and by a first-time candidate reading the AI-use policy page — all three audiences in the same document.

A good loop redesign looks like:

- Every stage has a **named signal** (e.g. "unaided debugging reflex", "AI-review discipline") and no two stages overlap on signal more than ~30%.
- The AI-use policy is written as a sentence the candidate reads *before* the stage, not inferred — including which tools, which model tier, whether the internet is allowed, whether they can paste from a chat into the editor.
- At least one stage is deliberately **AI-free**, and the rationale explains what unaided signal would otherwise be invisible.
- At least one stage is **AI-required** (not merely permitted), because allowed-but-optional AI use produces unreliable signal when the candidate guesses what the panel "wants to see".
- The rubric is calibrated so that "copies AI output without review" lands at 0 or 1 on multiple rows, and "uses AI to go deeper than a solo engineer would in the time box" lands at 3.
- There is a dedicated sub-stage or artifact review for **loop bias and legal exposure** that hooks into `Exercise 05` (LL144/EEOC checklist) before the loop goes live.
- Debriefs reference specific timestamps or artifacts, not vibes, because AI-augmented sessions compress visible work and shift it into prompts the panel must inspect.
- Calibration is a scheduled recurring activity, not a one-time launch step, because candidate AI fluency shifts quarter-to-quarter.

Design decisions this solution encodes:

1. **Judgment over draft speed.** Every stage rewards the candidate's ability to defend, revise, and reject AI output. Raw token-production speed is deliberately not scored.
2. **Mixed AI policy across the loop.** One AI-free stage, one AI-required stage, three AI-permitted stages with explicit tool lists. A uniform policy (all-allowed or all-forbidden) under-tests the role.
3. **Observable prompts.** Where AI is used, the candidate's prompts and the model's outputs are captured as scored artifacts — the prompt is part of the work product.
4. **Rubric shifts, not inflation.** Under AI, the bar on "produced working code" rises (because tooling makes this cheaper) and the bar on "caught the subtle wrong answer" rises further. The bar on "communication" is unchanged.
5. **Bias/legal review is a loop stage, not a post-mortem.** Before go-live and quarterly thereafter, a named reviewer runs the loop against the LL144/EEOC checklist from `Exercise 05`.
6. **Calibration is harder, so it is scheduled.** Two-interviewer shadow pairs for the first 6 loops after any rubric edit, with a written disagreement log.

The deliverable itself is three artifacts in one directory:

- `loop-blueprint.md` — the five-stage fenced spec from section 2.2 below, deployable to a wiki unmodified.
- `rationale.md` — the "Why this and not that" notes, including which alternatives were rejected and why.
- `rubric.md` — the eight-row scoring sheet from section 4, plus a `debrief-template.md` that cites rubric rows by number and requires artifact citation.

## 2. Worked implementation — the redesigned loop

### 2.1 Scope and prerequisites

- **Role profile:** IC3–IC5 backend/full-stack engineer on a product team that mandates AI-assistant usage (Cursor, Claude Code, Copilot) and reviews AI-touched PRs with the same standard as human-authored PRs.
- **Loop length:** 5 stages across ~4.5 hours of candidate time, scheduled across at most two days.
- **Panel size:** 5 interviewers + 1 debrief lead. The debrief lead is not an interviewer on the same candidate.
- **Precondition artifacts:** the role scorecard, the AI-use policy page sent to the candidate 48h before onsite, the written consent form covering recording/replay of coding stages, the tool allow-list with model tiers, and the Stage 2 "no-AI" statement the candidate acknowledges in writing.
- **Panel training precondition:** every interviewer has completed one mock loop as candidate and one as scorer against the rubric in this document in the last six months. No training, no slot on the panel.
- **Candidate disclosure (sent 48h before):** which stages allow AI, which forbid it, which require it; which tools are approved; whether recording occurs and what is retained; how AI use affects scoring; and a named contact for accommodations.
- **Not in scope here:** sourcing, recruiter screen content, offer committee mechanics. Hooks into `Exercise 02` (levelling) for signal-to-level mapping, `Exercise 05` for legal review, and `Exercise 06` for headcount justification tying this role to a plan.

### 2.2 The loop blueprint

```text
STAGE 1 — Recruiter + Hiring Manager screen (45 min, AI-permitted on candidate side only)
  Purpose:        Fit, motivation, pay transparency (per LL144 adjacent practice —
                  see Exercise 05), baseline on current AI practice.
  Candidate framing: "We use AI tools daily. Please tell us how you use them now, and
                      what you'd want to use here. You may reference notes or tools."
  AI-use policy:  Candidate may use any tool. Interviewer does not use AI during the
                  call to avoid introducing an asymmetric observation-of-observer loop.
  Signal:         Self-awareness on AI workflow; realistic scope claims; motivation;
                  does the candidate map AI use to outcomes, or to activity?
  Calibration:    Two-question standard probe: "Walk me through a recent PR where AI
                  saved you time," and "a recent PR where AI misled you." Score both
                  against the Stage 1 rubric row; refuse to rate without both answers.
  Anti-signal:    "I only use AI for boilerplate" (under-use) and "I don't review
                  model output, it's usually right" (over-use) are both penalised.
```

```text
STAGE 2 — Unaided code reasoning (60 min, AI-FORBIDDEN, open-notes/docs)
  Purpose:        Isolate the human's unaided debugging + reading reflex. This is the
                  only stage that answers "what is left when the tools go down?" — a
                  real condition during incidents, airgapped envs, and novel bugs.
  Candidate framing: "For this hour only, no AI assistants, no chat-based search.
                      Language docs and man pages are fine. We are measuring how you
                      reason when the tools are unavailable."
  AI-use policy:  No LLM assistants. Editor autocomplete limited to LSP, not Copilot.
                  Browser allowed for official docs only; interviewer observes tabs
                  via shared screen. Candidates with an approved accommodation that
                  overlaps with assistants follow the documented exception path.
  Signal:         Reading unfamiliar code; hypothesis formation; terminal fluency;
                  willingness to say "I don't know, here's how I'd find out"; does
                  the candidate narrate their reasoning without being prompted?
  Calibration:    Fixed bug set of 3 (easy/med/hard), rotated quarterly, scored on
                  time-to-first-correct-hypothesis, not time-to-fix. Scorer uses
                  a stopwatch on hypothesis-stated milestones, not on green tests.
  Anti-signal:    Running the test suite in a loop waiting for green; refusing to
                  read the code before changing it; silent debugging with no narration.
```

```text
STAGE 3 — AI-required feature slice (90 min, AI-REQUIRED, prompts captured)
  Purpose:        Observe how the candidate actually works in the role. Measure
                  prompt quality, review discipline, and knowing-when-to-stop.
  Candidate framing: "Build this small feature using Cursor or Claude Code — you
                      must use an assistant. We will read your prompts and the
                      model's responses alongside the diff. Your prompts are part
                      of the work we evaluate; terse prompts are fine, but blind
                      acceptance of model output is scored like blind acceptance
                      of a teammate's PR."
  AI-use policy:  Required. Candidate picks the tool from an approved list that
                  the panel has pre-tested. Session is screen-recorded with
                  consent obtained at loop invite. Prompt transcript saved to the
                  candidate's folder and purged per the retention policy.
  Signal:         Prompt specificity; rejection of bad suggestions; test-first vs
                  vibe-coding; whether they read the diff before accepting; whether
                  they notice when the model repeats itself or hallucinates an API.
  Calibration:    Scored against a prompt-quality rubric separate from code
                  rubric. Two interviewers watch — one live, one reviews recording.
                  Both score independently before the debrief, no discussion first.
  Anti-signal:    Accepting a diff that doesn't compile without re-prompting; using
                  the model to generate tests and then not reading them; silent
                  sessions with no prompt revisions across 90 minutes.
```

```text
STAGE 4 — Code review of AI-generated PR (45 min, AI-permitted for lookup only)
  Purpose:        Test review judgment on code that looks plausible but has at
                  least two embedded defects: one obvious (compile/style/obvious
                  logic) and one subtle semantic bug the model produced in a
                  real session. The subtle bug should map to a class of failure
                  the team actually sees in production.
  Candidate framing: "This PR was drafted by an AI assistant. Review it as you
                      would a teammate's PR. You may use AI to look things up but
                      not to review the diff for you. Comment inline."
  AI-use policy:  Lookup-only. Candidate must not paste the diff into a chat and
                  ask for a review. Interviewer observes shared screen; use of
                  AI to look up a specific API is fine and expected.
  Signal:         Catches the subtle bug; writes review comments at the right
                  altitude; distinguishes "style nit" from "will cause an incident";
                  proposes a regression test; asks the right clarifying question
                  of the (fictional) PR author before approving.
  Calibration:    Same PR for every candidate for a quarter. Scored on bugs-found
                  (binary per bug) + review-comment-quality (0-3). Scorer records
                  the timestamp of the subtle-bug catch; used in rubric row 5.
  Anti-signal:    Approving without comments; comments that are entirely
                  stylistic; copying the diff into a chat to "sanity-check it".
```

```text
STAGE 5 — System design + production incident walk-through (75 min, AI-permitted)
  Purpose:        Judgment at the design level, plus how the candidate reasons
                  about AI-assisted systems in production (observability of
                  agents, cost, failure modes, rollback of bad AI suggestions,
                  adversarial inputs to agentic workflows — see MITRE ATLAS).
  Candidate framing: "Design X. Then we'll switch: you'll walk us through a
                      production incident on a similar system. AI tools are
                      allowed; we expect you to narrate when you use them and
                      why. 'I don't need it here' is a valid narration."
  AI-use policy:  Permitted. Candidate narrates use ("I'd ask the assistant for
                  typical quotas here"). Over-reliance (e.g. letting the model
                  pick the architecture) is explicitly penalised on the rubric.
  Signal:         Tradeoff articulation; incident instincts; knowing which parts
                  of the system an assistant *cannot* reason about correctly
                  (ownership boundaries, past incidents, on-call rotation state).
  Calibration:    Two prompts (design + incident). Debrief lead cross-checks that
                  the two signals didn't collapse into one in scoring — a
                  candidate strong on design can look strong on incidents by
                  halo alone, which this prompt structure must surface.
  Anti-signal:    Letting the assistant draw the diagram; treating "add an LLM"
                  as a design primitive without discussing eval, cost, or
                  fallback; no mention of observability for AI-touched paths.
```

### 2.3 Why this and not that

- **Why one AI-free stage and not zero.** A fully-AI-augmented loop cannot distinguish a strong engineer from a strong prompter. The AI-free stage is the control. It is deliberately bounded to 60 minutes so the candidate doesn't experience the whole day as hostile to how they actually work.
- **Why one AI-required stage and not merely permitted.** "AI is allowed" produces a strategic-guessing problem: strong candidates sometimes hide AI use thinking it looks weak; weak candidates perform without it to look strong. Requiring it collapses that game.
- **Why capture prompts as artifacts.** The prompt is where judgment shows. A candidate who writes "fix this" and accepts the first diff produces no evidence of thinking; a candidate who writes a scoped prompt with constraints and a test plan produces a lot.
- **Why a canned AI-generated PR for Stage 4 rather than letting the candidate generate one.** Comparability. Every candidate reviews the same diff with the same embedded defects. Candidate-generated code would make Stage 4 un-calibratable across a quarter.
- **Why system design *and* an incident, not either alone.** Pure greenfield design under-tests operational judgment; pure incident under-tests architectural reasoning. Together, 75 minutes is enough to see both at shallow depth, which is the right altitude for an interview.
- **Why not take-home.** Take-homes under AI are uninterpretable. The candidate may have produced the artifact with a research team of models; you cannot calibrate time or effort. Replaced by Stage 3 which gives the same signal under observation.
- **Why not pair-programming with an interviewer.** It conflates interviewer-style and candidate-skill; under AI it becomes two people prompting one model and is nearly impossible to score. Preserved only as an optional debug sub-stage in Stage 2.
- **Why bias review is a loop stage, not post-hoc.** Every interview design embeds assumptions about what "good engineer" sounds like; under AI, those assumptions get amplified by the model's own training bias (e.g. certain idiolects of prompt phrasing). Hook: before go-live, run the loop blueprint through the `Exercise 05` LL144/EEOC checklist and record the sign-off.
- **Why candidate consent is on the loop invite, not the day-of form.** Day-of consent is coerced consent — the candidate has already taken PTO. Disclosure 48h before gives the candidate a real opportunity to decline recording without losing the loop.
- **Why Stage 2's "no-AI" is enforced observationally, not technologically.** Attempts to lock down the candidate's machine are brittle and hostile. The interviewer watches shared screen, the stage is short, and the rubric credits the hypothesis trajectory rather than output volume — making cheating low-value even if attempted.
- **Why the rubric does not have a dedicated "used AI well" row.** "Used AI well" is not a signal; it is a composite of prompt quality, review discipline, and knowing when to stop — all scored on separate rows. A composite row would double-count.

### 2.4 Calibration over time

Interviewer calibration under AI degrades faster than under classical loops for three reasons: (a) model capabilities shift mid-quarter, changing what "normal" output looks like in Stage 3; (b) candidate AI fluency distribution moves right over time, so last quarter's "3" becomes this quarter's "2"; (c) interviewers themselves use AI more effectively each quarter and unconsciously raise their bar. The design handles this with:

- A **scorer-disagreement log** maintained by the debrief lead. Any two interviewers who differ by more than 1 point on the same row write a short note on why. The log is reviewed at the quarterly rubric review.
- A **rubric-version stamp** on every scored loop. When a candidate is rescored after a rubric edit, the original score is preserved alongside the new score; raw score comparisons across versions are forbidden in hire/no-hire discussions.
- A **shadow-scorer rotation** so every interviewer shadow-scores one loop per quarter without being on the panel, writing their own scores and comparing to the panel's.
- A **tool-drift watch**: if an approved tool releases a new model tier, the panel re-tests Stage 3 and Stage 4 content against it before the next real candidate, because a stronger model can change what counts as "good prompting" visible to the panel.
- A **cross-ladder sanity check** against `Exercise 02`: the loop should not implicitly screen for a level above or below the open req. If three recent hires at a given level would fail the new loop, the bar has drifted; retune rather than ship.
- A **retention check**: the retention policy for recordings and prompt transcripts should be the shortest period consistent with calibration (typically 90 days). Longer retention invites discovery risk without calibration benefit.

## 3. Validation steps

These are *pre-rollout* checks. Each should produce a written artifact filed with the loop version, not just an interviewer's memory. Before the redesigned loop runs on a real candidate:

1. **Dry-run the whole loop with two internal engineers** of known calibration (one strong, one average). Confirm the rubric separates them by at least 6 points out of 24. If it doesn't, the rubric is under-discriminating.
2. **Red-team Stage 3 by having an engineer try to pass it using only "accept all suggestions" with no prompt editing.** If they score above the pass bar, raise the prompt-quality weighting.
3. **Confirm Stage 4's embedded subtle bug is actually subtle** by giving the PR to three engineers with a 15-minute timer; at least one should miss the subtle bug. If all three catch it instantly, it is not subtle enough.
4. **Run the loop blueprint through the LL144/EEOC checklist from `Exercise 05`.** Document the reviewer, date, and any adverse-impact flags raised. No go-live without this.
5. **Verify consent language** on the Stage 3 recording covers storage duration, access list, deletion on request, and jurisdiction. Legal sign-off attached.
6. **Verify the AI-tool allow-list is actually available** in the interview environment (accounts provisioned, no corp-network blocks, model tier matches policy). A candidate burning 10 minutes on auth invalidates the stage.
7. **Calibration pair-up:** schedule two interviewers to shadow-score the first six real loops. Written disagreement log reviewed by the debrief lead.
8. **Define the kill criteria:** what observation during the first quarter would make you pull the loop. Example: >30% scorer disagreement on Stage 3 rubric rows, or any adverse-impact flag from Exercise 05's quarterly recheck.
9. **Stress-test the AI-tool allow-list for accessibility.** Confirm the approved tools work for candidates on assistive tech (screen readers with Copilot/Cursor), and that the "no-AI" Stage 2 does not exclude candidates whose approved accommodation includes spell/grammar assistance that overlaps with LLMs. Document the exception path.
10. **Debrief dry-run.** Run a debrief on the dry-run candidate from step 1 with the real panel template. Confirm that rubric rows requiring artifact citation (3, 4, 5, 6) actually have cited artifacts, and that the debrief lead can produce a hire/no-hire recommendation in under 25 minutes. If it takes longer, the rubric or the debrief template is too heavy for weekly use.

## 4. Rubric

Each row is scored 0–3 against observed evidence from the stage(s) listed. Pass bar: **16 / 24**, with no row below 1 and at least one 3 among rows 3, 4, 6. The three-row 3-score minimum enforces that a candidate passes on judgment under AI (prompt quality, review discipline, altitude) and not on raw breadth across weaker rows.

| #  | Criterion                                           | 0                                                   | 1                                                      | 2                                                            | 3                                                                            |
|----|-----------------------------------------------------|-----------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------------|------------------------------------------------------------------------------|
| 1  | Self-aware AI workflow (Stage 1)                    | Claims no use, or claims uncritical use             | Describes current use in generic terms                 | Names specific wins and specific failures                    | Names failure modes, mitigations, and when they stopped using AI for a task  |
| 2  | Unaided debugging reflex (Stage 2)                  | Stalls; asks for the answer                         | Forms hypotheses but cannot prioritise                 | Prioritised hypotheses, used tools correctly                 | Found the bug and articulated why the obvious hypothesis was wrong           |
| 3  | Prompt quality (Stage 3)                            | One-line prompts; accepts first suggestion          | Adds constraints after failure                         | Scoped prompts up-front with constraints and acceptance tests| Prompts also encode rollback / verification, iterates on prompt not just code|
| 4  | AI-output review discipline (Stage 3)               | Accepts without reading                             | Skims diff, catches obvious issues                     | Rejects suggestions with reasoning                           | Rewrites or re-prompts to fix semantic issues the candidate spotted first    |
| 5  | Subtle-bug catch rate (Stage 4)                     | Misses both planted bugs                            | Catches obvious bug only                               | Catches both bugs                                            | Catches both plus an unplanted issue worth flagging                          |
| 6  | Review-comment altitude (Stage 4)                   | Only style nits, or only vague "LGTM-ish" comments  | Mix of style and substance, no prioritisation          | Prioritises substance; suggests concrete fix                 | Separates blocking from non-blocking; proposes test to prevent regression    |
| 7  | System-design tradeoff articulation (Stage 5)       | Picks a stack without justification                 | Lists tradeoffs without picking                        | Picks and defends under one axis (cost, latency, or team)    | Picks, defends under multiple axes, and names what would change their mind   |
| 8  | AI-in-production judgment (Stage 5)                 | Treats AI as magic; or dismisses it entirely        | Mentions one failure mode                              | Names observability and rollback for AI-assisted paths       | Names cost, eval, rollback, and when *not* to use an assistant in that system|

**Scoring mechanics.** Each interviewer scores only the rows mapped to their stage, plus any row they have direct evidence for. The debrief lead aggregates. Unscored rows are not averaged to a middle value — they are marked N/E ("no evidence") and the pass bar is adjusted proportionally (e.g. with one row N/E, the bar becomes 14/21). No interviewer scores a row from secondhand description during debrief; if nobody saw the signal, nobody saw the signal, and the recruiter schedules a bridge call rather than guessing.

**What moves up under AI, what stays.** Rows 1, 3, 4, 5, 6, 8 all move up under AI augmentation: the floor rises because the tooling makes basic competence cheaper, and the discriminator shifts to judgment and review. Row 2 (unaided reasoning) stays at the same bar — the AI-free stage is designed to measure the unaugmented capability directly. Row 7 (tradeoff articulation) stays because design communication is a human-to-human signal regardless of tooling.

## 5. Common mistakes

- **Letting Stage 3 drift into a take-home.** If the panel sends the feature spec in advance "to save time", the candidate preps with a model offline and the signal collapses. Spec is handed over at the start of the 90 minutes.
- **Scoring Stage 3 on whether the code "compiled and passed tests".** Under AI, almost all submissions compile. The discriminator is prompt quality and review discipline; weight those higher than output correctness.
- **Using a real recent PR for Stage 4.** Real PRs cannot be reused across candidates without bias (seniors who worked on that area have a tell). Use a synthetic PR with planted defects.
- **Letting interviewers improvise follow-ups without a shared bank.** Under AI, candidates probe the interviewer's knowledge; off-script follow-ups create wildly different difficulty between candidates. Maintain a shared bank with difficulty tags.
- **No explicit consent before recording Stage 3.** Recording prompts and screen without written consent is a legal exposure; no amount of "it's just for calibration" fixes it after the fact.
- **Treating the rubric as a scorecard and not a calibration tool.** If rows 3, 4, and 6 are not re-read in the debrief with the specific prompt/transcript cited, the rubric is decorative. Require citation in the debrief doc.
- **Inflating the "working code" bar without adjusting the time box.** When you raise expectations because "AI makes it faster", candidates run out of time on review and defence — the two things you actually wanted to see. Keep the scope tight.
- **Skipping the LL144/EEOC loop review** because the loop was "not AI-scored." The loop *design* is still subject to adverse-impact review; AI scoring thresholds are only one part of the regulatory surface.
- **One-time calibration at launch.** Candidate AI fluency and tool capabilities shift each quarter; a loop calibrated in Q1 is miscalibrated by Q3. Schedule a recalibration at every rubric edit and at minimum every six months.
- **Mixing panel members who disagree on the AI-use policy** without resolving it first. If one interviewer silently penalises AI use in Stage 5 and another rewards it, you are measuring interviewer, not candidate. Policy alignment is a pre-loop prerequisite.
- **Rotating the Stage 2 bug set too often.** Quarterly rotation is a floor; rotating every candidate makes difficulty uncontrollable and destroys cross-candidate comparison. Lock the set for a quarter, archive scores with the bug-set version.
- **Confusing "AI fluency" with "AI maximalism" in the debrief.** A candidate who uses AI less than another candidate is not weaker on that axis if their outputs are stronger. Score the signals (rows 3–4, 8), not the token count.
- **Relying on scorer memory instead of citation.** By debrief time (often 24–48 hours after Stage 3), interviewers remember impressions and lose the specific prompt-and-response moments that justified the score. Require the scorer to paste at least one prompt-and-model-reply pair per non-zero score on rows 3 and 4 directly into the debrief doc.
- **Letting one strong stage mask a weak one.** AI augmentation makes Stage 3 outputs look impressive; it is tempting to treat Stage 3 as a global indicator. The rubric's cross-row-3-requirement on rows 3, 4, 6 prevents this — follow it even when one stage was unusually strong.
- **Running the loop before the Exercise 05 review.** "We'll bias-check the loop after a few candidates" means the first cohort is the unofficial bias experiment. Not acceptable. No live loop before written sign-off.

## 6. References

- **NIST AI Risk Management Framework** — https://www.nist.gov/itl/ai-risk-management-framework
  Applies to the loop itself as a *decision system*. The GOVERN function maps to the AI-use policy page, consent form, and the written sign-off from the LL144/EEOC reviewer. MAP applies to identifying which stages have AI in the decision path and what harms (e.g. adverse impact, consent violation) apply. MEASURE applies to the rubric and the quarterly recalibration log — in particular, scorer-disagreement rate is the measurement artifact. MANAGE applies to the kill criteria defined in validation step 8, and to the exception path for accommodations in validation step 9.
- **OWASP Machine Learning Security Top 10** — https://owasp.org/www-project-machine-learning-security-top-10/
  Applies to Stage 3 and Stage 4 content design. A good Stage 4 planted-bug set should include at least one class of error that maps to OWASP ML risks (e.g. input manipulation, output integrity, unsafe handling of model output in downstream code) so that the candidate's review discipline is tested against failure modes the team actually faces in production AI code paths. Also informs what "AI-in-production judgment" means on rubric row 8 — a candidate who cannot name any of these risk categories cannot score 3 on row 8.
- **MITRE ATLAS** — https://atlas.mitre.org/
  Applies to Stage 5's AI-in-production discussion. When probing whether the candidate understands adversarial failure modes of AI-assisted systems (prompt injection in agentic workflows, model exfiltration, poisoned training data in RAG, abuse of tool-calling), ATLAS tactic IDs are the vocabulary the panel should use in debrief so that "AI security awareness" is not vibes-scored. Panel training should include at least one ATLAS tactic walkthrough per quarter so interviewers can recognise the signal when a candidate raises it unprompted.

Regulatory frames outside the above source list that this loop interacts with: **NYC Local Law 144** (automated employment decision tools — relevant if any scoring becomes algorithmic, which this loop deliberately avoids but which Exercise 05 treats in depth) and the **US EEOC** disparate-impact framework (relevant to any loop regardless of AI use). Do not treat these as substitutes for the sources above; they are complementary and are worked in detail in `Exercise 05`. If your jurisdiction or role recruits outside the US, add the applicable local frame to the Stage 1 pay-transparency script and to the Exercise 05 checklist before go-live.
