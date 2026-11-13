# Exercise 04 — Routing Contract With Risk Engineer And SecOps

> Module: `mod-109-accountability-incident-response-and-blameless-postmortems`
> Exercise type: **design** (RACI + routing contract document + grading rubric)

## 1. Solution overview

The deliverable is a **written routing contract** between the team and
its cross-functional partners — Risk Engineering, SecOps, Privacy,
Legal, and Comms — that answers *who owns what during an AI incident,
before the incident starts.*

A good routing contract is:

- **Specific about triggers, not vibes.** The handoff from team to
  Risk Engineering, and from Risk Engineering to SecOps, is keyed to
  signals the team runbook (exercise-01) already lists. If it is keyed
  to "it feels serious," it is not a contract.
- **Signed by the counterparties.** The team manager, Risk-Eng lead,
  and SecOps lead each co-sign a dated version. "We talked about it
  once" is not a contract.
- **Includes response-SLA.** How fast does the partner answer the
  page? What is the escalation path if they do not? Without SLAs the
  contract is a wish.
- **Reversible and reviewed.** The contract has a review cadence
  and a known-gaps section. The incident landscape changes; contracts
  that do not change with it decay into fiction.

Design decisions this solution encodes:

1. **Contracts remove in-incident negotiation.** The expensive failure
   mode is not "SecOps declined to help"; it is "the team spent 15
   minutes figuring out whether to page SecOps." Writing the contract
   in advance converts that negotiation into a table lookup.
2. **The contract is the inverse of the runbook paging tree.** The
   runbook (exercise-01 §2) names who to page, when. The contract
   names what the paged party owes in return. Both must match.
3. **Scope boundaries are explicit.** "SecOps does model security" is
   not a boundary. "SecOps owns credential compromise, prompt-injection
   attack-surface analysis, and model-exfiltration investigation;
   SecOps does *not* own model-quality regressions" is a boundary.
4. **Cross-team ownership cannot be ambiguous during an incident.**
   Every trigger belongs to one role primarily (Accountable), with a
   named supporter (Consulted). Multi-accountable triggers in a
   RACI are an unresolved negotiation that will be re-opened at 3 a.m.
5. **Blame-free contract, not blame-free scope.** The contract is
   blame-free about mistakes made during the incident. It is not
   blame-free about scope: if a partner repeatedly fails SLA, that is
   a contract-review conversation, not a postmortem agenda item.

## 2. Implementation — the routing contract

### 2.1 Contract document

