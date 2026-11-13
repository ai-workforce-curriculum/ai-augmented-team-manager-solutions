# Exercise 01 — Copilot Build-vs-Buy Matrix

> Module: `mod-111-vendor-budget-and-tool-selection-at-team-scope`
> Exercise type: **design** (worked decision matrix + rationale + grading rubric)

## 1. Solution overview

The deliverable is a one-page **build-vs-buy decision matrix** that a team
manager can take to their next planning meeting and say: *"for this team, in
this quarter, this is the copilot we are standardising on, these are the
criteria that made us pick it, and this is the review date at which we will
re-open the decision."* It is the artifact that stops the "which copilot
should we use?" question from being re-litigated in Slack every two weeks.

"Copilot" in this exercise is the broad category: the AI-assisted coding
surface an engineer uses day-to-day — IDE autocomplete, in-editor chat, a
terminal agent, or a full agentic coding host. Named examples the matrix
should be able to score include GitHub Copilot, Cursor, Claude Code, Codeium
/ Windsurf, Sourcegraph Cody, and the open-source stack (Continue, Aider,
aichat, custom MCP clients) you might self-host.

A good matrix is:

- **Weighted by what the team actually cares about**, not a generic feature
  grid. Different weights per team are the point, not a bug.
- **Scored against the team's own constraints** — the regulated-data team's
  weights and the stealth-startup's weights produce different winners, and
  both answers are correct for their context.
- **Explicit about the "buy" axis.** There is no single "buy" — there is
  "vendor SaaS with their inference," "vendor product with bring-your-own
  model key (BYOK)," and "vendor product routed through our own LLM
  gateway." These three options have very different risk and cost profiles.
- **Explicit about the "build" axis.** "Build" is almost never greenfield;
  it is almost always *integrate an open-source editor extension or agent
  harness against an internal model gateway.* A matrix that treats "build"
  as "write Copilot from scratch" is dishonest about the real cost.
- **Mapped to a reviewable artifact** — an ADR committed to the repo, with
  an owner, a cost, a review date, and a named kill-switch.

Design decisions this solution encodes:

1. **Hybrid is a first-class answer.** A large fraction of real teams land on
   "vendor IDE copilot for the editor experience + internal gateway for
   governance, metering, and higher-risk flows." A matrix that forces the
   team to pick exactly one row is forcing them into a false binary.
2. **The matrix evaluates the stack, not the brand.** "Copilot" and "Cursor"
   are not single things — each is a *client* plus a *model routing layer*
   plus a *tool authorisation surface*. Scoring must separate those.
3. **Data handling is a kill-switch dimension, not a weighted criterion.**
   If a vendor cannot contractually meet the team's data posture (training
   opt-out, zero-retention tier, region residency where required), the row
   is disqualified regardless of how well it scores elsewhere. Do not let a
   pretty total score launder that. This links directly to
   exercise-04 (due diligence) and exercise-05 (DPA / SOC 2 review).
4. **Lock-in is counted per switching path.** A vendor that supports BYOK
   and open prompts is cheaper to leave than one that bundles its own model
   and proprietary prompt templates. The matrix prices the *cost to leave*
   as a first-class row.
5. **The matrix produces a dated decision, not a recommendation to someone
   else.** The team manager is the person who signs the invoice or merges
   the integration PR; the matrix output is an ADR they own.
6. **The decision is reviewable on a cadence tied to vendor pace.** This
   market moves faster than most procurement cycles. A review date of "when
   the contract renews" is too slow; 90 days is more honest.

## 2. Implementation — the worked matrix and ADR

### 2.1 Decision options (the rows)

Every row is a *stack*, not a brand. Teams sometimes conflate "Cursor" with
"the model that happens to be behind Cursor today"; the matrix scores them
apart.

