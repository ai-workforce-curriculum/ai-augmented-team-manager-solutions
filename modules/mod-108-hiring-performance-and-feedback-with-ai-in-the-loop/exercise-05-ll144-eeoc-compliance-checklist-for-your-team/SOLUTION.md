# Exercise 05 — LL144 / EEOC Compliance Checklist For Your Team

> Module: `mod-108-hiring-performance-and-feedback-with-ai-in-the-loop`
> Exercise type: **design** (worked checklist artifact + runbook + grading rubric)

## 1. Solution overview

The deliverable is a **team-manager's operational compliance checklist** for AI
use inside the hiring, performance, and feedback surfaces that the manager
directly controls. It is scoped against **NYC Local Law 144 (Automated
Employment Decision Tools)** and **EEOC guidance on employer use of AI** in
employment decisions (notably the ADA guidance issued in 2022 and the Title
VII guidance issued in 2023). It is the artifact the manager runs quarterly,
plus on every triggered event, to verify that the team's practices are not
quietly drifting out of compliance between formal legal reviews.

This is **operational hygiene, not a legal defense.** The checklist does three
things only: surfaces which of the team's tools plausibly fall inside LL144
or EEOC AI scope so legal/HRBP/DPO can make the actual call; forces the
manager to name the notice, bias audit, and documentation obligations that
*could* attach and confirm the artifacts exist; and produces a dated, signed
record that the manager performed the review. It is **not** a legal
interpretation, **not** a substitute for an independent bias audit, and
**not** a replacement for counsel. Legal review is a mandatory gate before
adopting any tool this checklist flags.

A good checklist is **checkable** (every line is a verb plus an artifact —
a URL, document ID, dated email), **scope-aware** (names the triggers that
bring LL144 into play; over-scoping drowns real signals as badly as
under-scoping), **jurisdictionally honest** (LL144 is NYC; EEOC is federal;
Illinois AIVIA, Colorado AI Act, and EU AI Act are referenced for
escalation only), **bounded by role** (the manager is not auditor, counsel,
or DPO), and **dated and signed** (every run is a record; the record is the
point).

Design decisions this solution encodes:

1. **Routing artifact, not a judgment artifact.** When a line fails, the
   remediation is almost always "escalate to legal/HRBP," not "manager
   fixes." The checklist makes the escalation happen while there is time.
2. **AEDT scope is a legal determination, not a manager determination.**
   The manager flags candidates; legal confirms. This blocks the failure
   mode where a manager decides a tool is "just a helper" and skips audit.
3. **Bias audit is an external, independent activity.** The manager's job
   is to know when one is required, who has the current one, when it
   expires, and how to request a new one — not to perform it.
4. **Notice is templatized through legal.** The checklist points at the
   approved template rather than embedding notice language, because
   notice language is jurisdiction-specific and changes.
5. **Retention windows are placeholders.** They vary by jurisdiction,
   data category, and evolving rules. The checklist marks them
   `<!-- tbd-confirm: ... -->` for legal to fill in rather than
   hardcoding numbers the manager cannot defend.
6. **Quarterly plus triggered re-runs.** Annual attestation is not
   credible because the tool landscape moves monthly. New tool, new
   jurisdiction, new candidate pipeline, new assessment all force an ad
   hoc run regardless of calendar.

This checklist attaches naturally to Exercise 01 (the interview loop
redesign, which defines which steps involve AI-assisted screening and
assessment) and Exercise 02 (the levelling criteria, where AI-assisted
performance signals cross into promotion and compensation decisions). When
either of those artifacts changes, this checklist must run again.

## 2. Implementation — the compliance checklist

### 2.1 Scope and prerequisites

Before running the checklist the manager has on hand: the team's **tool
inventory** (AI tools touching sourcing through promo packet drafting), the
**interview loop definition** (Exercise 01), the **levelling criteria and
performance review process** (Exercise 02), the **legal / HRBP / DPO point
of contact list** with escalation path, the **last bias audit summary** on
file with date and expiry, and the **candidate and employee notice
templates** approved by legal. If any of the above is missing, that absence
is the first finding.

### 2.2 The checklist