```markdown
# Routing Contract — Copilot Team ↔ Risk Engineering ↔ SecOps ↔ Privacy ↔ Legal ↔ Comms

**Signatories:**
- Copilot Team Manager: <name>
- Risk Engineering Lead: <name>
- SecOps Lead: <name>
- Privacy Lead: <name>
- Legal Lead: <name>
- Comms Lead: <name>

**Effective:** 2026-11-12
**Review cadence:** quarterly; mandatory review after any SEV1 that
touched this contract.
**Review date:** 2027-02-12

## 1. Shared vocabulary

This contract references:

- Severity definitions from the Copilot team **Incident Runbook**
  (exercise-01 §1). Parties agree severity declared by the team's IC
  is binding; disputes go to the IC during the incident and to this
  contract's quarterly review afterward.
- Shared-taxonomy labels from **OWASP ML Top 10**, **MITRE ATLAS**,
  and **NIST AI RMF**. Trigger labels in §3 below cite specific IDs.

## 2. Roles (RACI definitions, scoped)

- **R** (Responsible): does the work.
- **A** (Accountable): one role per row; the "the-buck-stops-here" owner.
- **C** (Consulted): must be looped in; has opinions and evidence.
- **I** (Informed): must be notified; does not have to respond.

If a row below shows more than one **A**, the contract is broken;
raise it at review.

## 3. Trigger → routing table

Every trigger in the Copilot runbook maps to exactly one routing row.

| Trigger (shared-taxonomy ID) | Severity floor | R | A | C | I | Response SLA |
|---|---|---|---|---|---|---|
| **Prompt injection extracting system prompt or tool credentials** (OWASP ML09; MITRE ATLAS AML.T0051) | SEV2 | Copilot IC | **Copilot Team** | SecOps, Risk Eng | Privacy (if credentials grant external action) | SecOps on-call: 10 min to acknowledge page |
| **Tool call executed on a customer's behalf without consent** (MITRE ATLAS AML.T0053) | SEV1 | Copilot IC + SecOps | **Copilot Team** | SecOps, Privacy, Risk Eng | Legal, Comms | SecOps on-call: 5 min; Privacy: 15 min |
| **Training/fine-tuning data leaked in model output** (OWASP ML02; NIST AI RMF Measure 2.10) | SEV1 | Copilot IC + Privacy | **Privacy** (data-exposure accountability) | Copilot Team (mechanism), Risk Eng | Legal, Comms, SecOps | Privacy on-call: 10 min; Legal: 30 min |
| **Retrieval returning cross-tenant data** (OWASP ML06; NIST AI RMF Measure 2.10) | SEV1 | Copilot IC | **Copilot Team** (mechanism owner), **Privacy** (data-exposure) — co-A permitted only here, see §4 | Risk Eng, SecOps | Legal, Comms | Privacy on-call: 10 min; SecOps: 15 min |
| **Safety-eval regression > 20% post-deploy** (NIST AI RMF Measure 2.7) | SEV2 | Copilot team on-call | **Copilot Team** | Risk Eng | Comms (only if customer-visible) | Risk Eng on-call: 30 min |
| **Model-training data poisoning detected in incoming feed** (OWASP ML02; MITRE ATLAS AML.T0020) | SEV2 | SecOps + Copilot IC | **SecOps** (threat ownership), Copilot Team (model impact) — co-A permitted only here, see §4 | Risk Eng, Privacy | Legal | SecOps on-call: 15 min |
| **Model output harassment / disallowed content customer-facing** (NIST AI RMF Measure 2.11) | SEV2 | Copilot team on-call | **Copilot Team** | Comms, Risk Eng | Privacy, Legal | Comms on-call: 30 min |
| **Hallucinated reference acted on by user, widespread** (NIST AI RMF Measure 2.9) | SEV2 | Copilot team on-call | **Copilot Team** | Risk Eng | Comms | Risk Eng on-call: 30 min |
| **Novel failure mode not in this table** | declared by IC | Copilot IC | **Copilot Team** (interim) | Risk Eng | parties the IC judges relevant | Risk Eng on-call: 30 min; contract-review row is created automatically |

## 4. Co-accountability exceptions

Two rows above have co-**A**: *cross-tenant retrieval leakage* and
*training-data poisoning*. These are the only exceptions; co-**A**
is otherwise forbidden. In both exception cases the contract says
*who leads the external communications* (right-hand A) and *who leads
the mechanism remediation* (left-hand A). If the IC cannot tell those
apart, they default to the left-hand A.

## 5. Response-SLA definitions

- **Acknowledge** means the paged role has joined the incident channel
  and indicated presence. It does not mean a decision has been made.
- **Respond** means the paged role has made the first action owed
  (containment review, legal determination, Comms draft, etc.).
- **Escalate** means the paged role's lead gets paged if the SLA is
  missed. The escalation path is specified per role in §6.

If the response SLA is not met and escalation also fails, the IC is
authorised to take the most conservative action in that lane
unilaterally (e.g., default to not communicating externally if Comms
is unreachable; default to disabling the suspected surface if SecOps
is unreachable).

## 6. Escalation chains (per role)

| Role | L1 | L2 | L3 |
|---|---|---|---|
| Risk Engineering | Risk-Eng on-call | Risk-Eng lead | Director of AI |
| SecOps | SecOps on-call | SecOps duty officer | Director of Security |
| Privacy | Privacy on-call | DPO | General Counsel |
| Legal | Legal on-call | GC on-call | GC |
| Comms | Comms on-call | Head of Comms | CEO / COO (per crisis plan) |

L2 is paged automatically by the IC if L1 misses SLA. L3 is paged
by the Team Manager (or L2) in SEV1 extending past 60 minutes.

## 7. In-incident communication norms

- **Incident channel is primary.** Decisions in DM do not exist. If a
  partner cannot speak in the incident channel (sensitive legal /
  privacy), they state that explicitly and the decision is summarised
  into the channel by the IC afterwards.
- **No external replies without IC approval.** Any customer-facing
  comms (including support-agent responses) require IC sign-off.
- **Partner may veto mitigations in their lane.** Example: SecOps may
  veto a credential-rotation action that would compromise an active
  investigation. Veto is stated in channel with reason.
- **No blame talk in the channel.** Blame talk goes nowhere. If a
  decision looks wrong, name the decision, not the person.

## 8. Post-incident contract review

For every SEV1 or SEV2 incident, the postmortem (exercise-02) answers
three contract-specific questions in §8 (Action items):

1. Did the trigger match a row in this contract's §3? If no, add a
   row.
2. Did every SLA in §3 hold? If no, name the miss and its cause (not
   the person), and open a contract-review action item.
3. Did any co-**A** exception resolve cleanly? If the resolution
   required a conversation, document it as a precedent in §4 of the
   contract.

## 9. Known gaps

- No SLA defined yet for **Comms** on weekends outside crisis-plan
  window. Owner: Comms Lead; target 2026-12-15.
- Privacy on-call rota is 9-5 regional; night SEV1 paths rely on DPO
  cell phone. Owner: Privacy Lead; target 2027-01-31.
- No contractual scope with **Model Provider** (external vendor); if
  the mechanism is upstream of our team, we fall back to vendor support
  SLAs that are weaker than this contract's. Flagged to
  mod-111 vendor-contract exercise.

## 10. Change log

- 2026-11-12 (initial version) — signatories as above.
```

