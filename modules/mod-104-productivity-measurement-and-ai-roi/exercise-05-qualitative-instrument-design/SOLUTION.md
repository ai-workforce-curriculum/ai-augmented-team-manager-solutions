# Exercise 05 — Qualitative Instrument Design

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **design** (worked instruments + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **qualitative instrument kit** for measuring developer experience (DevEx) and AI-tool-adoption around a team's rollout: a pulse survey, a quarterly DevEx survey, and a semi-structured interview guide, plus the policy that governs when and how they are used.

A good kit is:

- **Tightly scoped to decisions.** Every question exists to inform a specific decision — never "nice to know." Questions that do not point at a decision are cut.
- **Instrumented against three failure modes.** Response fatigue (too many questions, too often); demand characteristics (questions that tell people what to say); and silence bias (people who disagree stop answering). The instrument design deliberately mitigates each.
- **Honest about what qualitative data can and cannot tell you.** A pulse score is a signal, not a verdict. Interviews triangulate. Nothing in the kit pretends a 4.2/5 average means a team is thriving.
- **Short enough that you actually run it.** An annual survey that runs is worth more than a monthly survey that drifts into neglect. The pulse is three questions; the quarterly is a page; the interview is 30 minutes.

Design decisions this solution encodes:

1. **Three instruments, not one.** A 3-question pulse (high-frequency, low-cost), a 10–12-question quarterly (the DevEx leg of DX Core 4), and a 30-minute semi-structured interview (depth on anomalies and threshold events). Each has a distinct purpose; collapsing to one loses coverage.
2. **The pulse is the gatekeeper.** It is the only instrument that runs continuously; everything else fires conditionally when the pulse moves or when the quarter closes.
3. **Anonymity by default; identifiability by opt-in.** Default anonymous to protect candor; interviews are opt-in with consent, so attribution is explicit.
4. **The survey is the same every quarter.** Comparability over time is more valuable than optimising wording. Changes are logged and dated so year-over-year analysis knows when the instrument changed.
5. **Qualitative data is read by a small number of named people on a set cadence.** Data that nobody is responsible for reading is not data; it is disposal.
6. **AI-specific items are *separate* from the general DevEx items.** AI-specific friction is a real category but putting it in every question would contaminate the DevEx baseline that pre-dates the rollout.

## 2. Implementation — the instruments

### 2.1 The 3-question pulse (weekly, 30 seconds)

Runs every Friday via Slack DM or email link. Anonymous by default; aggregated to the team weekly.

```markdown
## Weekly DevEx pulse

1. **This week, how productive did you feel on your main work?**
   (1 = far less than usual, 5 = far more than usual)

2. **What slowed you down the most this week?** (short free text; optional)

3. **If "AI assistant" was involved in your work this week, was it net
   helpful, net harmful, or neutral?** (helpful / neutral / harmful /
   did not use)
```

**Why these three questions:**

- **Q1** gives you a single comparable number per engineer per week; enough signal to see shifts week-over-week without a heavyweight survey.
- **Q2** is the only *free text* item in the pulse. It catches the modal friction before the weekly review, in the engineer's own words.
- **Q3** is the simplest possible AI-net-effect measure. Four options (not a scale), because fine-grained scales on perception-of-AI are noisy and the four buckets are what a team manager actually needs.

**What the pulse is NOT for:**

- Setting any individual's performance anything.
- Making a quarterly decision on its own (that is the quarterly survey + interviews).
- Comparing teams (the N is too small; comparability breaks).

### 2.2 The quarterly DevEx survey (10–12 questions, ~5 minutes)

Runs the first Monday of the quarter; open for one week. Anonymous. Required reading in the pre-read (see Exercise 04).

```markdown
## Quarterly DevEx Survey — <team>, <quarter>

> Anonymous. Aggregated results shared back with the team within 2 weeks.

### A. Flow & friction
1. "I have the tools I need to be productive." (1–5 Likert)
2. "I can go a full day without context-switching more than I'd like."
   (1–5)
3. "When I need a review, I can get one within a day." (1–5)
4. "My deep-work time is protected." (1–5)

### B. Confidence & quality
5. "I have confidence in the tests and CI for the code I ship." (1–5)
6. "When I make a change, I usually know if it broke something within
   the same day." (1–5)

### C. Satisfaction & well-being
7. "I would recommend this team to a friend as a place to work." (1–5)
8. "In the last quarter, I felt burnt out." (never / rarely / sometimes /
   often / always)

### D. AI assistant (new section, added 2026-02)
9. "In the past quarter, the AI assistant was net helpful to my work."
   (strongly disagree – strongly agree, 5-point)
10. "When the AI assistant was not helpful, the biggest reason was…"
    (choose one: wrong answer / outdated knowledge / too slow / broke
    my flow / had to re-explain context / privacy or safety concern /
    other — free text)
11. "The AI assistant has changed the kind of work I do." (yes, more
    higher-value work / yes, more lower-value work / no change / unsure)

### E. Open text
12. "If you could change one thing about how this team works, what
    would it be?" (free text; optional)
```

**Why 12 and not 30:** The marginal question past ~12 does not improve the signal enough to justify the fatigue cost — response rates on surveys longer than a page measurably fall. Keep the instrument to one page.

**Why the AI section is a dedicated section, not sprinkled in:** Sprinkling AI items throughout contaminates the DevEx baseline (the team needs to know whether DevEx moved because of AI *or because of other things*). A clearly-scoped "AI assistant" section lets you read the two sides separately.

**Why Q11 asks about *the kind of work*, not just productivity:** Shift in work composition is a real and common finding — engineers do less of some tasks (boilerplate, tests) and more of others (review, design, debugging). The productivity dimension alone misses this.

### 2.3 Semi-structured interview guide (30 minutes, 2–4 per quarter)

Run 2–4 interviews per quarter — targeted, not random. Trigger criteria:

- One pulse rating in the bottom quartile two weeks in a row, *or*
- One quarterly survey Q7 ("recommend this team") in the bottom quartile, *or*
- A notable outlier on Q11 (significant change in the kind of work), *or*
- A rollout milestone (new tool added, scope expanded) — one interview the quarter after.

Participation is **opt-in**, with written consent that notes are team-manager visible and anonymised before any wider share.

```markdown
## Semi-structured interview — DevEx & AI assistant

> 30 minutes. Audio-recorded with consent; transcribed; raw recording
> deleted within 7 days; quotes anonymised before any wider share.
>
> Interviewer: the team manager, unless the interviewee asks for a
> neutral interviewer (in which case: the manager's peer, with the same
> privacy guarantees).

### 1. Warm-up (3 min)
- What are you working on this week? What does a good week look like?

### 2. Flow and friction (8 min)
- Walk me through the last time you lost half a day to something that
  wasn't the work itself. What was it? What would have helped?
- What's the single biggest friction that nobody's talking about?

### 3. AI assistant (8 min)
- Tell me about the last time the AI assistant was clearly helpful.
  What did it do? What made it work?
- Tell me about the last time it was clearly unhelpful or harmful.
  What did it do? What did you have to do to recover?
- Has your use of the assistant changed what you're doing day-to-day?
  Are you spending time on different things than six months ago?
- Are there kinds of work you'd like to use it for but don't? Why not?
  (Watch for: trust gaps, safety concerns, policy ambiguity, latency.)

### 4. Growth and ownership (5 min)
- Six months from now, what do you want to be doing on this team that
  you're not doing today? Is the current rollout making that more or
  less likely?
  (Links to Module 107 — skill growth and anti-atrophy.)

### 5. Close (3 min)
- One concrete thing I, as the team manager, could do next month that
  would most help. (Capture verbatim.)
- Anything we didn't talk about that I should know?
```

**Why targeted and not random:** Random interviews waste the team's time and dilute the signal. Trigger-based interviews chase the anomalies that the quantitative surface flagged.

**Why 30 minutes, not 60:** A 30-minute interview respects the engineer's time and keeps the manager's follow-through tractable. If a specific thread genuinely needs depth, schedule a second 30-minute follow-up.

**Why the manager interviews by default:** The manager is the one accountable for acting on the results. Delegating to a neutral researcher adds a handoff; keep the loop tight. But offer the neutral-interviewer option — some conversations the manager is not the right counterpart for.

### 2.4 The governance policy (one-page)

```markdown
# Qualitative Instrument Policy — <team>

## Cadence
- Weekly: 3-question pulse. 30 seconds. Anonymous.
- Quarterly: 10–12-question DevEx survey. ~5 minutes. Anonymous.
- Quarterly: 2–4 semi-structured interviews, trigger-driven. Opt-in.

## Who runs it
- Pulse + quarterly: the team manager. Aggregates and shares back within
  2 weeks of close.
- Interviews: the team manager by default; neutral interviewer on
  request.

## Confidentiality
- All instruments are anonymous by default.
- Interview notes are team-manager-visible; shared more widely only after
  anonymisation and after the interviewee has reviewed the paraphrase.
- Raw recordings are deleted within 7 days.
- Nothing from any instrument is used in performance review input.
  Full stop.

## What we report back
- Within 2 weeks of each pulse / survey close, the team sees aggregated
  results and the manager's top 3 interpretations. The team can push back
  on the interpretations in writing before they become decisions.
- The quarterly survey's AI-assistant section becomes the DevEx leg of
  the ROI pre-read (see Exercise 04).

## What we act on
- Pulse: used as a sentinel. Two consecutive weeks of a bottom-quartile
  item trigger (a) an interview, (b) a named follow-up action owned by
  the manager within 2 weeks.
- Quarterly: becomes the primary DevEx data point in the exec pre-read;
  top interpretations are owned, dated, and re-checked next quarter.
- Interviews: themes are transcribed into a running `devex_themes.md`;
  recurring themes (3+ interviews mention the same thing) escalate to
  a team-level change.

## Changes to the instrument
- The quarterly DevEx survey wording is frozen for the comparability it
  gives. Changes must be logged in `instrument_changelog.md` with a
  dated note, and the first post-change quarter is flagged in dashboards
  so year-over-year reading accounts for the discontinuity.
```

### 2.5 Analysis tips (how to actually read the data)

- **Report distributions, not just averages.** A team with a 4.2 average and a bottom quartile at 2.0 is a team with a hidden problem. Show the histogram.
- **Correlate pulse-Q2 free text with quantitative shifts.** When Q1 drops in a given week, read every Q2 for that week. The explanation is almost always in there.
- **Watch for silence bias.** Response rate is a signal. A sudden drop from 85% to 50% is not random; it is data.
- **Code interview notes with a fixed scheme.** A handful of codes ("tool trust gap," "review latency," "ambient context cost," "boilerplate reduction," "verification overhead," etc.) applied consistently across interviews is more useful than longer free-form notes.
- **Report back verbatim quotes with consent.** Anonymised but verbatim quotes are more persuasive than summarised themes, and they anchor the team in their own words.

## 3. Validation steps

1. **Pre-register the instrument.** Commit the exact survey wording and cadence to a repo before the first run. Any change thereafter is a logged amendment, not a silent edit.
2. **Pilot with 3 engineers.** Have them take the quarterly survey in front of you. Where they pause, flinch, or ask for clarification, the question is broken. Fix.
3. **Benchmark response rate.** The first pulse's response rate is your baseline. Target 70%+ on the quarterly; below 50% makes the data dangerous to interpret.
4. **Blind-interpret test.** Hand the quarterly results, stripped of your interpretation, to one peer manager. Ask them to write their top 3 reads. If their reads differ significantly from yours, you are over-interpreting.
5. **Re-contact test.** After one quarterly cycle, ask one anonymous pulse respondent (via general team channels, not individually) whether their experience of the instrument matched its stated purpose. Correct any confidentiality mismatch *before* the second quarter.
6. **Action-trace test.** For each quarterly cycle, trace every "top interpretation" to a named action with a date. Interpretations without actions are the single clearest sign that qualitative data is not being used.
7. **Comparability check.** At quarter 4, compare response distributions to quarter 1. Any surprising stability (zero movement) in a quarter where the rollout obviously changed is suspicious — either the instrument is desensitised or the team is self-censoring. Investigate.

Validation is **procedural and behavioural**: you are checking that the instrument changes decisions the team makes, not that the survey reads well in isolation.

## 4. Rubric

Use this to grade a submitted qualitative instrument kit. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Cadence design** | One instrument, one cadence | Two instruments | Three instruments at distinct cadences | Three instruments with explicit trigger criteria for the interview layer |
| R2 | **Length discipline (quarterly)** | > 20 questions | 15–20 | 10–14 | 10–12, segmented by theme, one page |
| R3 | **AI items separated from DevEx baseline** | Mixed throughout | Added items not clearly separated | Dedicated AI section | Dedicated AI section + rationale for the separation + AI items evaluated independently in analysis |
| R4 | **Pulse design** | No pulse | Pulse > 10 items | Pulse ≤ 5 items, mix of closed + open | Pulse ≤ 5 items + Q2-style free text + one AI net-effect question |
| R5 | **Interview rigor** | Ad hoc | Fixed guide, no triggers | Semi-structured guide + consent flow + trigger criteria | All of 2 + anonymisation + verbatim-quote protocol + opt-in neutral interviewer |
| R6 | **Confidentiality and use** | Not addressed | Mentioned vaguely | Written confidentiality rules | Written rules + explicit prohibition on performance-review use + retention/deletion schedule |
| R7 | **Analysis and action loop** | No analysis plan | Results shared once | Shared back + top interpretations named | Shared back + interpretations + named actions + re-check next cycle |
| R8 | **Comparability over time** | Instrument changes freely | No change log | Change log + flagged discontinuities | Change log + flagged discontinuities + explicit freeze-by-default policy + dated amendments |

**Pass bar: 16/24.** Below that, iterate before shipping the instrument to the team.

## 5. Common mistakes

- **Writing a 40-question survey.** Length is the enemy of response rate. Everything past 12 reduces completion probability and introduces fatigue bias.
- **No open-text field.** A survey with only closed items captures only what the designer predicted; the open-text item is where you learn what you did not expect.
- **AI items mixed into every DevEx question.** You want to be able to say "DevEx moved, and here is how much of that is attributable to the AI rollout." Mixing destroys that separation.
- **Rewording the quarterly every cycle.** Comparability year-over-year is more valuable than any single improvement in wording. Freeze by default; log changes with dates.
- **Running the instrument and not sharing results back.** The *single* most reliable way to kill future response rates is to collect data and go silent. Share back within two weeks, every cycle, even if the result is embarrassing.
- **Using survey results for performance reviews.** This is both ethically wrong (participants were told anonymous) and operationally self-defeating (the next survey is a rubber stamp or a protest). The policy must be explicit.
- **Interviews without triggers.** Random interviews are expensive and dilute the signal. Trigger on anomalies and milestones.
- **No coding scheme for interview notes.** Free-form notes across 10 interviews are unreadable. A short fixed code list applied consistently yields themes you can actually act on.
- **Quantitative average without the distribution.** A 4.2/5 mean with a bottom quartile at 2.0 is a different team than a 4.2 with a bottom at 3.5. Show the shape.
- **Silence bias ignored.** If response rate drops in a quarter, that *is* data. Investigate.

## 6. References

- Forsgren, N., Storey, M.-A., Maddila, C., Zimmermann, T., Houck, B., & Noda, J. (2021). *The SPACE of Developer Productivity.* ACM Queue, 19(1). <https://queue.acm.org/detail.cfm?id=3454124> — the Satisfaction & well-being dimension is the explicit grounding for the quarterly survey's sections A, B, C.
- Noda, A. & Forsgren, N. and the DX research team. *DevEx: What actually drives productivity.* DX / `getdx.com`. <https://queue.acm.org/detail.cfm?id=3595878> — the *Developer Experience* framework (feedback loops, flow, cognitive load) is the source of the quarterly section A/B items. (Published in ACM Queue, 2023.)
- Tacho, L. & Noda, A. (2024). *DX Core 4.* DX / `getdx.com`. <https://getdx.com/research/measuring-developer-productivity-with-the-dx-core-4/> — the DevEx leg of the Core 4 is what this quarterly instrument feeds.
- Ziegler, A. et al. (2024). *Measuring GitHub Copilot's Impact on Productivity.* CACM. <https://cacm.acm.org/research/measuring-github-copilots-impact-on-productivity/> — supports the design choice to measure perceived helpfulness and acceptance patterns qualitatively, not only via telemetry.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function's documentation requirements and the Measure function's stakeholder-feedback expectation map onto the policy document in section 2.4.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [MITRE ATLAS](https://atlas.mitre.org/) — relevant when the "biggest reason" selection on Q10 is a privacy/safety concern; those answers should be routed to the team's security review path, not aggregated away.
- Exercise 01 (SPACE / DX Core 4 side by side) and Exercise 04 (exec pre-read) — the quarterly DevEx numbers from this instrument feed both.

<!-- follow-up: once four quarterly cycles are in, add a "stability analysis" section that reports which items have been stable across cycles (and therefore reliable as DevEx baselines) and which drift (and therefore belong under watchful interpretation). -->
