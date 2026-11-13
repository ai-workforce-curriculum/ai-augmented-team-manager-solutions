# Exercise 04 — Underperformance Root-Cause Drill
> Module: mod-108 Hiring, Performance, and Feedback with AI in the Loop
> Exercise type: **design** (worked taxonomy + diagnostic drill + decision tree + grading rubric)

## 1. Solution overview

The deliverable is a **diagnostic artifact** a manager runs *before* they decide on a remedy for an IC who is tracking below the bar. It has four parts: a **root-cause taxonomy** (named, mutually distinguishable), an **ordered diagnostic drill** (observations + 1:1 prompts), a **decision tree** from signal to named remedy, and a **remedy table** that also lists the *wrong* remedy for each cause. The purpose is to stop the most common manager failure mode in AI-augmented teams: pattern-matching a single symptom ("shipping less complex work", "PRs bouncing in review", "slow to pick up a new stack") to a single remedy ("they need to practice more", "put them on a PIP") when the AI-in-the-loop shift has made the symptom-to-cause mapping genuinely many-to-many.

What good looks like:

- The taxonomy names **over-reliance** and **review-depth collapse** as first-class causes, not as sub-flavors of "skill gap." They map to different remedies.
- The drill is explicitly a **diagnostic sequence**, not a performance conversation. The output is a hypothesis, not a verdict.
- The decision tree reaches `route-to-HRBP`, `route-to-EAP`, and `escalate-to-skip` as valid terminal states — not every leaf is a coaching plan.
- The manager can distinguish **cannot** (skill/tool gap) from **chose not to** (fit, motivation, misaligned incentives) from **did not notice** (attention, review-depth collapse, context-poor prompting). These three have three different remedies and conflating them is the top failure mode.
- The artifact is **compatible with Exercise 02's levelling criteria** — the drill references which levelling dimension is off, not an abstract "doing badly."

Design decisions:

1. **Taxonomy of seven causes, not three.** A three-bucket model ("skill / fit / effort") was the pre-AI standard. It no longer discriminates. Over-reliance, review-depth collapse, and context-poor prompting are distinguishable in the AI-in-the-loop setting and warrant their own remedies.
2. **"Mistaken-for" column is mandatory.** Each cause lists what it is commonly misread as. This is the artifact's primary defense against snap diagnosis.
3. **Attribution hygiene is called out explicitly.** When an IC repeatedly attributes failures to "the model was wrong" or "the agent lost context" without evidence, that *attribution pattern itself* is a diagnostic signal, not a fact about the tooling.
4. **PIP is not a step in the drill.** The drill precedes any PIP decision. If the drill terminates in a coaching plan and the coaching plan fails against defined checkpoints, *then* the PIP conversation (with HRBP, with the levelling rubric from Exercise 02) begins. A PIP is a legal/HR instrument, not a diagnostic tool.
5. **1:1 prompts are open and observational, not leading.** The script skeleton is written so the IC can disconfirm the manager's current hypothesis. Leading questions ("are you leaning on the assistant too much?") contaminate the signal.
6. **The drill documents itself.** Every step produces a dated note in the manager's private 1:1 doc. This is how the manager (a) tracks whether the hypothesis is holding up over weeks and (b) protects the IC if the case later escalates to HR — the record shows diagnostic process, not predetermined conclusion.
7. **The drill is loop-shaped, not one-shot.** A failed 4-week coaching plan re-enters the drill at Step 1 with a *new* hypothesis, not a continuation of the old one. This matters because the first cause the manager named may have been plausible but wrong, and a second pass through the drill is cheaper than six months of a coaching plan that was aimed at the wrong target.

Cross-references: **mod-108 Exercise 02 (levelling criteria refresh)** supplies the dimension names the drill points at ("scope," "review craft," "ambiguity handling"). **mod-107 Exercise 04 (burnout and over-reliance 1:1 prompts)** supplies the prompt library this drill borrows from for the over-reliance and health-adjacent branches.

## 2. Implementation — the diagnostic artifact