### 2.2 Why the contract looks like this

- **One Accountable per row (with two signed exceptions).** The most
  common reason for lost minutes during an AI incident is two parties
  each believing the other owns the decision. Single-A enforces an
  owner by construction. The two co-A rows exist because
  cross-tenant leakage and training-data poisoning genuinely have two
  distinct accountabilities (mechanism vs. external exposure); the
  contract pays the complexity cost there and nowhere else.
- **Triggers keyed to taxonomy IDs.** OWASP ML / MITRE ATLAS / NIST AI
  RMF labels on every row make the contract machine-readable against
  the runbook and the postmortem template. If the runbook adds a
  trigger, the contract adds a row; both reference the same ID.
- **SLAs in minutes, not adjectives.** "Timely" is not an SLA. The
  minutes in §3 are meant to be contested and revised; what they are
  not meant to do is remain absent.
- **Partner veto is written in.** SecOps can veto a mitigation that
  would harm an investigation; Privacy can veto a comms action that
  would expose data. Writing the veto down prevents the veto from
  becoming an in-channel power struggle.
- **Novel-mechanism row.** Every real incident landscape evolves faster
  than the contract. The "not in this table" row routes to the team as
  interim A with Risk Eng as C, and auto-creates a contract-review
  item. The contract therefore doesn't fall off a cliff when the next
  attack class arrives; it degrades gracefully.
- **Fallback authority for SLA miss.** If a partner is unreachable, the
  IC has written permission to default to the conservative action.
  Without it, outages extend by hours while someone tries to reach
  someone.

## 3. Validation steps

To validate a routing contract before signing:

1. **Signatory check.** The named leads of each partner team have read
   it and agreed in writing. If any signature is missing, the contract
   is draft, not operative.
2. **Trigger-coverage check.** Every trigger in the runbook (exercise-
   01 §1) corresponds to a row in §3 of the contract. Diff the two
   lists; resolve to zero.
3. **Taxonomy-ID coverage.** Every row cites specific OWASP / ATLAS /
   RMF IDs, not family names.
4. **Single-A check.** Grep §3 for rows with more than one bold role.
   The only hits should be the explicit co-A rows named in §4.
5. **SLA realism.** Each named SLA has a paging target the paged role
   has acknowledged they can meet. SLAs copied from an org-wide
   template without that acknowledgment are theatre.
6. **Escalation-aliveness check.** L1 → L2 → L3 chains in §6 have been
   tested; names are current.
