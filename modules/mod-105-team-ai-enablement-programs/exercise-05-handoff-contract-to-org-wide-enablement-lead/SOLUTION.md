# Exercise 05 — Handoff Contract to Org-Wide Enablement Lead

> Module: `mod-105-team-ai-enablement-programs`
> Exercise type: **design** (worked contract + decision rationale + grading rubric)

## 1. Solution overview

When an AI enablement program matures — the team manager has run Day 0 / 30 / 60 / 90 at least once (see Exercise 01), has a working CoP (Exercise 04), and has a fluency-measurement artifact set (Exercise 03) — the organisation often appoints or promotes an **org-wide enablement lead**. That lead's job is to take practices that worked at one team and scale them. Without a written contract, this handoff is where good practices die: ownership is unclear, artifacts stay on the team manager's laptop, decisions sit in nobody's queue, and three months later the lead is "still ramping" while the team is "still figuring out who's in charge."

The deliverable is a **handoff contract**: a short, signed document between the team manager and the org-wide lead that transfers what, retains what, and specifies the terms of continued collaboration. It makes six things explicit that are usually fuzzy:

1. **What is being handed off** (artifacts, decisions, calendars, members, budgets).
2. **What is explicitly retained by the team manager** (things that must not drift upstream).
3. **How joint decisions are made** during the transition.
4. **What the org-wide lead owes back** to the team (data, improvements, exceptions).
5. **Success criteria** for the handoff itself.
6. **Rollback criteria** if the handoff fails.

Design decisions this solution encodes:

1. **Handoffs are transfers *of named artifacts*, not transfers *of responsibility in principle*.** A contract that says "the lead now owns enablement" fails on contact with the first Slack ping. A contract that says "the lead now owns the pattern library at this URL, owns the quarterly brief cadence, owns the tool-budget rubric revision schedule, and co-owns the review checklist" is actionable.
2. **Retained items are named.** The team manager keeps the accountability contract, the per-IC fluency scores, the tool-budget allocation for *their* team, and the 1:1 coaching data. If the handoff drags any of these upstream, it breaks the accountability layer the manager is obligated to maintain — which NIST AI RMF **Govern 1.1** explicitly requires to live with named accountable personnel.
3. **Joint-decision rules are pre-written.** During the first 90 days of handoff, the team manager and the lead disagree. The contract specifies how disagreements resolve (default: team manager prevails on team-local matters; lead prevails on org-shared artifacts; escalate to a named director otherwise).
4. **Data owed back to the team is enumerated.** Too many handoffs flow data upstream and never downstream. The contract lists what the lead will *return* (benchmarks, sibling-team patterns, policy updates, vendor negotiations).
5. **Success criteria are testable and dated.** "Handoff successful" is measured on specific deliverables at specific dates, not vibes.
6. **Rollback is explicit.** If the handoff fails — the lead goes on leave, is reorged, or is overwhelmed — the contract specifies how the team manager resumes ownership without losing continuity.
7. **The contract is a *live* document.** It is version-numbered, has a review date, and is co-signed on each revision. It is not a one-time email.

## 2. Worked implementation — the contract

### 2.1 Contract template

Save as `enablement-handoff-contract-v1.md` in the shared wiki, linked from the team's enablement one-pager (Exercise 01).