### 2.1 Root-cause taxonomy

```markdown
| # | Named cause | Symptoms you'd see | Commonly mistaken for |
|---|---|---|---|
| C1 | **Skill atrophy** — fundamentals the IC used to have have degraded from disuse | Cannot explain code they authored last week; cannot debug without prompting the assistant; regressions when the assistant is unavailable; shallow answers on stack internals | "Skill gap" (they never had it) — wrong remedy. Atrophy needs practice-reps on known ground; a gap needs new instruction. |
| C2 | **Over-reliance on the assistant** — IC defers judgement to the assistant even where they have (or had) capability | Ships assistant output with minimal transformation; cannot defend design choices in review; "the model suggested it" as rationale; drops to zero throughput during assistant outages | "Laziness" or "effort" problem — wrong remedy. Over-reliance is usually an identity/tooling habit, not a motivation issue. |
| C3 | **Context-poor prompting** — IC produces low-quality assistant output because they feed low-context prompts | Assistant output is generic, wrong-shaped, or ignores local conventions; many iterations to get to merge; PRs with obvious codebase-mismatch | "The tool is bad" or "the model is dumb" — wrong remedy. The input is the problem, not the model. |
| C4 | **Review-depth collapse** — IC ships more volume but the review (their own and others') has gotten shallower | Rising PR throughput but rising revert rate; bugs in merged code the IC "reviewed"; comments like "LGTM" on 400-line diffs; IC's own PRs defended less in review | "Productivity win" — wrong remedy. Volume looks like a win on dashboards; the quality signal is in reverts, incidents, and downstream fix cost. |
| C5 | **Task-fit / scope mismatch** — the work assigned is above, below, or orthogonal to the IC's current strengths | Pattern of stalling on one *kind* of work (e.g., frontend) while thriving elsewhere; disengagement correlated with topic, not time | "They're underperforming overall" — wrong remedy. Reshape the work before reshaping the person. |
| C6 | **Workload / sustained overload** — the IC is doing too much, has been for too long | Declining quality over weeks; missed small things; irritability in review; late-night commits; vacation days not taken | "Attitude problem" — wrong remedy. Chronic load degrades every other dimension and will mimic every other cause on this list. |
| C7 | **Health, life event, or wellness** — something outside work is dominating cognitive load | Step-change (not drift) in output; uncharacteristic errors; withdrawal from team rituals; non-work signals (if volunteered) | "Lost motivation" — wrong remedy. This is HRBP/EAP territory, not a coaching plan. |
| C8 | **Attribution-hygiene failure** — IC systematically attributes errors to the assistant without evidence | "The model was wrong" without logs, repro, or the original prompt; same failure modes recurring across models/tools; no post-hoc reflection on their own role | Interpreted as C3 (bad prompting) or C2 (over-reliance) — related but distinct: this is a *reasoning hygiene* issue about how the IC explains failure, which blocks any coaching plan from landing. |
```

Eight entries, not seven: C8 was added because the common mistake is to collapse it into C2 or C3, and the remedy (teach the IC to produce a failure write-up with inputs, outputs, and their own decision chain) is distinct from either.

### 2.2 Diagnostic drill — ordered observations and 1:1 prompts

The manager runs **steps 1-4 before any 1:1**, then uses the 1:1 prompt skeleton to disconfirm or refine the hypothesis. The drill is a diagnostic sequence; it is not a judgment session and the IC should not be told "we are running a diagnostic drill on you." The frame to the IC is a standard 1:1.

