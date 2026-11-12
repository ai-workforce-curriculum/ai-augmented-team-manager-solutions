# Exercise 04 — Burnout and Over-Reliance 1:1 Prompts

> Module: `mod-107-skill-growth-anti-atrophy-and-career-development`
> Exercise type: **design** (worked prompt set + facilitation rationale + grading rubric)

## 1. Solution overview

The deliverable is a short, **re-usable set of 1:1 prompts** the manager asks
ICs to open conversations about two specific risks under AI augmentation —
*burnout* (the IC is grinding to keep up with the assistant-amplified pace,
or is lonely because collaboration moved into the tool) and *over-reliance*
(the IC's judgement is quietly being outsourced to the assistant, and they
have not noticed). The set is accompanied by a facilitation note on *how* to
use it so the prompts do not degrade into a questionnaire.

A good prompt set is:

- **Specific enough to produce a concrete answer.** Vague prompts
  ("how are you feeling about AI?") produce vague answers. Each prompt
  names a behaviour, an artifact, or a timeframe.
- **Diagnostic, not accusatory.** The prompts are designed to help the IC
  *see* a signal the manager also sees. They are not designed to
  interrogate. If the IC reads them as a performance-management setup,
  they will perform; the prompts will produce no information.
- **Uses the IC's own artifacts as the anchor.** Ground the prompt in a
  specific recent PR, incident, or thread so the conversation cannot
  slide into generalities.
- **Paired with a facilitation note.** The best prompt in the world, read
  off a laptop, creates stilted 1:1s. The manager needs cover-text on
  *when* in the 1:1 this topic belongs, *how* to introduce it, and *what
  to do with the answer*.
- **Actionable at the end.** Every topic ends with either "I noted this
  and will watch" or "we agreed to one specific next step."

Design decisions this solution encodes:

1. **Burnout and over-reliance are treated as related but distinct.**
   They can coexist, they can mask each other, and they have different
   remedies. A single prompt cannot carry both.
2. **Signals come before prompts.** The manager comes to the 1:1 with
   their observations already in hand (from Exercise 01). Prompts are
   not a fishing expedition.
3. **The IC is a peer in the diagnosis.** Prompts share what the manager
   has noticed and ask the IC for their read. Prompts that do not share
   anything from the manager's side read as interrogation.
4. **Consent and confidentiality are named explicitly.** Burnout is a
   health-adjacent topic. The prompts say what will and will not leave
   the room.
5. **The prompts themselves are not a therapy session.** They route
   toward the right venue — EAP, mental-health resources — when a
   signal is beyond the manager's scope. The manager is not a
   counsellor; the prompts do not pretend otherwise.

## 2. Implementation — the prompt set

### 2.1 Facilitation note (the manager reads this before the 1:1)

```markdown
# Facilitation note: burnout & over-reliance prompts

**When to use:** Any of the following triggers.
- Exercise-01 research surfaced a concentrated over-reliance or
  burnout signal on a specific IC.
- The IC's PR cadence is up but their review-depth or write-up output
  is down.
- The IC has asked for a 1:1 topic you can tell is adjacent (tooling
  frustration, feeling slow, wanting a break).
- Routine coverage: once a quarter per direct, even without a signal.

**When not to use:** First 1:1 with a new direct (too intimate too
early); 1:1 after a formal performance conversation (looks like pretext);
a 1:1 the IC has booked for a specific topic (don't hijack it).

**How to open the topic.** Share one of your own observations first;
do not open with a question. Example:

> "I've been reading my last-60-days notes and I noticed we're shipping
> more small PRs than ever, but I haven't seen a design doc from you
> this quarter. Can we spend 10 minutes on how your workflow has
> shifted? I want to make sure we are not trading away stuff that
> matters to your career."

Then ask **one** of the prompts below — not all of them. These are a
palette, not a script.

**Confidentiality.** Say it out loud: "What you say here stays here
unless we agree together to carry it forward. If something comes up
that belongs with HR or EAP, I'll tell you before I do anything with
it." If you cannot keep that commitment for a given topic, name the
exception at the start.

**Signal, not answer.** Treat the 1:1 answer as one more data point,
not a diagnosis. Over-reliance and burnout are patterns across
observations; don't act on a single conversation unless the IC is
explicitly asking you to.

**Route, don't carry.** For signals outside your scope, name the
resource ("our EAP has after-hours intake," "your HRBP is the right
place for workload normalisation") and offer to introduce. Do not
pretend to be the solution to a problem you are not qualified to
solve.
```