```markdown
# Team AI-in-Hiring/Performance Compliance Checklist
Team: ____________________  Manager: ____________________
Run date: __________  Run type: [ ] Quarterly  [ ] Triggered (why: _______)
Last run date: __________  Last run owner: __________

> This checklist is operational hygiene only. It is not legal advice and does
> not replace counsel. Failing items route to Legal / HRBP / DPO. Signing
> this checklist attests that the manager performed the review in good faith
> on the date shown, not that the team is legally compliant.

## Section A — Scope & triggers

- [ ] A1. I have enumerated every tool, model, script, or agent the team
      uses in: sourcing, resume screening, interview scheduling, interview
      scoring, take-home review, calibration, performance review drafting,
      promo packet drafting, feedback summarization, PIP drafting.
- [ ] A2. For each tool in A1, I have recorded: vendor, version, where it
      runs (team / company / vendor cloud), what inputs it sees, what
      outputs it produces, who acts on the output.
- [ ] A3. I have flagged every tool whose output could "substantially
      assist or replace discretionary decision-making" for an employment
      decision (hire, promote, transfer, terminate, compensate) as a
      *candidate AEDT* and routed the list to legal for determination.
      [Reference: NYC Local Law 144, DCWP implementing rules]
- [ ] A4. I have identified every candidate or employee location in the
      pipeline that touches a NYC resident or NYC work location, including
      remote hires who reside in NYC. Non-NYC headquarters does not remove
      scope.
- [ ] A5. I have identified every state/jurisdiction with its own AI
      employment law in play for our pipeline (e.g., Illinois AIVIA,
      Maryland facial analysis, Colorado AI Act, EU AI Act for EU
      candidates). I have escalated any that apply to legal; those are
      outside this checklist's scope but inside the team's obligations.
- [ ] A6. Legal has returned a written AEDT / non-AEDT determination for
      each tool from A3 and the determinations are on file with a date and
      a reviewer name. Determinations older than
      <!-- tbd-confirm: retention/refresh interval, confirm with legal -->
      have been refreshed.

## Section B — Pre-use: inventory, AEDT identification, notice

- [ ] B1. For every tool confirmed as an AEDT under LL144, a current
      independent bias audit is on file, performed by an auditor who meets
      the independence criteria in the DCWP rules.
      <!-- tbd-confirm: LL144 bias audit independence criteria specifics -->
- [ ] B2. For every tool confirmed as an AEDT, the bias audit summary has
      been published on the required public-facing surface, in the form and
      for the duration required by DCWP rules.
      <!-- tbd-confirm: LL144 publication duration and format -->
- [ ] B3. The bias audit expiry date for each AEDT is tracked in the team
      calendar with reminders ahead of expiry
      <!-- tbd-confirm: LL144 audit refresh cadence, confirm with legal -->.
- [ ] B4. The candidate notice template approved by legal is linked here:
      __________. I have confirmed that every active pipeline invokes the
      notice at the required point before the AEDT is used, with the
      required lead time
      <!-- tbd-confirm: LL144 candidate notice lead time -->.
- [ ] B5. The candidate notice names the job qualifications and
      characteristics the AEDT will assess and provides the required
      alternative-process or accommodation path. Legal has approved the
      wording on this version.
- [ ] B6. Every new tool proposed for the hiring/performance surface goes
      through this checklist *before* pilot, not after. Pilots involving
      real candidate or employee data without legal sign-off are prohibited.

## Section C — Bias audit & reporting

- [ ] C1. I understand that the bias audit is performed by an independent
      third party, not by me, not by the vendor alone, not by our internal
      ML team. My role is to request, track, and consume the audit, not
      perform it.
- [ ] C2. For each AEDT, the most recent bias audit report (or its summary)
      is linked here with: auditor name, audit date, scope of data used,
      demographic categories evaluated, and headline findings.
- [ ] C3. I have read the summary findings, not just the headline, and
      have raised any finding I do not understand with legal/HRBP before
      continuing use of the tool.
- [ ] C4. Where the audit used historical data from a different employer
      or population than ours, I have flagged that limitation to legal and
      confirmed the audit is still acceptable under the rules.
      <!-- tbd-confirm: LL144 historical-data audit acceptability -->
- [ ] C5. The team has a written procedure for pausing an AEDT if a bias
      audit lapses, finds material disparate impact, or if the vendor
      changes the model behind the tool. The procedure names who can pull
      the lever.
- [ ] C6. "Bias audit" and "bias certification" are not used
      interchangeably in our documentation. The audit is a measurement
      activity with an expiry; it is not a permanent certification.

## Section D — Candidate / employee notice & disclosure

- [ ] D1. Candidate-facing notice fires at the required moment in every
      pipeline variant (full-time, intern, contractor-to-hire, internal
      transfer). I have walked each variant end-to-end this quarter and
      confirmed the notice is visible.
- [ ] D2. The notice names a working contact for accommodation requests
      and alternative-process requests. The contact is monitored; I have a
      log of accommodation requests received and resolved this quarter.
- [ ] D3. Employee-facing uses (performance review AI drafting, promo
      packet summarization, feedback aggregation) have their own notice
      and consent path approved by legal and HRBP. I do not assume
      employee notice is covered by the candidate notice.
- [ ] D4. EEOC ADA considerations are addressed for every AEDT and every
      employee-facing AI tool: reasonable accommodation is available, the
      tool does not screen out individuals with disabilities on protected
      bases, and the accommodation request path is friction-light.
      [Reference: EEOC guidance on ADA and AI, 2022]
- [ ] D5. EEOC Title VII considerations are addressed: I have escalated
      any tool whose outputs appear to correlate with race, color,
      religion, sex, or national origin on inputs I can inspect, even if
      the bias audit passed.
      [Reference: EEOC guidance on employer use of AI, 2023]
- [ ] D6. Candidates and employees can request a human review of an
      AI-assisted employment decision that affects them. The request path
      and SLA are documented.

## Section E — Documentation & retention

- [ ] E1. The team keeps a dated record of every AEDT use that resulted in
      a shortlist cut, a rejection, a promotion decision, or a performance
      rating change, with the human decision-maker named.
- [ ] E2. The retention window for AEDT output, bias audit reports,
      candidate notices, accommodation requests, and manager review
      records is set at
      <!-- tbd-confirm: retention windows per jurisdiction, confirm with legal -->
      and is enforced by document management policy, not by manager memory.
- [ ] E3. Model version changes from the vendor are logged, with date and
      impact assessment. A model version change is a trigger for a
      re-review of AEDT status and (if AEDT) a re-audit question to legal.
- [ ] E4. Prompts, system prompts, and in-context instructions used by the
      team to drive AI-assisted hiring/performance drafting are
      version-controlled and retained with the output they produced.
- [ ] E5. Training, calibration, and vendor demo recordings that contain
      candidate or employee data are retained per policy, not on personal
      drives.
- [ ] E6. Nothing in this checklist, this quarter's artifacts, or the
      underlying evidence is stored in a tool that is itself out of scope
      for the team's data handling policy (e.g., personal cloud accounts,
      unapproved SaaS).

## Section F — Coordination & redlines the manager cannot cross alone

- [ ] F1. I have confirmed the current legal, HRBP, and DPO points of
      contact and their backup. Names and dates recorded here: __________.
- [ ] F2. Any novel tool, novel use case, novel jurisdiction, or novel
      model version change goes to legal *before* production use, not
      after. I have a written escalation procedure.
- [ ] F3. I do not author legal interpretations of LL144, EEOC guidance,
      or any other statute. Where this checklist asks "does X apply," my
      answer is always either "legal confirmed yes/no on date Z" or
      "escalated to legal on date Z, awaiting response."
- [ ] F4. I do not negotiate bias audit sufficiency, retention windows, or
      notice language with vendors without legal in the loop.
- [ ] F5. I do not use an AEDT that lacks a current bias audit, regardless
      of vendor assurances. "Vendor says it's fine" is not a check that
      passes.
- [ ] F6. Where this checklist is silent or ambiguous, I escalate rather
      than interpret. External counsel is the final gate for ambiguous
      cases and is reached through legal, not directly.

## Sign-off

Manager signature: ____________________  Date: __________
Legal reviewer (for triggered runs or quarterly spot check): __________
Next scheduled run: __________  Next known trigger: __________
```