```markdown
### Step 1 — Baseline the signal (manager, solo, 20 min)
- What specifically is below the bar? Name the dimension from the Exercise 02 levelling rubric (scope, ambiguity, review craft, collaboration, impact). "Just underperforming" is not usable.
- Over what window? One sprint is not a signal. 4-8 weeks is.
- Is the signal a step-change or a drift? Step-change points at C7 or C6; drift points at C1/C2/C4.
- What is the counterfactual — what would an at-level IC have delivered on the same work?

### Step 2 — Pull the artifacts (manager, solo, 30-45 min)
- Last 6-10 PRs authored by the IC: diff size, review comments received, revert/follow-up fix rate, time-to-merge.
- Last 6-10 PRs reviewed by the IC: comment density, catches made, LGTMs on large diffs.
- Incidents or escalations touching code the IC owns in the window.
- Any assistant-session transcripts/logs the IC voluntarily shares in team channels (do not pull private logs without consent).

### Step 3 — Pattern-match against the taxonomy (manager, solo, 15 min)
- For each row in the taxonomy, write one sentence: "Evidence for this cause is ___ / Evidence against is ___."
- Rank the top two most likely causes. Do not pick one yet.
- Flag any evidence pointing at C7 (health/life). If present, the next step is HRBP/EAP routing, not a coaching 1:1.

### Step 4 — Attribution scan (manager, solo, 10 min)
- Across the IC's last 6-10 written explanations of "why this didn't work" (PR descriptions, incident notes, standup updates), how often is the assistant named as the cause vs. the IC's own decisions? If > 60% of failures are attributed to the tool without a repro, flag C8.

### Step 5 — The 1:1 (45-60 min, private, no laptop open for notes, write up after)
Open with the IC's view first. Do not lead. Prompts, in order:

1. "Walk me through the last PR you felt best about. What decisions were yours, what were the assistant's, how did you check its work?"
   *Listens for: ownership language, specific review decisions, awareness of what the assistant did badly. Weak signal here points at C2/C4/C8.*

2. "Walk me through a recent PR you felt worst about. Same question."
   *Listens for: whether the IC can name their own error, or only the tool's.*

3. "If the assistant was down for a week, what parts of your current work get harder, and by how much?"
   *A truthful "most of it" is honest and points at C2 or C1. A reflexive "not much" with contradicting evidence points at C2 plus C8.*

4. "Is there a kind of work you're assigned right now that you don't think you're the right person for?"
   *Opens space for C5. Phrasing invites fit, not failure.*

5. "What does your week actually look like — hours, on-call load, interrupts?"
   *Opens space for C6. The IC may underreport; corroborate with on-call rotations and meeting load.*

6. "Is there anything outside work affecting your capacity right now that it would help me to know about? You don't owe me specifics."
   *Opens space for C7. If anything surfaces, pivot immediately — do not continue the diagnostic.*

7. "When you look at [specific recent artifact], what would you do differently now?"
   *Tests metacognition. Flat or deflecting answers reinforce C8.*

8. "Where do you feel your edge is right now — what do you do better than the assistant, better than most of the team?"
   *Opens space for C5 (fit) from the positive side and surfaces the IC's self-model. Inability to answer is itself a signal — often of sustained over-reliance (C2) having eroded the IC's sense of their own distinct contribution.*

9. "What would make the next two weeks noticeably better?"
   *Closes the 1:1 forward-looking, not evaluative. The IC's answer is also diagnostic: environmental asks (fewer meetings, less on-call) point at C6; tooling asks point at C3; "I don't know" with flat affect points at C6/C7.*

Note on 1:1 conduct: the manager takes no notes during the 1:1 itself (laptop closed) and writes up the session within two hours of its end. Live note-taking shifts the register of the conversation from candid to evaluative and suppresses exactly the signals the drill is trying to surface.

### Step 6 — Hypothesize and name the remedy (manager, solo, same day)
- Write a 1-paragraph hypothesis naming 1-2 causes from the taxonomy and the evidence supporting each.
- Pick the remedy from the remedy table (2.3).
- Set a checkpoint date: 4 weeks out for C1/C2/C3/C4/C8; 2 weeks for C5/C6; immediate for C7.
- Record all of the above in your private 1:1 doc, dated. Do not share this doc with the IC verbatim; share the *plan* that comes out of it.
```

### 2.3 Decision tree — from "underperformance observed" to named remedy