### 2.2 Over-reliance prompts

> Use one of these when the signal is about judgement being outsourced
> to the assistant, not when the signal is about workload.

```markdown
# Over-reliance prompts (choose one per 1:1)

## 1. The "last diff you wouldn't have written" prompt
"Can you walk me through the last PR that you merged where the code
looks different from what you would have written on your own? What's
the gap — is it 'the assistant found a better idiom' or 'I accepted
something I don't fully understand'?"

- Follow-up if gap is understanding: "What would help — more review
  time, a carve-out block, pairing?"
- Follow-up if gap is idiom: "Good. How did you verify it was better?"

## 2. The "explain the diff cold" prompt
"Pick a PR of yours from the last two weeks. If I asked you to walk me
through it on a whiteboard right now without opening the assistant,
how far would you get? No test — I just want to know where the
confidence gradient is."

- Follow-up for shallow confidence: "Which part would you want time
  to re-read? Let's budget that time for next week."
- Follow-up for solid confidence: "Great — what in your workflow is
  letting you stay confident?"

## 3. The "last time you disagreed" prompt
"When was the last time the assistant gave you an answer you decided
not to take? What was the disagreement about?"

- A long silence here is itself the signal. The IC who cannot name a
  recent disagreement is an IC whose judgement is being consistently
  deferred-to.
- Follow-up: "What makes you take an answer vs. push back?"

## 4. The "escalation reflex" prompt
"I remember <specific escalation thread>. You said the assistant had
given a wrong answer and asked someone else to resolve it. In
hindsight, what would have given you confidence to resolve it
yourself?"

- This one lands hard; use it only when the specific thread is
  already shared context. It is a precise prompt for a precise
  observation.

## 5. The "what did you learn" prompt
"Over the last quarter, what is one concrete thing you now understand
that you didn't at the start? Something you could teach the IC2 next
to you."

- A thin answer here across multiple quarters is a ladder-level
  signal, not only an over-reliance signal.
```

### 2.3 Burnout prompts

> Use one of these when the signal is about workload, loneliness, or
> loss of agency. These are the harder prompts to ask well.

```markdown
# Burnout prompts (choose one per 1:1)

## 1. The "end-of-day" prompt
"At the end of a typical day right now, do you feel like you did the
work you meant to do, or like you reacted to the assistant and the
sprint board for 8 hours and never got to the thing you cared about?
No judgement — I just want to know what the rhythm is."

- Follow-up for the 'reactive' answer: "What would one unreactive
  day a week look like — what would you do with it?"
- Follow-up for the 'did-the-work' answer: "What's protecting that
  for you? I want to know what's load-bearing."

## 2. The "collaboration" prompt
"Who have you actually talked to this week? I mean out-loud, in a
meeting or a pair or a walk — not Slack. I ask because the people I
know who adopted the assistant hardest have also become the loneliest
on the team."

- Follow-up for sparse answer: "Want me to put a weekly pair on the
  calendar? Who would you want to pair with?"
- This prompt names a specific failure mode of assistant-heavy work
  — isolation — that is often missed by workload-only framing.

## 3. The "would you trade" prompt
"If you could trade an hour of assistant-amplified cycle time for an
hour spent reading someone else's code slowly, or pairing, or writing
a design doc — would you? And if yes, what's stopping you?"

- Useful for isolating 'I'm burning out on the *pace*' from 'I'm
  burning out on the *content*.'
- Follow-up: "What permission do you need from me to make that trade
  on your own?"

## 4. The "weekend" prompt
"How many weekends in the last two months did you check in on
assistant runs or merges? I'm not auditing — I'm just checking that
the workload hasn't quietly expanded into evenings because the tool
makes it easier to."

- This is the "the assistant removes the friction that used to
  protect weekends" signal. Specific to post-adoption burnout.

## 5. The "last time you were proud" prompt
"When was the last time you looked at something you shipped and
felt good about it — not 'that landed', but 'that was mine, I can
defend it, it's a good piece of work'?"

- Gentle but surgical. A long silence here is a signal across several
  axes (over-reliance, lack of growth, meaning loss) at once.
- Follow-up: "What would a piece of work like that look like next
  quarter?"

## 6. The "if we did nothing" prompt
"If I did nothing — no carve-out, no re-prioritisation, no workload
conversation — and next quarter looked exactly like this one, how
would you be?"

- Converts an abstract wellness question into a concrete forecast.
- Follow-up for any answer worse than "fine": "Ok — what's the
  one change I could make that would shift that forecast most?"
```