| Row | Client | Inference / routing | Tool surface | Example |
|---|---|---|---|---|
| **B1 — Vendor SaaS, vendor inference** | Vendor IDE / plugin (GitHub Copilot, Cursor, Codeium) | Vendor-hosted model(s); vendor chooses | Vendor-controlled; limited MCP / plugin ecosystem | GitHub Copilot on the vendor's default |
| **B2 — Vendor SaaS, BYOK** | Same vendor IDE | Model provider's API with the team's key (Anthropic, OpenAI, Google) | Vendor-controlled | Cursor / Continue with team key |
| **B3 — Vendor SaaS routed through internal gateway** | Same vendor IDE | Internal LLM gateway in front of the model provider(s) | Vendor-controlled; gateway logs / meters | Cursor / Claude Code pointed at `llm-gateway.corp` |
| **H1 — Hybrid: vendor client + internal MCP** | Vendor IDE | Vendor or BYOK | Team-published MCP servers for internal tools; vendor tools for editor-local | Cursor + internal `jira-mcp`, `db-readonly-mcp` |
| **BLD1 — Open-source client, internal gateway** | OSS editor extension (Continue) or OSS terminal agent (Aider) | Internal LLM gateway | Internal MCP; team can veto any tool | Continue + `llm-gateway.corp` + internal MCP |
| **BLD2 — Fully custom** | Team-built UI or extension | Internal gateway | Internal, bespoke | You are probably not actually in this row — see §2.6 |

### 2.2 Criteria (the columns)

Criteria are grouped so that the "kill-switch" group is scored first and
applied before totals. A row that fails any kill-switch criterion is
disqualified and does not get a total score.

**Kill-switch group (pass / fail).** Fail any one and the row is out.

| ID | Kill-switch criterion | Pass means |
|---|---|---|
| K1 | **Training-data opt-out** | Vendor contractually commits that prompts, completions, and code context are not used to train models, for all tenants on the tier we are buying. |
| K2 | **Retention tier available** | A zero- or short-retention tier is contractually offered and we are on it; retention window is documented. |
| K3 | **Data residency where required** | If the team's data has a residency constraint, the vendor contractually processes within the allowed region(s). |
| K4 | **Named sub-processors disclosed** | Sub-processor list is published and change-notified. |
| K5 | **Breach notification obligation** | Vendor commits to a specific breach-notification SLA (hours, not days). |
| K6 | **Deprovisioning is not destructive** | We can offboard and delete tenant data on exit without losing operational logs we need for incident response. |

**Weighted group.** Score 0–3 per cell. Assign a weight per column before
scoring; defaults are illustrative and should be re-weighted per team.

| ID | Criterion | Default weight | What 3 looks like | What 0 looks like |
|---|---|---|---|---|
| W1 | **Model quality for our stack** | 15 | Clear lead on the languages / frameworks our team uses most (measured in §3). | Visibly worse on our languages than baseline free tools. |
| W2 | **Model-switch optionality** | 10 | BYOK or internal-gateway routable; can switch model providers in a day. | Bundled model only; switching requires leaving the product. |
| W3 | **Tool / MCP extensibility** | 10 | Open plugin / MCP surface; team can publish and *revoke* tools. | Closed; team cannot restrict what the agent can call. |
| W4 | **Governance hooks** | 10 | Admin console for seat allocation, per-seat usage, per-tool authorisation; audit log export. | No admin surface; usage is per-user credit card. |
| W5 | **Cost predictability** | 10 | Flat per-seat or metered with hard caps the team controls. | Metered with no hard cap; one bad agentic loop can run up a bill. |
| W6 | **HITL surface** | 8 | Per-tool-call confirmation gate exposed to the client; policy configurable per workspace. | No gate; agent acts unilaterally. |
| W7 | **Incident response support** | 7 | Named security contact, documented runbook for vendor-side incidents, status page history is credible. | Email-only support, no runbook, status page is marketing. |
| W8 | **Cost to leave** | 8 | Prompts, settings, and history exportable; OSS-compatible config; no proprietary prompt template lock-in. | Proprietary prompts; exporting history means screen-scraping. |
| W9 | **Supply-chain posture** | 7 | SBOM published; known-vulnerability disclosure history; signed releases. | No SBOM; closed-source extension that touches the filesystem. |
| W10 | **Interop with our dev stack** | 5 | First-class on our editors (VS Code, JetBrains, Neovim) and CI; MCP works in our CI image. | One editor only; nothing in CI. |
| W11 | **Review / rollback posture** | 5 | Changes appear as reviewable diffs, not silent edits; undo is a first-class command. | Edits apply without a diff view; no bounded undo. |
| W12 | **Team enablement cost** | 5 | Low: onboarding is minutes; docs cover the team's workflow. | High: team needs a week of enablement per IC. |