```text
Underperformance observed
|
+-- Is the signal a sudden step-change, not a drift?
|   |
|   +-- YES: Is there a plausible non-work explanation (illness, family, major life event)?
|   |        |
|   |        +-- YES or UNKNOWN: route-to-HRBP-and-EAP (C7). Do not continue drill.
|   |        |
|   |        +-- NO: investigate C6 (recent overload spike) first; if ruled out, re-enter drill at Step 2.
|   |
|   +-- NO (drift over weeks): continue.
|
+-- Does the IC have the skill but not exercising it (AI doing the thinking)?
|   |
|   +-- Can they defend design choices in review without invoking the assistant?
|   |   |
|   |   +-- NO, and they could 6-12 months ago: remedy = C2 over-reliance plan (pair sessions
|   |   |   with assistant-off segments; rotate them onto a task where the assistant performs poorly).
|   |   |
|   |   +-- NO, and they never could: remedy = C1 atrophy OR true skill gap (route via Exercise 02
|   |       levelling rubric). Practice reps on known fundamentals.
|   |
|   +-- YES, they can defend the design, but assistant output is poor quality:
|       |
|       +-- Prompts are short / missing codebase context: remedy = C3 context-poor prompting
|           (teach prompt structure, context packs, repo-aware patterns).
|
+-- Is throughput up but quality down (reverts, incidents, shallow reviews)?
|   |
|   +-- YES: remedy = C4 review-depth collapse (install review checklists, cap PR size,
|            re-weight their goals away from volume, pair them on a review).
|
+-- Is the failure pattern topic-specific (fine on backend, stalling on frontend)?
|   |
|   +-- YES: remedy = C5 task-fit (reshape scope, swap work, or grow via deliberate stretch
|            with support). Do not frame as performance.
|
+-- Is the IC chronically overloaded (on-call + feature + interrupt stack)?
|   |
|   +-- YES: remedy = C6 load relief first; re-diagnose after 4 weeks of sustainable load.
|            Any other diagnosis under overload conditions is unreliable.
|
+-- Does the IC systematically blame the assistant without evidence?
|   |
|   +-- YES: remedy = C8 attribution hygiene (require incident write-ups with inputs,
|            outputs, and own-decision chain; coach on reasoning, not on tool use).
|
+-- Coaching plan + 4-week checkpoint
    |
    +-- Clear improvement against named signals: continue, taper support.
    |
    +-- No improvement, plan was followed: re-enter drill at Step 1. New hypothesis.
    |
    +-- No improvement, plan was not followed: escalate to skip + HRBP; PIP conversation
        begins here, with Exercise 02 rubric as the evidentiary base.
```

### 2.4 Remedy table — right remedy, and the wrong one

```markdown
| Cause | Correct remedy | Explicitly WRONG remedy |
|---|---|---|
| C1 Skill atrophy | Structured practice-reps on fundamentals they once had; assistant-off pairing; whiteboarding. Timebox 4-6 weeks. | Sending them to a generic training course for a skill they already learned once. Treating it as a gap. |
| C2 Over-reliance | Assistant-off segments; assign a task in a domain the assistant handles poorly; require decision-defense in review. Reference mod-107 Ex04 prompts. | Telling them to "use the AI less." Non-specific, unmeasurable, and reads as a values judgement. |
| C3 Context-poor prompting | Teach prompt structure, context packs, repo-aware patterns; pair on 2-3 real tasks. | Giving them a different model/tool. Input quality is the bottleneck. |
| C4 Review-depth collapse | Review checklists; PR size caps; re-weight goals away from volume; assign them as co-reviewer on a high-stakes change with a strong reviewer. | Praising their throughput. Dashboards that only count PRs merged. |
| C5 Task-fit | Reshape scope; swap work; make growth-stretch *explicit* and supported, not covert. | Framing a fit problem as a performance problem. Doubling down on the mismatched work. |
| C6 Workload | Cut load first — on-call, meetings, scope. Re-diagnose only after 4 weeks of sustainable load. | Running the rest of the drill under overload conditions and trusting the signal. |
| C7 Health / life | Immediate HRBP + EAP routing; adjust expectations; stop the drill. | Continuing to collect "performance evidence" during a known life event. |
| C8 Attribution hygiene | Require structured failure write-ups (input, output, own decision chain); coach on reasoning. | Lecturing them that "the model isn't always wrong." Won't land; the IC needs a process, not a scolding. |
```