### 2.4 After the 1:1

```markdown
# After the 1:1

**Capture.** Before the next meeting, write a 2-sentence note: what
you heard, what you agreed to do. Do not write verbatim quotes without
consent. The note lives in the manager's own 1:1 doc, not a shared
venue.

**Act on exactly one thing.** The temptation is to walk out with a
list. Pick one. Carve-out block, pair, redistribution of tickets,
introduction to HRBP — one. Tell the IC which one. Ten half-done
responses look like performance management; one completed response
looks like a manager.

**Set the next check-in.** Burnout and over-reliance are patterns, not
events. Agree with the IC when you will return to this conversation —
usually 2–4 weeks out. Put it on both calendars.

**Trigger escalation when it is obvious.** If the IC describes a
situation that your team's EAP, mental-health policy, or HR are the
right resource for, say so, in the meeting, and follow the company's
documented escalation path. Do not sit on these.
```

### 2.5 Why these prompts and not others

- **"Walk me through the last diff you wouldn't have written"** replaces
  the generic "do you feel over-reliant on AI?" The generic version
  invites a defensive answer. The specific version invites the IC to
  reason about a real artifact; the manager learns something either way.
- **"Explain the diff cold"** is the operational analogue of the
  Exercise 03 ladder criterion. It converts a ladder expectation into a
  1:1 question that is cheap to answer.
- **The escalation-reflex prompt is dangerous and precise.** It only
  works when the escalation is already shared context. If the IC does
  not know you watched it, the prompt feels like ambush. Named in the
  facilitation note so inexperienced managers do not misuse it.
- **"Collaboration" prompt catches isolation.** Burnout framings often
  focus on hours. Under heavy assistant use, isolation is a specific
  and different signal: the IC is no longer *talking to humans* about
  their work. The prompt addresses it directly.
- **"Weekend" prompt catches assistant-enabled workload creep.** The
  assistant removes the friction that used to protect off-hours. The
  prompt is specific to this post-adoption pattern.
- **"Would you trade" and "if we did nothing"** are the two prompts that
  convert wellness talk into something the manager can act on. The
  former isolates pace vs. content; the latter forces a forecast and a
  single lever.
- **"When you were last proud"** is deliberately the gentlest, because
  it is the one most likely to land in silence; silence is what the
  manager came for.

## 3. Validation steps

To validate the prompt set before using it:

1. **Say each prompt out loud to yourself.** If it feels stilted,
   revise. The prompts must sound like something you would actually
   say.
2. **Localise to your team's venues and artifacts.** "PR" might be
   "change request"; "Cursor" might be another tool. Replace every
   generic word.
3. **Write your own facilitation note.** The prompts here are a
   palette; the opener you say in your own voice is yours. Script the
   opener in your own words before the 1:1.
4. **Role-play with a peer manager.** Ask a peer to play the IC and
   push back on one prompt. Notice where your instinct is to defend
   the prompt vs. respond to the answer. Your defensive instinct is
   the exact one you need to park.
5. **Do not use more than one prompt per 1:1.** Walk through the
   whole conversation in your head on the single prompt. If the whole
   conversation fits in 10 minutes, you underscoped; if it spills past
   25, you overscoped.
6. **Pre-write the escalation path.** Before using any prompt, know
   which signals route you out of the room: to HR, to EAP, to the HRBP.
   If you have to look it up in the moment, it is already too late.
7. **Test for coerciveness.** Ask: if the IC answered "I'm fine" to
   every prompt, would I still believe it? If the answer is "no
   because I already decided," do not use the prompt — your mind is
   made up and the 1:1 is theatre.