```markdown
# AI Enablement Handoff Contract

Between: <team manager name>, manager of <team>
And:     <org-wide enablement lead name>
Date:    <YYYY-MM-DD>
Version: 1.0
Review:  90 days from signing; next review date: <YYYY-MM-DD>

## 1. Scope of this contract

This contract covers the transition of the AI enablement
program's org-level artifacts and decisions from team-manager
ownership to org-wide-lead ownership. It explicitly does not cover
the team manager's local responsibilities for their team.

## 2. What is being handed off

The following artifacts, calendars, and decisions transfer to the
org-wide lead on signing:

| Item | Current location | New owner | Transfer date |
|---|---|---|---|
| Pattern library (CoP deliverable) | <wiki URL> | Lead | <date> |
| Failure-mode digest (CoP deliverable) | <wiki URL> | Lead | <date> |
| Shared evals + judge-prompt library | <repo URL> | Lead | <date> |
| Quarterly brief cadence (publishing + distribution) | — | Lead | <date> |
| Tool-budget rubric *template* (not team allocations) | <wiki URL> | Lead | <date> |
| Review-checklist proposal pipeline (upstream to review-standards owner) | <wiki URL> | Lead | <date> |
| Org-level enablement roadmap (one-page) | <wiki URL> | Lead | <date> |
| CoP stewardship coordination across teams | — | Lead | <date> |
| Vendor liaison for enablement-specific tools | — | Lead (with mod-111 vendor owner) | <date> |

For each row, the lead acknowledges they have read the artifact
and understand the cadence. Transfer is complete only when
acknowledgement is recorded in this contract's appendix A.

## 3. What is explicitly retained by the team manager

The following do not transfer and must not drift to the lead:

| Item | Reason |
|---|---|
| Accountability contract with the team | NIST AI RMF Govern 1.1 — named accountable personnel at team scope |
| Per-IC fluency scores (Exercise 03 artifact A) | Privacy; 1:1 coaching data |
| Tool-budget *allocation* for this team (Exercise 02) | Local workflow fit; local ICs |
| 1:1 coaching notes, journals, performance material | Privacy; manager-of-record |
| Team-local policies that implement the org policy | Local calibration |
| Day 0 / 30 / 60 / 90 plan for this team's next quarter | Local calendar reality |

If the lead needs any of these for an org-level decision, the team
manager produces an *aggregate* with identifying detail removed.

## 4. Joint-decision rules during transition (first 90 days)

- **Team-local matters** — the team manager prevails. Examples:
  which of this team's ICs get Pro-tier seats; whether to run a
  workflow experiment this quarter; whether to retire an internal
  prompt.
- **Org-shared artifacts** — the lead prevails. Examples: format of
  the quarterly brief; scope of the pattern library; policy
  escalation path.
- **Decisions affecting both** — team manager and lead co-sign. If
  they cannot agree within 5 working days, escalate to <named
  director>. The escalation is a one-pager stating the decision,
  each party's position, the decision wanted, and the deadline.
- **Vendor and procurement decisions** — co-sign with mod-111
  vendor owner. The lead and vendor owner together carry the
  org-level negotiation; the team manager retains veto on their
  team's seat count.

## 5. What the lead owes back to the team

The lead returns the following to this team (and all participating
teams), on the stated cadence:

| Return | Cadence | Format |
|---|---|---|
| Sibling-team pattern-library additions | Monthly | Changelog + top 3 highlights |
| Vendor negotiation outcomes (contract changes, tier changes, pricing changes) | As they happen, within 5 working days of execution | One-page brief + impact-on-this-team row |
| Policy updates from the governance owner | Within 5 working days of ratification | Diff + action items for this team |
| Benchmark comparison: this team's fluency dashboard vs. org median | Quarterly | De-identified aggregate |
| Escalations from the CoP that affect this team's scope | As they arise | DM + wiki entry |

The team manager owns consuming and acting on these; the lead
owns producing them.

## 6. Success criteria

The handoff is successful when all the following hold at the 90-day
review:

- **S1.** All artifacts in §2 have an acknowledgement entry in
  appendix A.
- **S2.** The lead has produced at least one quarterly brief.
- **S3.** At least one pattern-library entry from a sibling team
  has been returned to this team.
- **S4.** No item from §3 has drifted into the lead's scope.
- **S5.** At most one joint-decision escalation reached the
  director; each escalation was resolved within 10 working days.
- **S6.** The team manager's fluency dashboard (Exercise 03)
  continued on cadence through the handoff.

If any of S1–S6 is missed, review the rollback criteria in §7.

## 7. Rollback criteria

The handoff is paused or reversed, with named steps, if:

- The lead is on extended leave, reorged, or otherwise unavailable
  for more than 20 working days without a named delegate.
- Any §3 retained item drifts to the lead and is not returned
  within 10 working days of flagging.
- The CoP (Exercise 04) triggers its own sunset criteria while
  under the lead's coordination.
- The team's fluency dashboard shows a negative trajectory
  temporally correlated with the handoff.

Rollback mechanics:
1. Team manager resumes ownership of the §2 artifacts named in the
   rollback trigger.
2. Lead and manager co-sign a rollback addendum describing which
   items reverted and why.
3. The director named in §4 is notified within 2 working days.
4. A revised plan is co-drafted within 10 working days.

## 8. Signatures

- Team manager: __________________  Date: ___________
- Org-wide enablement lead: __________________  Date: ___________
- (Optional) Director: __________________  Date: ___________

## Appendix A — Artifact transfer acknowledgements

For each row in §2, the lead confirms they have read the artifact,
understand its cadence, and accept ownership from the transfer
date.

- [ ] Pattern library — acknowledged on <date>
- [ ] Failure-mode digest — acknowledged on <date>
- [ ] Shared evals — acknowledged on <date>
- [ ] Quarterly brief cadence — acknowledged on <date>
- [ ] Tool-budget rubric template — acknowledged on <date>
- [ ] Review-checklist proposal pipeline — acknowledged on <date>
- [ ] Org-level enablement roadmap — acknowledged on <date>
- [ ] CoP stewardship coordination — acknowledged on <date>
- [ ] Vendor liaison — acknowledged on <date>

## Appendix B — Escalation log

| Date | Decision | Team mgr position | Lead position | Resolution | Resolved by (date) |
|---|---|---|---|---|---|

## Appendix C — Change history

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | <date> | <mgr + lead> | Initial handoff |
```