### 2.5 Why this and not that

- **Why eight causes, not three.** The traditional "skill / fit / effort" trichotomy collapses C1, C2, C3, C4, and C8 into a single bucket called "skill," which is why the pre-AI performance playbook now routinely picks the wrong remedy. Each of those five has a distinct remedy that works for it and fails for the others.
- **Why no PIP step inside the drill.** A PIP is a formal HR instrument with legal weight. The drill is a *pre-formal* diagnostic. Mixing them means the drill's prompts start to look like evidence-gathering for an adverse action, which contaminates the signal and is also wrong practice. The drill may *terminate* in "begin PIP conversation with HRBP," but PIP is not a node *inside* the drill.
- **Why 1:1 prompts are open, not leading.** Asking "are you over-relying on the assistant?" almost always returns "no, I don't think so" regardless of the truth; it also damages trust. Asking "walk me through the last PR you felt best about" surfaces the same signal without putting the IC in a defensive posture.
- **Why C8 is separate from C2 and C3.** Over-reliance (C2) is about *behavior*: deferring judgement. Context-poor prompting (C3) is about *inputs*: what the IC types into the box. Attribution hygiene (C8) is about *explanation*: how the IC narrates failure to themselves and others. All three can be present independently; the remedies are different; coaching the wrong one wastes 4-8 weeks.
- **Why the manager drafts the hypothesis before the 1:1.** Walking into the 1:1 without a hypothesis produces a vague "how's it going" conversation that never names the signal. Walking in with a *shared with the IC* hypothesis anchors the IC to the manager's reading. The right posture is: hypothesis in the manager's head going in, actively open to being disconfirmed, written up only after.
- **Why checkpoints are 4 weeks, not 2.** For C1/C2/C3/C4/C8, behavior change below 4 weeks is unobservable through PR noise. 2-week checkpoints look rigorous but produce false negatives and false positives in roughly equal measure. C5/C6/C7 have faster checkpoints because the remedy is environmental (load relief, scope change), not behavioral.
- **Why "cannot / chose not to / did not notice" is the top-level cut.** These three verbs map to three different managerial remedies: teach, re-align incentives, build attention systems. Collapsing them (the pre-AI default) is why coaching plans sound right in the room and produce no change in the artifact. The taxonomy encodes this cut as C1/C3 ("cannot or does not yet"), C2/C5 ("chose not to or chose otherwise"), and C4/C8 ("did not notice or did not reflect").
- **Why the drill is private documentation, not shared documentation.** The manager's hypothesis is tentative and may be wrong. Sharing it verbatim ("I think you're over-reliant on the assistant") anchors both parties to the hypothesis and makes disconfirmation socially expensive. What gets shared with the IC is the *plan* that comes out of the hypothesis, framed in terms of concrete behaviors and checkpoints, not the labelled cause.

## 3. Validation steps