## 4. Rubric

Use this to grade a submitted prompt set + facilitation note.
Each criterion scores 0–3. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Specificity of prompts** | Vague ("how do you feel") | Mixed | Most prompts name a behaviour / artifact | Every prompt names a behaviour, artifact, or timeframe |
| R2 | **Separation of burnout and over-reliance** | Conflated | Mentioned separately | Distinct prompt sets | Distinct prompt sets + note on how they interact |
| R3 | **Manager-shares-first facilitation** | Pure interrogation | One-sided | Facilitation note names "share first" | Named + example opener + guidance on when NOT to open |
| R4 | **Consent and confidentiality** | Absent | Mentioned | Stated out loud | Stated out loud + explicit exceptions named |
| R5 | **Follow-up / action** | None | "We talked" | One next step | One next step, named owner, named review date |
| R6 | **Routing / escalation** | None | Mentioned | EAP / HRBP named | EAP / HRBP named + manager's path pre-written + the manager tells the IC before escalating |
| R7 | **Non-coerciveness** | Prompts corner the IC | Mixed | Prompts accept "I'm fine" as a valid answer | Prompts accept "I'm fine" + name the pattern (not event) nature so a single answer does not trigger over-reaction |
| R8 | **Fit to the signal** | Prompt set used identically regardless | Some prompt selection guidance | Selection criteria named | Selection criteria named + explicit triggers from Exercise 01 research |

## 5. Common mistakes

- **Reading the prompts off a laptop verbatim.** The 1:1 turns into a
  survey. The IC performs. No information is produced.
- **Asking every prompt.** The prompt set is a palette. Using all of
  them is a management-by-checklist tell; the IC will close up.
- **Opening with a prompt instead of an observation.** If the manager
  has not said what they have noticed, the IC is being asked to
  self-diagnose in front of their boss. That is a ritual, not a
  conversation.
- **No confidentiality promise, or an unkept one.** The prompts carry
  more weight than a normal 1:1; confidentiality has to be stated and
  respected. A single breach destroys the practice for the team.
- **Using the escalation-reflex prompt without shared context.** The
  manager mentions the thread as if it were an accusation; the IC
  reads it as one. Do not deploy the surgical prompts without having
  earned them.
- **Carrying a diagnosis the manager is not qualified to carry.** The
  prompts touch on wellness; the manager is not a counsellor. Route to
  the right resource, named in the facilitation note.
- **Writing it up verbatim in a shared doc.** The IC spoke in
  confidence. The note in the manager's own 1:1 doc is the record;
  the shared doc is not.
- **Acting on a single conversation.** Patterns span multiple 1:1s and
  multiple artifacts. One "I'm fine" or one worrying answer is a data
  point, not a basis for policy action.
- **Timing it right after a performance conversation.** The prompts
  will be read as pretext. Time them routinely or in response to
  Exercise 01 signals, not as pre-PIP footwork.
- **Treating the 1:1 as a replacement for the policy response.** The
  prompts surface the signal. The *response* (carve-out, workload
  rebalance, mentoring pair) is engineered elsewhere — in Exercise 02
  and in the IC development plan (Exercise 05). The 1:1 produces the
  input; it is not the response.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function's accountability expectations (named humans,
  stated practices, documented residual risk) are the frame for the
  explicit consent, routing, and follow-up discipline in the
  facilitation note. The *Manage* function's framing of "risk
  response actions" is the structural reason the 1:1 is designed to
  produce *one* named next step, not a general "we talked" outcome.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  over-reliance on assistant output is one way the security posture
  of the team weakens; the over-reliance prompt set is the
  people-facing complement to the review-checklist controls Mod-103
  lays down.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy; cited here because the manager's observations in the
  facilitation note include a category ("escalation reflex,"
  "accepted-without-reading") that corresponds to over-reliance
  exposure categories in the broader taxonomy. The 1:1 feeds these
  observations back into the team's risk register.

<!-- follow-up: once the prompt set has been in use for a quarter,
capture which prompts produced information and which produced
polite silences. The palette should shrink, not grow, over time. -->