Max weighted score: `3 × (15+10+10+10+10+8+7+8+7+5+5+5) = 3 × 100 = 300`.

### 2.3 The scoring protocol

1. **Weight before you score.** Fill the "weight" column against *this team's*
   constraints before looking at any product. Two minutes with the team is
   enough. If you cannot name why a weight is what it is in one sentence,
   the weight is wrong.
2. **Run the kill-switch pass first.** Score K1–K6 per row from the DPA
   (exercise-05) and the due-diligence packet (exercise-04). Rows that fail
   are struck through, not scored.
3. **Score the surviving rows 0–3** on each weighted column against
   *evidence*, not vibes. Each cell gets a one-line citation: a doc link, a
   contract clause, a reproducible benchmark, a logged trial.
4. **Note which weights the result is sensitive to.** If swapping W1 from
   15 to 10 flips the winner, say so. A result that is sensitive to small
   weight changes is a result the team is not yet confident in.
5. **Pick the winner, write the ADR.** The ADR names: the row chosen, the
   top two reasons, the kill-switch rows struck, the review date, the named
   human owner, and the kill-switch conditions that would end the deal
   mid-contract.

### 2.4 Worked example — "Platform API team" (fictional)

Team context:
- 4 ICs, internal infrastructure team. Code is Go + TypeScript.
- Data posture: team handles customer-adjacent data (support logs) but no
  payment or health data. Training opt-out is required by the data-and-IP
  handling policy (see mod-110/exercise-04).
- Cost envelope: ~$500 / month / IC envelope, with a hard stop at $750 / IC.
- Governance: the team manager must report seat allocation and per-IC
  usage to their skip quarterly (see mod-110/exercise-01).

Weights (the team changed three defaults):
- `W4 Governance hooks: 10 → 14` (reporting obligation is real).
- `W5 Cost predictability: 10 → 14` (hard stop at $750).
- `W2 Model-switch optionality: 10 → 12` (the team wants to run a 30-day
  A/B on provider quality next quarter).
- All others at defaults. Total weight: 110.

Kill-switch pass (illustrative; real values must come from the signed DPA):

| Row | K1 | K2 | K3 | K4 | K5 | K6 | Survives? |
|---|---|---|---|---|---|---|---|
| B1 Vendor SaaS, vendor inference | ✅ (on business tier) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| B2 Vendor SaaS, BYOK | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| B3 Vendor SaaS via internal gateway | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| H1 Hybrid (vendor client + internal MCP) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| BLD1 OSS client + internal gateway | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| BLD2 Fully custom | n/a | n/a | n/a | n/a | n/a | n/a | out — see §2.6 |

Weighted scoring (illustrative; replace with real benchmarks and evidence):

