# Exercise 01 — Team Incident Runbook Authoring

> Module: `mod-109-accountability-incident-response-and-blameless-postmortems`
> Exercise type: **design** (runbook document + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **team-scoped AI-incident runbook** that an on-call
engineer can open at 3 a.m. and act from without having to interpret policy.
It is the artifact that converts "we have an AI governance program" into
"the person holding the pager knows what to do when the model does
something wrong."

A good runbook is:

- **Acted from, not read through.** Every top-level section answers one
  question an on-call will actually type into a search bar: *is this an
  incident? who do I page? what do I turn off? what do I write down?*
- **Specific to AI failure modes the team actually ships.** Generic
  SRE runbooks already exist and do not cover prompt-injection,
  tool-call exfiltration, hallucinated API calls, or retraining-set
  poisoning. The team runbook is where those modes land with team-specific
  mitigations next to them.
- **Tied to real surfaces.** Every switch the runbook tells the on-call to
  flip ("disable tool X for tenant Y", "revoke token Z") must exist today
  and must be documented with the exact dashboard/PR/CLI invocation.
- **Blame-free in the response layer.** The runbook is read under stress;
  it must not create incentives to hide. Who-pushed-what is a post-incident
  question, not an in-incident one. (See exercise-02 for the postmortem
  side of the same discipline.)

Design decisions this solution encodes:

1. **The runbook is scoped to the team, not the company.** A central
   security playbook is necessary; it is not sufficient. The team owns the
   model, the prompts, the tool permissions, and the evals — so the team
   owns the first 30 minutes of response and the handoff contract
   upstream. (Hands-off coordination is covered by exercise-04.)
2. **Severity is defined before any specific incident.** If severity is
   argued during the incident, the runbook has already failed. Severity
   definitions live at the top and reference concrete, team-visible
   signals.
3. **Every mitigation is a one-command action.** "Consider rolling back"
   is not a mitigation; `./ops/disable-tool.sh <tool-id> <tenant-id>` is.
   If the command does not exist yet, the runbook calls that out
   explicitly and books work to make it exist — it does not paper over
   the gap.
4. **The runbook explicitly maps AI failure modes to shared taxonomies.**
   OWASP ML Top 10, MITRE ATLAS, and NIST AI RMF categories appear next
   to the response steps. This is how the team's incidents feed back into
   the industry's shared vocabulary rather than being invented locally
   each quarter.
5. **Containment comes before diagnosis.** The runbook pushes the
   on-call toward "stop the bleed" before "understand the root cause,"
   because diagnosis on a live model-serving pipeline risks making the
   blast radius bigger. Diagnosis belongs to the postmortem.

## 2. Implementation — the runbook

The sections below are the runbook itself, written for a fictional team
("Platform API") that operates an LLM-backed support-copilot. Teams
should treat this as a fill-in template: every name, dashboard link,
and command is a placeholder the team must replace with real artifacts.

### 2.1 Runbook document (template)

```markdown
# Platform API — AI Incident Runbook

**Owner:** <team manager>
**Last reviewed:** 2026-11-12 (review cadence: quarterly + after every SEV ≤ 3)
**Review date:** 2027-02-12
**Scope:** All production surfaces of the Support Copilot
(`copilot-api`, `copilot-tools-*`, `retrieval-index-*`) operated by
this team. Out of scope: shared auth, shared vector DB infra — handed
off to Platform Security per exercise-04 routing contract.

## 0. If you are reading this at 3 a.m.

- You are **on-call for the AI surface**, not for the whole platform.
- **First action is always containment, not diagnosis.** Jump to §3.
- **Page a human partner immediately for any SEV1 or SEV2**: see §2.
- **Do not reply publicly until the Incident Commander says so.**
  Customers hear from one voice.
- **Everything you type goes in the incident channel (`#incident-copilot-<id>`).**
  Memory is evidence. If it is not in the channel it did not happen.

## 1. Is this an incident? Severity definitions

A signal is an incident when **any one** of the following is true. If none
are true, it is a bug; file it normally and move on.

| Severity | Trigger (any one) |
|---|---|
| **SEV1** | Model output has caused (or is clearly on-track to cause) real-world harm, financial loss, or a privacy breach. Example triggers: regulated-data leak to a user who should not see it; tool call executed on a customer's behalf without their consent; model-generated instructions that caused a customer to take a destructive action. |
| **SEV2** | A systemic failure mode is active and not self-limiting. Example triggers: prompt-injection successfully exfiltrating a system prompt across ≥ 3 distinct user sessions in an hour; hallucinated tool call succeeding against production APIs; eval harness regressed > 20% on any safety metric in the last deploy. |
| **SEV3** | A bounded but reproducible failure is in production. Example triggers: single tenant seeing toxic output; retrieval returning cross-tenant chunks; latency SLO breach caused by model, not infra. |
| **SEV4** | Isolated report, not reproducible yet; or affects a non-prod surface. |

**If you are unsure between two severities, pick the higher one.**
Over-paging recovers; under-paging does not.

The AI-specific trigger table below maps common failure modes to the
severity they *usually* warrant. The mapping is a hint, not a
substitute for judgement.

| Failure mode | Default severity | Shared-taxonomy label |
|---|---|---|
| Prompt injection extracting system prompt or tool credentials | SEV2 (SEV1 if credentials grant external action) | OWASP ML09 *Output Integrity Attack*; MITRE ATLAS *AML.T0051 LLM Prompt Injection* |
| Training/fine-tuning data leaked in model output | SEV1 | OWASP ML02 *Data Poisoning / Leakage*; NIST AI RMF *Measure 2.10 Privacy-Enhanced* |
| Model making tool calls that mutate state without user consent | SEV1 | MITRE ATLAS *AML.T0053 LLM Plugin Compromise* |
| Hallucinated reference to an API, library, or policy that does not exist, acted on by the user | SEV2 if widespread; SEV3 otherwise | OWASP ML08 *Model Skewing* (closest analog); NIST AI RMF *Measure 2.9 Explainability* |
| Retrieval returning data from one tenant to another | SEV1 | OWASP ML06 *AI Supply Chain Vulnerabilities*; NIST AI RMF *Govern 1.6 Risk management processes* |
| Safety-eval regression > 20% post-deploy | SEV2 | NIST AI RMF *Measure 2.7 Safety* |
| Model output producing harassment / disallowed content in customer-facing surface | SEV2 | NIST AI RMF *Measure 2.11 Fairness* |
| Model-training data poisoning detected in incoming feed | SEV2 | OWASP ML02 *Data Poisoning Attack*; MITRE ATLAS *AML.T0020 Poison Training Data* |

> The labels in the right column come from OWASP Machine Learning Security
> Top 10, MITRE ATLAS, and NIST AI Risk Management Framework. Use them in
> the incident channel so the postmortem (exercise-02) can be keyed into
> the industry taxonomy without translation.

## 2. Paging tree

| Role | Who (today) | How to page | When |
|---|---|---|---|
| Incident Commander (IC) | On-call rota in PagerDuty service `copilot-ic` | PD escalation | SEV1, SEV2 |
| Team manager | <name> | PD + Slack DM | SEV1 |
| Risk Engineer partner | Risk-Eng on-call (see exercise-04) | `/page risk-eng` in `#incident-copilot-<id>` | SEV1, SEV2, any model-safety trigger |
| SecOps partner | SecOps on-call (see exercise-04) | `/page secops` | SEV1, any prompt-injection / credential / exfil trigger |
| Legal / Privacy | Privacy on-call rota | PD service `privacy-oncall` | SEV1 involving user data |
| Comms | Comms on-call | Slack `#comms-oncall` | Any SEV1 with likely customer visibility |

If a role is not paged within **5 minutes of declaration**, the on-call
escalates one level up the PD chain. No silence as a signal.

## 3. Containment (first 15 minutes)

Pick the smallest action that stops the bleed. The sequence is roughly
"turn the specific thing off" → "turn the surface off" → "turn the
service off." Do not jump straight to the biggest hammer.

1. **Identify the surface.** Is it one tenant? One model variant? One
   tool? Record the scope in the channel in one line.
2. **Disable the smallest thing that stops it.**
   - Disable a single tool for a single tenant: `./ops/disable-tool.sh <tool-id> --tenant <tid>`.
   - Disable a single tool globally: `./ops/disable-tool.sh <tool-id>`.
   - Flip the model variant back to the previous known-good: use the
     `MODEL_PIN` feature flag in the `copilot-config` dashboard; the
     link is pinned in `#incident-copilot-<id>`.
   - Disable the surface: toggle `copilot-enabled=false` for the
     affected tenant(s).
   - Disable the service: route all traffic to the deterministic
     fallback (`fallback-canned-reply`). This is the last-resort
     switch.
3. **Preserve evidence before you flip anything destructive.**
   - Snapshot the live prompt + tool-call trace for the suspected
     session(s): `./ops/snapshot-session.sh <session-id>`. This is
     non-destructive and must run *before* you revoke credentials.
   - If credentials are suspected compromised, snapshot the
     `AuditLog` table slice for the window first, then rotate.
4. **Record the containment action in the channel in a single message
   with the format:**
   `/contain <action> | scope: <who/what> | taxonomy: <OWASP/ATLAS/RMF label> | verifier: <who confirms it stuck>`

## 4. Diagnosis (after containment, not before)

Diagnosis is the IC's call and typically pulls in Risk Engineering
and/or SecOps per the exercise-04 routing contract. The on-call's job
during diagnosis is **narrating evidence**, not forming conclusions.

Diagnosis uses these artifacts:

- Live prompt + tool-call traces captured in §3.
- Retrieval logs for the window: `./ops/retrieval-log.sh --since "-30m"`
- Safety-eval results for the current and prior deploys.
- Model and prompt version pins (both live and prior).
- Any customer report text verbatim (not paraphrased).

Do **not** attempt to fix the model, prompts, or tools during diagnosis.
Fix belongs to a change managed through normal review (see mod-103 and
exercise-05 for the regression-fixture capture that *will* be required
before any fix merges).

## 5. Customer communication

- IC owns customer comms. On-call drafts; IC approves; Comms sends.
- **Do not claim a cause during an active incident.** State impact and
  mitigation, not hypothesis. "We have disabled Feature X for tenants A
  and B while we investigate" is good. "This was caused by a prompt
  injection" during an open incident is not.
- Status page cadence: initial within 15 min of SEV1/SEV2 declaration;
  update at least every 30 min until resolved.

## 6. Resolution and handoff

An incident moves to **resolved** when **all three** are true:

1. Containment action is verified by a human who is not the one who
   took it. "Verified" means they ran the explicit check listed in the
   containment playbook step.
2. No new reports of the signal for a defined watch window (SEV1:
   60 min; SEV2: 30 min; SEV3: 15 min).
3. A follow-up ticket exists, linked in the channel, with an owner
   and a target date.

The incident does **not** resolve on vibes. It resolves on verified
checks.

Handoff to the postmortem (exercise-02) is automatic: the IC schedules
the blameless review within 5 business days and captures a regression
fixture (exercise-05) within 2 business days.

## 7. What never goes in the runbook

- Names of blame. "John deployed the bad prompt" is a postmortem data
  point only if it is required to understand the mechanism, and even
  then the postmortem uses role not name (see exercise-02).
- Guesses about root cause during the incident.
- Fixes. The runbook is for response, not for mitigation development.
- Marketing language.

## 8. Known gaps (as of this review)

- `./ops/snapshot-session.sh` does not yet capture retrieval provenance.
  Owner: <name>; target 2026-12-01.
- No one-command rollback for prompt-template changes; today it is a
  three-step dashboard flip. Owner: <name>; target 2026-12-15.
- Retrieval log retention is 7 days; privacy and regulated-use-case
  investigations may need 30. Flagged with Privacy; see exercise-04 for
  the routing contract that owns the escalation.

## 9. Change log

- 2026-11-12 (initial version) — <team manager>
```

### 2.2 Why the runbook looks like this

- **Severity before anything else.** The one thing that goes wrong most
  often in real AI incidents is people arguing about whether they are in
  one. Fixing that up-front removes the most common source of delay.
- **Shared-taxonomy labels right next to the trigger table.** This is
  the single mechanism that lets individual-team incidents aggregate into
  organization-wide trend data. Without the labels, every team invents its
  own vocabulary and the governance program (mod-110) has nothing to
  roll up. NIST AI RMF, OWASP ML Top 10, and MITRE ATLAS are the
  references used here; the pattern is to pin the specific sub-item (e.g.
  *OWASP ML09*) next to the trigger, not just the top-level framework
  name.
- **Containment before diagnosis is a physical property of AI systems.**
  A running LLM-backed service can be actively causing harm *while* you
  diagnose. The runbook pushes the on-call away from "poke at the problem
  until I understand it" and toward "stop it, then understand it."
- **Every switch has a known command.** If the runbook says "disable the
  tool" but the on-call cannot find the command, the runbook has failed.
  Section 8 ("known gaps") is the honest counterpart: where the
  commands do not yet exist, they are named as known gaps with owners.
- **"What never goes in the runbook."** The blame-free discipline is
  written into the response-time artifact, not only into the postmortem.
  By the time the postmortem tries to recover a blame-free culture, the
  damage of a name-in-the-runbook is done.

## 3. Validation steps

Before shipping the runbook, verify:

1. **Walk every command.** Every CLI, dashboard link, and PagerDuty
   service in the document resolves to something that exists today.
   The runbook is tested by the person who is not on-call this week;
   this flushes out tribal knowledge.
2. **Fire a tabletop exercise.** Pick two recent public AI-incident
   patterns (e.g., LLM prompt-injection, retrieval cross-contamination)
   and have the on-call declare, contain, and resolve without reading
   anything other than the runbook. If they reach for Slack or Google,
   the runbook has gaps.
3. **Severity disagreement test.** Give three recent internal bug reports
   to two engineers independently and ask them to assign severity using
   only the table. If they disagree, the trigger table is ambiguous and
   needs tightening.
4. **Blast-radius check.** Every containment command in §3 has a
   verifier in §6. Confirm each verifier is a human action, not a
   dashboard showing a dashboard.
5. **Paging-tree aliveness test.** Call every role in the paging tree
   during business hours; confirm the paging path actually lands a
   human within the stated SLA. A paging tree that was true six months
   ago is not necessarily true today.
6. **Known-gaps freshness.** Known gaps older than the last review
   cadence are either resolved or bumped — never silently retained.

## 4. Rubric

Use this to grade a submitted runbook (self-assessment or peer review).
Each criterion scores 0–3. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Severity definitions** | Not present | Named but not keyed to signals | Keyed to team-visible signals | Keyed + explicit "pick the higher one" tiebreak |
| R2 | **AI-specific trigger table** | Missing | Lists failure modes without labels | Failure modes + shared-taxonomy labels | Failure modes + taxonomy labels + default severity + rationale |
| R3 | **Paging tree** | Role names only | Role names + how to page | Roles + how + when + fallback escalation | Full tree + tested-in-last-N-days timestamp |
| R4 | **Containment commands** | Prose only ("consider rolling back") | Named commands, not all tested | All commands exist and are one-liners | All commands exist, are one-liners, and have a verifier path in §6 |
| R5 | **Evidence preservation** | Absent | Mentioned | Named commands for snapshot/export | Named commands + ordered before destructive actions + retention called out |
| R6 | **Customer comms discipline** | Absent | Named owner | Named owner + "no cause claims during incident" rule | Named owner + no-cause rule + cadence + draft→approve→send path |
| R7 | **Resolution criteria** | Vibes | One criterion | Multi-criterion | Multi-criterion + verifier-is-not-the-actor + handoff to postmortem/regression |
| R8 | **Blame-free language** | Names in runbook | Mixed | Roles only in response layer | Roles only + explicit "what never goes in the runbook" section |
| R9 | **Known gaps honesty** | Absent or boilerplate | Gaps listed | Gaps listed + owners | Gaps + owners + dates + review-cadence commitment |

## 5. Common mistakes

- **Copying a generic SRE runbook and calling it done.** The resulting
  document covers service outages and misses every AI-specific failure
  mode. The giveaway: no mention of prompt injection, retrieval
  cross-contamination, or safety-eval regressions in the trigger table.
- **"Call the on-call" without a tree.** A single-role paging entry
  that reads "Slack #oncall" is not a paging tree; it is a wish. The
  rubric's R3 exists to catch this.
- **Containment steps that are not commands.** "Roll back the model" is
  not actionable at 3 a.m. by someone who did not deploy it.
- **Diagnosis before containment.** The most expensive AI-incident
  pattern is "we tried to understand what the model was doing wrong
  while it kept doing it wrong." Write containment first. Enforce it in
  the trigger table ordering.
- **Severity-by-argument.** If severity is defined only as examples the
  team argued about once, the next incident will reopen the argument.
  Signal-keyed severity removes the argument.
- **Taxonomy-free incidents.** If every incident label is invented on
  the spot, there is no aggregation layer upward. The OWASP/ATLAS/RMF
  labels are not decoration; they are the interface to the governance
  layer (mod-110).
- **No "known gaps" section.** A runbook without named gaps is a
  runbook that is pretending to be complete. The next real incident
  will find the gap on its own; the runbook should find it first.
- **Names-in-runbook.** Blame-free response is defended in the
  response-time artifact, not reconstructed in the postmortem. See
  exercise-02 for the postmortem counterpart.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function (accountability structures, incident response
  processes) and the *Manage* function (risk response, including
  containment and documented recovery) are the structural reasons the
  runbook has a paging tree, a resolution contract, and a known-gaps
  section. The *Measure* function's categories (Safety, Privacy,
  Explainability, Fairness) map to the trigger-table rows above.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  provides the per-row taxonomy labels on the trigger table (e.g. ML02
  Data Poisoning, ML06 Supply Chain, ML09 Output Integrity). The runbook
  references the labels directly so that aggregated incident data can be
  compared across teams and against industry data.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy
  for machine-learning systems. The AML.T0051 (LLM Prompt Injection) and
  AML.T0053 (LLM Plugin Compromise) entries in particular map directly
  to the most common high-severity triggers on an LLM-serving team, and
  are cited in the trigger table so that the postmortem (exercise-02)
  can use the same vocabulary.

## 7. Open follow-up

Once internal incident data exists, replace the "default severity"
column defaults with team-calibrated values. Current defaults are
judgement calls anchored to the OWASP / ATLAS severity descriptions,
not to team incident history.