7. **Comms veto / Privacy veto language present.** §7 names the
   partner-veto norm explicitly.
8. **Known-gaps section non-empty.** A first-pass contract with no
   known gaps is a contract that has not been read honestly.

## 4. Rubric

Use this to grade a routing contract. Each criterion scores 0–3. Pass
bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Signatories and review cadence** | Absent | Named | Named + cadence | Named + cadence + mandatory-review-after-SEV1 clause |
| R2 | **Trigger table coverage** | Doesn't match runbook | Partial | Matches runbook 1:1 | Matches runbook + taxonomy IDs + default severity |
| R3 | **Single-A discipline** | Many rows multi-A | Multi-A unexplained | Single-A except named exceptions | Single-A + signed co-A exceptions + mechanism/comms split articulated |
| R4 | **Response SLAs** | Absent or vague | Minutes named | Minutes + escalation chain | Minutes + escalation + fallback-authority clause for IC |
| R5 | **Partner veto rules** | Absent | Mentioned | Named by role | Named + stated-with-reason requirement in channel |
| R6 | **In-channel decision norm** | Absent | Mentioned | Channel-primary + no-DM-decisions | Channel-primary + no-blame-talk + partner-veto surfacing norm |
| R7 | **Novel-mechanism row** | Absent | Named | Named + routes to team interim | Named + routes to team interim + auto-contract-review |
| R8 | **Known gaps** | Absent | Listed | Listed + owners | Listed + owners + dates + review date |
| R9 | **Post-incident review hook** | Absent | Mentioned | 3 questions listed | 3 questions listed + enforced via postmortem template §8 |

## 5. Common mistakes

- **Whole-table co-A.** Writing both teams as Accountable on every row
  because "it's cross-functional." The contract then does nothing it
  was designed to do; the in-incident negotiation returns.
- **Severity-by-partner.** Letting SecOps declare severity for
  injection triggers while letting the team declare severity for
  quality triggers. Severity is one function (the IC's); split it and
  the postmortem becomes an argument about which severity to grade
  the response against.
- **"Timely" SLAs.** Adjectives are not response contracts. Minutes
  are. If minutes are hard to commit to, the contract should pick a
  rough pair (e.g., 5 / 15 / 30) and refine at review.
- **No fallback authority.** If the contract does not say what the IC
  does when a partner is unreachable, the IC defaults to "wait for
  partner," which extends the harm window. Write the fallback.
- **Family-name taxonomy.** "OWASP ML" on the row is not a label; it
  is a pointer. Use ML09, ML02, ML06 — specific IDs that aggregate.
- **No review cadence.** The partner landscape (headcount, on-call
  rotas, scope) changes. A contract without a review cadence silently
  decays to fiction within six months.
- **Contract divorced from the runbook.** If the trigger list in §3
  does not match the trigger list in the runbook, one of them is
  wrong and the incident will discover which at the worst time.
- **Hidden co-A.** Listing two **R**s and one **A** on a row where the
  team has conceptually co-A behaviour — the hiding makes the
  contract technically single-A but operationally broken. Name the
  co-A if it exists; do not launder it through R.
- **No contract-review action from postmortems.** If the contract is
  not interrogated by every SEV1, it will not improve. §8 of the
  contract and §8 of the postmortem template (exercise-02) are
  mutually load-bearing.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function's accountability expectations (documented
  roles, responsibilities, lines of communication between AI
  developers, operators, and third parties) are the structural reason
  the contract exists at all; this exercise is the team-scoped
  implementation of that expectation.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  trigger-row labels in §3 cite ML-series IDs for
  injection, poisoning, leakage, and supply-chain rows.
- [MITRE ATLAS](https://atlas.mitre.org/) — technique IDs
  (AML.T0020 poison training data, AML.T0051 prompt injection,
  AML.T0053 plugin compromise, etc.) used to label SecOps-routed
  rows.

## 7. Open follow-up

Once the model-provider vendor contract is in scope (mod-111), add
a §11 "Upstream vendor fallback" section with the vendor's own
incident-response SLA and the gap between it and this contract.
Flagged as a known gap in §9.