| Criterion | W | B1 | B2 | B3 | H1 | BLD1 |
|---|---|---|---|---|---|---|
| W1 Model quality for our stack | 15 | 3 | 3 | 3 | 3 | 2 |
| W2 Model-switch optionality | 12 | 0 | 2 | 3 | 3 | 3 |
| W3 Tool / MCP extensibility | 10 | 1 | 2 | 2 | 3 | 3 |
| W4 Governance hooks | 14 | 1 | 2 | 3 | 3 | 3 |
| W5 Cost predictability | 14 | 2 | 1 | 3 | 3 | 3 |
| W6 HITL surface | 8 | 1 | 2 | 2 | 3 | 3 |
| W7 Incident response support | 7 | 3 | 3 | 3 | 3 | 1 |
| W8 Cost to leave | 8 | 1 | 2 | 2 | 3 | 3 |
| W9 Supply-chain posture | 7 | 2 | 2 | 2 | 2 | 2 |
| W10 Interop with our stack | 5 | 3 | 3 | 3 | 3 | 2 |
| W11 Review / rollback posture | 5 | 2 | 2 | 2 | 2 | 2 |
| W12 Team enablement cost | 5 | 3 | 3 | 2 | 2 | 1 |
| **Total (W × score)** | | **175** | **220** | **262** | **299** | **263** |

Sensitivity check:
- If `W5 Cost predictability` is reverted to the default 10, H1 → 283 and
  BLD1 → 251; H1 still wins. Not sensitive.
- If `W2 Model-switch optionality` is reverted to 10, H1 → 293 and B3 → 256;
  H1 still wins. Not sensitive.
- If `W4 Governance hooks` is reverted to 10, H1 → 283 and BLD1 → 251; H1
  still wins. Result is robust under these perturbations.

Decision: **H1 — vendor IDE client + internal MCP servers, BYOK into
internal LLM gateway.** This gives the team the vendor client's model
quality on their stack, combined with gateway metering and the ability to
revoke any MCP tool from one central place.

### 2.5 The ADR (what gets committed)

```markdown
# ADR-2026-11-01 — Team standard copilot stack

**Status:** Accepted, 2026-11-13.
**Owner:** <team manager>.
**Review date:** 2027-02-13 (90 days). Off-cycle review triggered by: any
vendor-side incident; any change to the DPA; any model deprecation that
affects our key routes; any IC bringing a receipt that a new option is
materially better on W1 or W4.

## Decision

The team's standard copilot stack is **H1**: vendor IDE client in the
editor + internal MCP servers for team tools, routed to model providers
through the internal LLM gateway using BYOK.

## Context

The team made this decision using the Mod-111 Ex-01 build-vs-buy matrix.
Weighted scores, with the team's weights, placed H1 first (299 / 330).
The result is robust to three sensitivity checks. The matrix,
including scores and citations, is in `docs/copilot-matrix.md`.

## Consequences

- We buy vendor IDE client seats at the business tier (training opt-out,
  zero-retention, SOC 2 Type II). Seat count: 4 ICs + 1 manager. Monthly
  cost envelope $X; hard cap on gateway spend $Y per IC per month, set
  on the gateway.
- Internal MCP servers published for: `jira`, `db-readonly`,
  `incident-logs`, `docs-search`. Authorisation policy per Mod-106 Ex-03.
- Model provider accounts (BYOK): one per provider under evaluation.
- We do **not** standardise the model itself in this ADR. The gateway
  routes per workspace; the "which model" decision is reviewed monthly.

## Kill-switch conditions

This decision is reversed, not re-scored, if any of the following occur:
1. The vendor materially weakens K1 (training opt-out) or K2 (retention)
   in a DPA update.
2. A vendor-side incident exposes team code to another tenant, or to the
   vendor's training pipeline.
3. The vendor removes BYOK / internal-gateway routing on the tier we are
   on.
4. Gateway spend on any single provider exceeds the monthly cap twice
   in a rolling quarter without a sanctioned project cause.

## Alternatives considered

- **B1 (vendor SaaS, vendor inference).** Rejected on W2 and W4. Score 175.
- **B2 (vendor SaaS, BYOK).** Rejected on W4 (governance). Score 220.
- **B3 (vendor SaaS via internal gateway).** Rejected on W3 (MCP
  extensibility only partial). Score 262. Close second; may be revisited.
- **BLD1 (OSS client + gateway).** Rejected on W1 and W12 at this time.
  Score 263. Revisit if vendor client terms change materially.
- **BLD2 (fully custom).** Rejected on effort and incident-response
  posture. See §2.6 of the matrix doc.

## References

- `docs/copilot-matrix.md` — full scoring with citations.
- Mod-111 Ex-04 — vendor due diligence packet.
- Mod-111 Ex-05 — DPA / SOC 2 posture review.
- Mod-110 Ex-04 — data and IP handling policy.
- Mod-106 Ex-03 — MCP tool authorisation policy.
```