### 2.2 Decision rationale (why these choices and not others)

- **Transfer by *named artifact*, not by *responsibility*.** "The lead now owns enablement" is a slogan, not a transfer. The contract lists nine artifacts, each with a URL, a transfer date, and a required acknowledgement. The lead cannot be surprised by an inherited obligation that isn't in the table.
- **Retained list is as important as transferred list.** The accountability contract, per-IC scores, 1:1 notes, and local budget allocation must stay with the manager. If any of these drift upstream, the team's accountability layer breaks and the manager of record becomes ambiguous — which is the outcome NIST AI RMF **Govern 1.1** is designed to prevent.
- **Pre-written joint-decision rules.** "We'll figure out how to collaborate" is where every handoff dies. The contract pre-decides: team-local → manager prevails; org-shared → lead prevails; both → co-sign with timed escalation. Two-sentence rules beat ten-paragraph principles.
- **Return cadence.** The single most common pathology of org-wide enablement is *extractive*: the lead collects from teams, synthesises, and shares nothing downstream. Section 5 inverts that: monthly pattern additions, five-working-day vendor and policy diffs, quarterly benchmarks. Without this, teams quietly stop feeding the lead.
- **Six concrete success criteria, not "it went well."** S1–S6 are testable at the 90-day review. S4 in particular ("no §3 retained item drifted") is often the one that fails silently.
- **Explicit rollback.** Handoffs fail. The rollback section is not pessimistic — it is operationally kind. It specifies the mechanics so the team doesn't limp along under a lead who's been quietly reorged two months ago.
- **The contract has version, review date, and appendices.** A contract without version numbers becomes "some Google Doc from last year." The version number and review date make the contract a *living* document. Appendices make the acknowledgements and escalation history auditable without inflating the main body.
- **Alignment with CoP and governance.** Section 2's "CoP stewardship coordination across teams" is the natural upstream of the CoP's quarterly brief (Exercise 04). Section 3's retained items align with the team manager's accountability contract (mod-103 ex-07) and the team's per-IC privacy commitments (Exercise 03). Section 5's policy diffs come from the governance owner (mod-110). The handoff contract sits at the junction of these, not inside any one of them.

## 3. Validation steps

1. **Walk the artifact-transfer table against your own wiki.** Every row needs a real URL. If any row says "to be determined," the handoff is not ready — the manager has not yet produced that artifact.
2. **Read §3 to a privacy or HR partner.** If they flag any retained item as actually belonging org-wide (e.g., per-team budget totals), tighten the item's wording to include only what must remain local.
3. **Dry-run a joint decision.** Pick a hypothetical: the lead wants to standardise the review checklist across teams, but your team's checklist has two local items. Walk through §4: does the contract produce a clean decision within 5 working days? If not, the rules are too soft.
4. **Dry-run the return cadence.** Imagine 30 days post-signing. What exactly has the lead sent back? If the answer is "nothing specific," the cadence items in §5 are under-specified.
5. **Dry-run the rollback.** Imagine the lead is reorged on day 60. Walk through §7: who resumes what, when, and how is the director notified? If any step is vague, tighten it.
6. **Peer-review with one director and one adjacent team manager.** The director catches org-level dependencies (reporting lines, budget authority) the manager misses. The adjacent manager catches cross-team edge cases (e.g., shared ICs on matrixed teams).
7. **Sign only when §2's appendix A is filled out.** A contract signed without acknowledgements is a contract whose transfer never actually happened. The appendix is the transfer, not the signature.