### 2.3 Quarterly runbook

```markdown
# Quarterly LL144/EEOC Checklist Runbook

Pre-work (owner: manager, lead time: 1 week)
  - Refresh tool inventory from current team practice (walk the pipeline
    end-to-end; do not trust the old list).
  - Pull current bias audit documents and expiry dates.
  - Pull candidate notice template and confirm it is the current
    legal-approved version.
  - Pull last run's artifact and findings list; carry forward any open
    items that have not been closed.

Run (owner: manager, duration: ~90 minutes)
  - Walk the checklist top to bottom. Do not skip. Mark fails honestly.
  - For every fail, write a one-line finding with an owner and a date.
  - Do not fix fails during the run; the run is a review, not a repair.

Post-run (owner: manager, lead time: 1 week)
  - File findings into the team's compliance tracker with owner and date.
  - Escalate any new AEDT candidates, any lapsed audits, any missing
    notices to legal immediately, not as part of a batch.
  - Share a short summary with HRBP and skip-level manager. The summary is
    "what changed and what is open," not the full artifact.

Triggered re-runs (owner: manager, immediate)
  - New tool added to the hiring/performance surface.
  - New jurisdiction added to the hiring pipeline.
  - Vendor model version change.
  - New assessment type or new candidate population (e.g., first intern
    class, first senior hire pipeline).
  - Any candidate or employee complaint touching AI-assisted process.
```