1. **Taxonomy discriminability check.** Draft 10 short underperformance vignettes. Assign each to a cause from the taxonomy. If any vignette plausibly fits three or more causes, the taxonomy or its symptoms column needs tightening.
2. **Drill-does-not-lead check.** Have a peer manager read the Step 5 prompts cold. Ask: "Can you tell which cause I think it is?" If yes, the prompts are leading and need rewriting.
3. **Decision-tree completeness check.** For each terminal remedy, confirm there is a path from "underperformance observed" to that leaf. Confirm `route-to-HRBP` and `route-to-EAP` are reachable from the C7 branch, not buried in text.
4. **PIP-isolation check.** Grep the artifact for "PIP." Confirm no node *inside* the decision tree is a PIP step; PIP only appears as a downstream escalation.
5. **Levelling-rubric linkage check.** Confirm Step 1 references Exercise 02's named dimensions. "Just underperforming" must be unusable input to the drill.
6. **Attribution-scan actionability check.** Confirm Step 4 names a concrete corpus (PR descriptions, incident notes, standup updates) and a concrete threshold. "Watch for attribution patterns" without a corpus is unenforceable.
7. **Documentation-discipline check.** Confirm the drill tells the manager *where* notes go (private 1:1 doc), *when* (same day), and *what not to share verbatim* with the IC. If missing, the artifact fails to protect either party.
8. **Confidentiality check.** Confirm no step instructs the manager to pull private assistant transcripts, personal chat logs, or health information without consent. Step 2 explicitly scopes to voluntarily shared signals.
9. **"Cannot / chose not to / did not notice" trace.** For a given cause in the taxonomy, confirm the drill can distinguish it from the other two verbs. If the drill can only distinguish "cannot" from "the other two lumped together," the artifact has regressed to the pre-AI three-bucket model and needs re-work.

## 4. Rubric

| # | Dimension | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Taxonomy names over-reliance and review-depth collapse as distinct causes | Absent | Mentioned in passing | Named with symptoms | Named, with symptoms *and* "mistaken-for" column |
| 2 | Drill is sequenced, not a single conversation | Single-step | Two-step | Ordered but under-specified | Ordered, each step has owner, duration, output |
| 3 | 1:1 prompts are open, not leading | Leading throughout | Mixed | Mostly open | Open, disconfirmation-friendly, cross-referenced to mod-107 Ex04 |
| 4 | Decision tree reaches HRBP/EAP as a valid terminus | Absent | Mentioned | Reachable node | Reachable node *with* trigger conditions named |
| 5 | Remedy table names the *wrong* remedy per cause | Right remedy only | Wrong remedy for some | Wrong remedy for most | Wrong remedy for every cause, rationale implied |
| 6 | PIP is positioned *after* the drill, not inside it | Treated as a drill step | Ambiguous | Positioned downstream | Positioned downstream *and* conditioned on Exercise 02 rubric |
| 7 | Attribution hygiene is a named cause with its own remedy | Absent | Folded into "skill" | Called out | Called out *with* scan procedure and threshold |
| 8 | Confidentiality and documentation discipline are explicit | Absent | One mention | Explicit location and timing | Explicit location, timing, consent boundaries, and what to withhold |

**Pass bar: 18 / 24,** with no dimension scoring 0 and dimensions 1, 4, and 7 each scoring at least 2. A submission can be dense and well-written and still fail if it omits over-reliance as a first-class cause (dim 1), the HRBP/EAP off-ramp (dim 4), or attribution hygiene (dim 7) — those three omissions are the ones that most predict a manager picking the wrong remedy in practice.

## 5. Common mistakes