### 2.6 Why "fully custom" is almost never the answer

The matrix should not let a team bluff themselves into BLD2 ("build the
whole copilot") without naming the cost. For a team of < ~20 engineers the
cost of operating the client, the context engine, the tool-call plane, the
evals, and the on-call rotation for a copilot that users want to use is
higher than the premium on any vendor seat. BLD2 is a reasonable answer
only when at least one of the following is true:

- The team has a regulatory constraint (air-gapped, specific sovereign
  cloud, specific sector rule) that no vendor satisfies.
- The team **is** the AI product org and the copilot is the product.
- The vendor market has priced the team out of its seat budget and BYOK
  via OSS client (BLD1) does not meet its workflow needs.

Otherwise, BLD1 is the honest "build" answer, and H1 is usually the honest
"hybrid" answer.

### 2.7 Why the matrix is one page, not ten

- A matrix that no-one reads does not drive decisions. One scrolling pane
  matches how a team manager will actually re-read it when the sales rep
  from the losing vendor calls.
- Matrices with 40 criteria are usually a disguise for "we had no
  opinion," and the vendor whose sales deck matched 40 features wins by
  default. 12 weighted criteria + 6 kill-switches is enough.
- The one-page ADR at the end is the artifact that survives the matrix.
  If the ADR is clear, the matrix has done its job; if not, no amount of
  extra criteria will save the decision.

## 3. Validation steps

Before taking the matrix or ADR to the skip or to procurement:

1. **Did the kill-switch pass actually pull from the signed DPA, not the
   marketing page?** Marketing pages promise "we do not train on your data"
   generically; DPAs are where that is contractual. Score K1–K6 against the
   DPA, not against a sales deck. Link exercise-05.
2. **Is every weighted-cell score backed by a one-line citation?** Scores
   without citations are vibes. A cell with "W1 = 3" must link to a doc, a
   benchmark, a dated internal trial, or a vendor's own measurable claim —
   not a tweet.
3. **Have you run the sensitivity check?** Flip each of the top three
   weights by 20% and rescore. If the winner changes, say so in the ADR.
4. **Did you run at least one 1–2 week trial on the top two rows before
   signing the annual contract?** Scoring "model quality for our stack"
   without actually running the thing on the team's codebase is a common
   failure mode.
5. **Does the ADR name a dated review and named kill-switch conditions?**
   An ADR without an expiry is an ADR the org will outgrow by six months.
6. **Is the data posture independently signed off?** The team manager is
   not the owner of K1–K6; legal and security are. Record their names
   against the kill-switch pass.
7. **Does the matrix leave at least one row deliberately un-struck?** If
   every row except the winner was struck for a kill-switch, the team has
   no fallback if the winner's terms change. Keep at least one viable
   fallback alive.

## 4. Rubric

Use this to grade a submitted matrix + ADR (self-assessment or peer
review). Each criterion scores 0–3. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Rows capture the real option space** | One vendor vs. "build it ourselves" only | Includes BYOK somewhere | Separates client / inference / tools | All six rows or a defensible subset with the hybrid row explicit |
| R2 | **Weights are team-specific and justified** | Defaults used, no justification | Defaults used, some justification | Some weights changed with one-sentence rationale | Every changed weight has a one-sentence rationale tied to a team obligation |
| R3 | **Kill-switch pass present and run from DPA** | No kill-switches | Kill-switches listed but scored from marketing | K1–K6 scored from DPA for most rows | K1–K6 scored from the signed DPA for every row, with clause citations |
| R4 | **Cells cite evidence** | Scores are bare numbers | Some cells cite | Most cells cite | Every scored cell has a one-line citation to a doc / benchmark / trial |
| R5 | **Sensitivity check performed** | Not performed | Mentioned | Done on at least one weight | Done on top three weights; result's robustness stated in the ADR |
| R6 | **ADR produced and committed** | None | Prose decision, no ADR format | ADR present without review date or owner | ADR with status / owner / review date / kill-switch conditions / alternatives |
| R7 | **"Cost to leave" counted as a criterion** | Absent | Mentioned informally | Scored but not prominent | W8 weighted, scored with citation, mentioned in the ADR narrative |
| R8 | **Hybrid is honestly considered** | Not considered | Dismissed in one line | Scored but not weighted fairly | Scored on its merits; if rejected, reasons link to specific weighted rows |
| R9 | **Review cadence tied to vendor pace** | No review date | Review at contract renewal | Review at a fixed interval ≥ 6 months | Review at ≤ 90 days + named off-cycle triggers |

## 5. Common mistakes

- **Scoring the brand, not the stack.** "Cursor won last quarter because
  the model behind it was best" is not a durable finding — the model behind
  it changed three times. Score the client, the inference layer, and the
  tool surface separately.
- **Kill-switches scored from marketing pages.** "We don't train on your
  data" on the home page is not a DPA clause. Score K1–K6 from the signed
  contract; otherwise K1–K6 is theatre.
- **Buying for a benchmark the team will not reproduce.** A vendor's
  internal benchmark says their model is best on `HumanEval`. The team
  does not write HumanEval; they write their internal Go services. Score
  W1 against a one-week trial on the team's own codebase, not a public
  leaderboard.
- **Ignoring W8 ("cost to leave").** The team that cannot export its
  prompt history, its tool configs, and its MCP server registrations is
  stuck with the vendor on the first bad renewal. Count W8.
- **Treating "build" as "write a copilot from scratch."** Then naturally
  everything else wins on cost. The honest "build" row is BLD1 (OSS
  client + internal gateway + internal MCP). Score that row fairly.
- **Pretending the hybrid row does not exist.** Many teams quietly end up
  there anyway. The matrix should make this explicit so the team can
  budget and govern it instead of it accreting.
- **No sensitivity check.** The winner that a 1-point weight change
  dethrones is not a winner; it is a near-tie the team is pretending is
  decisive.
- **No review date.** The vendor market has moved three times in the
  calendar year; the team's standard cannot be "whatever we picked 18
  months ago."
- **Stacking the kill-switches at the bottom.** Kill-switches must gate
  the scoring, not be weighted into it. A row that fails K1 must not be
  able to recover through strong scores elsewhere.
- **Confusing "did not fail K*" with "did well on K*."** K1–K6 are
  binary passes. If K1 passes narrowly on a tier-specific carve-out, note
  the carve-out in the ADR; do not score K1 as 3.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Map** function's attention to *context, intended use, and
  dependencies* is the structural reason the matrix is scoped per team
  rather than universal; the **Govern** function's expectations around
  third-party and vendor risk are why K1–K6 are kill-switches rather than
  weights, and why the ADR names an owner and a review date. The **Measure**
  function underwrites the requirement that each cell be scored against
  evidence (a trial, a benchmark, a contract clause), not against vibes.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the supply-chain and data-poisoning risk categories motivate column
  W9 (supply-chain posture). A copilot is a dependency in your software
  supply chain; it deserves the same scrutiny.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy.
  Used downstream of this matrix in the vendor due diligence checklist
  (exercise-04) and the DPA / SOC 2 review (exercise-05) to sanity-check
  that vendor-side risks are being categorised against a shared vocabulary
  rather than invented locally.

<!-- follow-up: once the completeness pipeline has shipped a second cycle of
module solutions, add a worked example of the 90-day re-review of this ADR
so managers can see what evolving a decision looks like without re-opening
the whole matrix from zero. -->