### 2.4 Why these sections and not others

**Scope first** because the common failure mode is running a perfectly good
checklist on the wrong tools; a wrong AEDT determination poisons everything
downstream. **Pre-use before use** because audits and notices must exist
before production, not after a candidate raises a concern. **Bias audit as
its own section** because conflating "we have a bias audit" with "we are
LL144 compliant" is the single most common vendor pitch failure, and the
manager needs specific language to refuse it. **Notice as its own section**
because candidate-facing and employee-facing notice have different
triggers, content, and owners. **Documentation separately** because every
section above produces retention-governed artifacts. **Coordination and
redlines last** because the checklist's final posture — "what the manager
must not do alone" — is what the manager should walk away remembering.

## 3. Validation steps

Before adopting this checklist as the team's quarterly artifact, run these
checks:

1. **Dry run against the current pipeline.** Walk the checklist end-to-end
   against today's tools. Un-checkable lines are real findings; do not
   hide them by revising the line.
2. **Dry run against last quarter.** Try to answer each line using only
   artifacts that existed at end of last quarter. Lines that cannot be
   answered reveal documentation gaps.
3. **Legal review of the checklist itself.** Legal confirms the scoping
   language is accurate for LL144 and EEOC, the placeholders are
   acceptable to leave open, and no line reads as a legal interpretation
   the manager is not qualified to make.
4. **HRBP review of notice and accommodation lines.** HRBP confirms D1–D4
   reflect the actual candidate experience and that the accommodation path
   is monitored in practice.
5. **DPO review of retention and tooling.** DPO confirms Section E maps
   to the data classification policy and no checklist artifact lives in an
   unapproved location.
6. **Independence check on auditor references.** Confirm the auditor named
   in C2 for each AEDT meets DCWP independence criteria as interpreted by
   legal. Do not accept vendor-affiliated audits silently.
7. **Cross-reference to Exercise 01 and Exercise 02.** Every AI-touched
   step in the interview loop (Exercise 01) and every AI-assisted
   performance signal in the levelling criteria (Exercise 02) must appear
   in this checklist's tool inventory.
8. **Scheduled-run drill.** Put the next run on the calendar, confirm
   trigger events have a written escalation path, and tabletop one
   triggered scenario ("vendor pushed a model update yesterday") before
   relying on the process.

## 4. Rubric

Grade the checklist artifact, not the state of compliance it reveals. A
team with a bad state and an honest checklist scores higher than a team
with a quiet checklist and an unknown state.

| Dimension | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Scope definition (AEDT + jurisdictions) | No scope section or asserts "no AI in hiring" with no inventory | Tool inventory present but AEDT classification done by manager alone | Inventory complete; AEDT candidates routed to legal; NYC-resident remote case covered | All of 2 plus other-jurisdiction sweep (Illinois, Colorado, EU, etc.) and vendor-model-change trigger |
| Pre-use controls (audits, notice) | No audit tracking, no notice gating | Audit tracking exists; notice gating ad hoc | Audit expiry tracked on calendar; notice templatized through legal and gated before use | All of 2 plus pilot-before-production rule and model-version refresh logic |
| Bias audit framing | Audit treated as one-time certification or as manager self-audit | Audit external but independence not verified | Audit external, independent per DCWP, summary read and understood | All of 2 plus procedure to pause the tool on lapse or material finding, and audit-vs-certification language explicit |
| Candidate/employee notice & accommodation | No notice process or boilerplate only | Candidate notice exists; employee-facing uses not addressed | Candidate and employee notice distinct, accommodation path monitored | All of 2 plus human-review-of-AI-decision request path with SLA |
| Documentation & retention | Ad hoc notes, personal drives | Centralized docs but retention window unstated | Retention windows placeheld pending legal, version-controlled prompts and outputs | All of 2 plus model-version change log and training/recording data policy |
| Legal/HRBP/DPO coordination | Named contacts missing | Contacts named; no written escalation path | Escalation path written; manager explicitly does not interpret statute | All of 2 plus external counsel escalation defined and a real example of an escalation this quarter |
| Checkability (verb + artifact per line) | Lines are aspirational statements | Mix of aspirational and checkable | All lines name an artifact or named person and date | All of 2 plus every line's artifact is actually linked/filled in the current run |
| Cadence and triggers | No cadence; "when we remember" | Quarterly scheduled, no triggers | Quarterly plus named triggers (new tool, new jurisdiction, model change) | All of 2 plus tabletop-drilled triggers and a dated history of triggered runs |