Validation is behavioural: you are checking that the contract survives contact with the first disagreement, the first reorg, and the first "please can we have the data" request.

## 4. Rubric

Use this to grade a submitted handoff contract (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Scope framing** | Vague "we're handing over enablement" | Scope named generically | Scope named with inside / outside | Scope named + explicit non-coverage of team-local work |
| R2 | **Transfer table (§2)** | Narrative only | Items listed | Items + URLs | Items + URLs + transfer date + required acknowledgement |
| R3 | **Retained items (§3)** | Absent | 1–2 items | 4+ items with reasons | 6 items covering accountability, per-IC data, local budget, 1:1 notes, local policy, plan |
| R4 | **Joint-decision rules (§4)** | "We'll collaborate" | Mentions escalation | 3 cases + 5-day escalation | 3 cases + escalation path + named director + vendor/procurement co-sign |
| R5 | **Return cadence (§5)** | Absent | "The lead will share" | 2–3 named returns | 5 returns with cadence + format |
| R6 | **Success criteria (§6)** | "It went well" | 1–2 vague criteria | 4+ testable criteria | 6 testable criteria including no-drift (S4) and dashboard-continuity (S6) |
| R7 | **Rollback (§7)** | Absent | "We can revisit" | Triggers listed | Triggers + mechanics + 2-working-day notification rule + revised plan window |
| R8 | **Version / review discipline** | Undated prose | Dated once | Versioned | Versioned + review date + change history (appendix C) |
| R9 | **Acknowledgement discipline** | None | Signatures only | Appendix A exists | Appendix A filled with per-item acknowledgement dates before sign-off |
| R10 | **Links to other modules** | Absent | One cross-ref | 2–3 cross-refs | Cross-refs to Exercise 01 plan, 02 budget, 03 measurement, 04 CoP, mod-108/109/110/111 ownership boundaries |

**Pass bar: 20/30.** Below that, iterate before signing.

## 5. Common mistakes

- **Transferring responsibility in principle without transferring artifacts.** The lead "owns enablement now" — but the pattern library is still on the manager's laptop, the brief cadence is still unpublished, and the lead learns their obligations by Slack ping.
- **Letting retained items drift.** The most insidious failure mode. Per-IC fluency scores get aggregated for an "org dashboard" and suddenly privacy is broken. The 1:1 notes get requested for an "org coaching program." Hold the §3 line explicitly.
- **No joint-decision rules.** Disagreements become standoffs. The manager and lead slow-roll each other's requests until a director intervenes anyway. Pre-written rules are cheap.
- **Extractive handoff.** The lead collects and never returns. Within one quarter, teams quietly stop responding. Section 5 is the antidote.
- **Fuzzy success criteria.** "Handoff was successful" without S1–S6 is unfalsifiable. The 90-day review has nothing to assess.
- **No rollback.** The lead goes on leave, nobody owns the pattern library, the CoP quietly dies. The rollback section is not pessimistic — it is operationally kind.
- **Signing without appendix A.** The signature is on the contract, but no artifact has been formally acknowledged. The handoff didn't happen; a document was produced.
- **Treating the contract as a one-time email.** Without a version number and review date, the contract decays. Six months later nobody can find the current version.
- **Merging the contract with the org-wide program charter.** The handoff is a *bilateral* contract between manager and lead. The org-wide program charter is a different document. Confusing them bloats both.
- **Not notifying the mod-111 vendor owner.** Vendor liaison in §2 overlaps with vendor ownership; without an explicit mod-111 co-sign, the vendor owner is blindsided the first time a tool question routes through the lead.
- **No cross-team visibility.** If three teams each hand off to the same lead using different contract formats, the lead has an impossible job. The contract template should be shared across sibling teams, not re-invented per manager.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Govern** function (specifically Govern 1.1 named accountable personnel, Govern 1.2 roles and responsibilities, and Govern 2.1 risk-management processes documented at appropriate scope) is the primary source for the retained-items design (§3) and the acknowledgement discipline (appendix A).
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the privacy / data-handling framing for §3 retained items (per-IC fluency scores and 1:1 notes) draws on the security-exposure categories in the ML Top 10.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy used by the org-wide lead when consolidating cross-team failure-mode digests; relevant to §5 return cadence (benchmark comparison and policy updates).

<!-- follow-up: after one full handoff cycle, replace the 90-day review window with the empirical time-to-stable observed on this org's handoffs. Until then, 90 days is a reasonable default; it is not a cited figure. -->