1. **Collapsing over-reliance into "skill gap."** They look similar on the surface; the remedies (practice-reps vs. assistant-off segments + decision defense) are different and interchanging them wastes a 4-week cycle.
2. **Celebrating C4 as a productivity win.** Rising PR throughput with rising revert rate is not a win. Dashboards that only count merged PRs will reinforce this exact failure.
3. **Running the drill under C6 (overload) conditions.** Any other cause's signal is unreliable while the IC is chronically overloaded. Cut load first, re-diagnose.
4. **Making the 1:1 a judgment session.** The moment the IC perceives the conversation as an evaluation, the signal collapses. Prompts must be open; the manager's hypothesis stays in the manager's head.
5. **Letting attribution-hygiene (C8) slide because it's uncomfortable to name.** "The model was wrong" without logs is a reasoning failure and must be surfaced; otherwise no coaching plan lands.
6. **Jumping to a PIP from a single diagnostic 1:1.** PIP is a formal instrument; it requires HRBP, Exercise 02 rubric evidence, and a prior documented coaching cycle. The drill is not a PIP intake.
7. **Pulling private assistant transcripts without consent.** This is a confidentiality violation and, in many jurisdictions, a legal exposure. Step 2 scopes artifacts to things voluntarily shared or that the manager already has review access to. <!-- tbd-confirm: specific jurisdictional treatment of employer access to developer AI tool transcripts varies by region and employment contract. -->
8. **Confusing C5 (task-fit) with underperformance.** Putting someone on a coaching plan for a mismatched assignment is both wrong and corrosive. Reshape the work before reshaping the person.
9. **Checkpointing too fast.** Two-week behavioral checkpoints produce noise, not signal. 4 weeks for behavioral causes, 2 for environmental, immediate for health.
10. **Skipping the write-up.** Un-documented drills look identical to arbitrary management if the case later escalates. The dated 1:1 note is the manager's and the IC's protection. Record the hypothesis, the evidence for and against, the remedy, and the checkpoint.
11. **Letting one strong signal drown out the others.** A vivid piece of evidence ("this one PR was terrible") can lock the manager onto a single cause. Step 3 explicitly requires writing evidence *for and against* each row to counter this bias.
12. **Treating the drill as one-shot.** The drill is a loop. A failed coaching plan re-enters at Step 1 with a new hypothesis. Managers who treat the first diagnosis as final are the ones who end up with a 6-month-old PIP and no change in the IC's work.

## 6. References

- **NIST AI Risk Management Framework** — https://www.nist.gov/itl/ai-risk-management-framework. Relevance: NIST AI RMF's **Measure** and **Manage** functions are the right mental model for a drill like this one. The drill is an organization-level manage/measure instrument — you cannot manage underperformance in an AI-in-the-loop team without a structured way to measure *which* AI-interaction failure mode is active. The taxonomy columns ("symptoms / mistaken-for") mirror how NIST AI RMF asks organizations to characterize and distinguish AI risks before selecting a response.
- **OWASP Machine Learning Security Top 10** — https://owasp.org/www-project-machine-learning-security-top-10/. Relevance: although OWASP ML Top 10 is a security taxonomy, two of its concerns are directly pertinent here — overreliance on model output and inadequate verification of model-generated artifacts. These are the external, security-framed names for what this drill calls C2 (over-reliance) and C4 (review-depth collapse). Using the OWASP vocabulary in coaching conversations gives the IC a non-personal frame for the issue.
- **MITRE ATLAS** — https://atlas.mitre.org/. Relevance: MITRE ATLAS catalogues adversarial threats against ML systems, but its diagnostic style — named techniques with observable signals — is a useful structural model for the taxonomy in 2.1. The "named cause + symptoms + commonly-mistaken-for" shape in this exercise borrows ATLAS's convention that a threat/behavior only becomes actionable when it is distinguishable from adjacent threats/behaviors by named observables.

Cross-references within this curriculum:

- **mod-108 Exercise 02 (levelling criteria refresh)** supplies the dimensions ("scope," "ambiguity handling," "review craft," etc.) that Step 1 of the drill points at. "Just underperforming" is not a usable drill input; "below bar on *review craft* over 6 weeks" is.
- **mod-107 Exercise 04 (burnout and over-reliance 1:1 prompts)** supplies the prompt library this drill reuses for the C2 (over-reliance) and C6/C7 (overload / health) branches of Step 5. Do not re-derive those prompts; cite that exercise and reuse.

A final note on scope: this drill does not attempt to be a performance-management methodology. It is the step that goes *before* a performance-management methodology — the diagnostic that decides which methodology (coaching plan, scope reshape, HRBP routing, EAP routing, or formal PIP) is the right one. Managers who treat the drill as a substitute for formal performance processes misuse it; managers who skip the drill and go straight to a formal process pick the wrong process a predictable fraction of the time, because the pre-AI three-bucket diagnosis no longer discriminates in an AI-in-the-loop team. The drill is the bridge between "something is off" and "here is the named response." <!-- tbd-confirm: empirical base rates for mis-diagnosis across the taxonomy are not published; this drill is a structural proposal, not a validated instrument. -->