**Pass bar: 16 / 24, with no dimension at 0, and no dimension related to AEDT
classification, bias audit, or legal coordination below 2.** A score above
the pass bar means the checklist is credible team-level hygiene; it still
does not substitute for legal review before adopting any specific tool.

## 5. Common mistakes

1. **Treating the checklist as a one-time exercise.** Run once and never
   again is worse than no checklist — it creates a false paper trail.
   Quarterly plus triggered is the floor.
2. **Confusing a bias audit with a bias certification.** Vendors pitch the
   former as the latter. An audit is a measurement with an expiry and a
   scope; it is not a lifetime fairness stamp.
3. **Manager-authored legal interpretation.** "I believe LL144 does not
   apply here because..." in a manager's handwriting is a failure. The
   acceptable form is "escalated to legal, determination on file."
4. **Assuming non-NYC teams are out of scope when they hire NYC
   residents.** LL144 scope follows candidate and work location, not
   company headquarters. Remote hires in NYC pull the team into scope.
5. **Treating "AI" as a binary.** Not every AI-flavored tool is an AEDT;
   not every non-AI tool is exempt. The question is whether the tool
   substantially assists or replaces discretionary decision-making for a
   covered employment decision — a legal question, not a marketing one.
6. **Running the checklist on sourcing and skipping performance** (or
   vice versa). EEOC Title VII and ADA concerns extend to performance,
   promotion, and termination; LL144 covers employment decisions broadly.
7. **Taking vendor compliance claims at face value.** "SOC 2," "GDPR
   ready," and "bias-tested" are not substitutes for an independent bias
   audit that meets DCWP rules. Require the actual audit artifact.
8. **Storing the checklist's evidence in an unapproved tool.** Personal
   note apps, public docs, or unsanctioned SaaS holding accommodation
   requests or AEDT outputs is itself a finding and a privacy risk.
9. **Letting candidate notice language drift from the legal-approved
   version.** Managers who edit notice copy "to sound friendlier" create
   enforcement exposure. Point at the template; do not rewrite it.
10. **Collapsing EEOC obligations into LL144.** LL144 is NYC statute;
    EEOC is federal; they do not supersede each other. A tool that passes
    a LL144 audit can still raise Title VII or ADA issues, and vice versa.

## 6. References

**Authorized module sources**

- **NIST AI Risk Management Framework** — <https://www.nist.gov/itl/ai-risk-management-framework>.
  The RMF's **Govern** function underwrites every line of Section F
  (coordination, redlines, escalation). The **Map** function underwrites
  Section A (scope, triggers, AEDT determination). The **Measure**
  function is where bias audit (Section C) lives in the framework's
  language. The **Manage** function is Section E (retention, document
  management, model-version tracking). Running this checklist quarterly
  is a concrete expression of the RMF applied to the hiring and
  performance surface.
- **OWASP Machine Learning Security Top 10** — <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Relevant for the inputs side of Section E: model-version change
  tracking and prompt/instruction retention map to ML supply chain and
  input manipulation risks. A vendor model change that looks like a
  silent upgrade is both a compliance trigger and a supply chain event.
- **MITRE ATLAS** — <https://atlas.mitre.org/>.
  Adversarial pressure on the AI-assisted hiring and performance pipeline
  (prompt injection in resumes, adversarial content in take-homes,
  manipulation of scoring prompts) sits in Section B's pre-use controls
  and Section E's prompt retention. ATLAS gives the vocabulary for the
  threat side of the AEDT pipeline, which complements the fairness side
  that LL144 and EEOC address.

**Regulatory frames referenced by name only**

- **NYC Local Law 144 (Automated Employment Decision Tools)** and the
  **NYC Department of Consumer and Worker Protection (DCWP) implementing
  rules.** Scope, AEDT definition, bias audit, publication, and
  candidate notice obligations referenced throughout Sections A–D.
  <!-- tbd-confirm: specific DCWP rule citations and current revision date -->
- **EEOC guidance on employer use of AI**, including the ADA guidance
  issued in 2022 and the Title VII guidance issued in 2023. Referenced
  in Section D (accommodation, Title VII input correlations, screen-out
  risk) and Section F (escalation of ambiguous EEOC concerns).
  <!-- tbd-confirm: exact titles and publication dates of each EEOC document -->

> **This checklist is operational hygiene. It does not replace counsel
> from an attorney licensed in the relevant jurisdiction.** Every finding
> that touches LL144 scope, EEOC scope, or any other employment statute
> routes to legal. The manager signs the run; counsel signs the
> compliance posture.
