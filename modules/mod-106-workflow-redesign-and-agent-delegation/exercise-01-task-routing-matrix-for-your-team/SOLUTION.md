# Exercise 01 — Task Routing Matrix for Your Team

> Module: `mod-106-workflow-redesign-and-agent-delegation`
> Exercise type: **design** (worked matrix + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a one-page **task routing matrix**: for every recurring task on the team's backlog, the matrix states (a) whether a human does it, an AI assistant does it, or they pair, (b) who signs off, and (c) what evidence is required to merge or ship. The point is to stop making the route-the-task decision ad hoc on every ticket and instead settle it once per task class.

A good matrix is:

- **Written against the team's real task taxonomy**, not a generic one. "Internal CRUD endpoint" and "auth / authz change" are different task classes even though both show up as "code PR".
- **Short enough to keep in a wiki page and read before standup** — target 8–15 rows. More than that and nobody remembers which row a new ticket belongs to.
- **Risk-tiered on the **effect of the change**, not the file path it touches.**
- **Signed off** — a team manager reviews it, team members acknowledge it, the AI governance owner (if there is one) is CC'd.

Design decisions this solution encodes:

1. **Route by risk × reversibility, not by vibe.** The two dimensions that actually matter are: *how bad is it if the output is wrong?* and *can we cheaply back it out if it is?* Anything high-impact and low-reversibility (schema migrations, prompts shipped to prod, security policy changes) stays human-led regardless of how boring the task looks.
2. **Three routes, not two.** "Human" and "AI" is a false binary — most real tasks are **AI-drafts-human-reviews** or **human-drafts-AI-checks**. The matrix names all three.
3. **The matrix maps each row to an evidence requirement.** "AI did it" is not a disposition; "AI did it, author attested, review checklist ticked, eval suite green" is. The evidence requirements link to the artifacts from Mod-103 (review checklists, ADR template, eval commissioning rubric).
4. **The matrix explicitly lists the "do not delegate" rows.** A positive list of what the team keeps human-only is more useful than a long list of what it lets agents do.
5. **The matrix has a review cadence.** Risks shift quarter over quarter (new tools, new regulations, new incidents). A matrix that doesn't change in a year has either stopped learning or stopped mattering.

## 2. Implementation — worked example of the matrix

### 2.1 Scope statement (printed at the top of the matrix)

```markdown
# Task Routing Matrix — Team <name>
**Owner:** <team manager>
**Last reviewed:** 2026-11-11
**Next review:** 2027-02-11 (quarterly) or sooner if a severity-Medium
or higher AI-related incident is declared on this team.

Scope: recurring task classes on this team's backlog. Covers code,
documents, prompts/model wiring, decisions we automate, and comms we
publish. One-off tasks not on this matrix default to **Human-led, AI
may draft** until a row is added.

Legend:
- **H** — Human-led. Human does the work; AI may be used as a lookup
  tool but does not draft the artifact.
- **AI→H** — AI-drafts, human-reviews. AI produces the artifact; a
  named human reviewer attests and owns the merge/publish.
- **H→AI** — Human-drafts, AI-checks. Human produces the artifact; AI
  is used as a critic, linter, or test generator before review.
- **Pair** — Human and AI iterate together on the same artifact; no
  hand-off, human remains at the keyboard.
- **DND** — Do not delegate. Keep human-only until this row is revised.

Evidence column maps to artifacts the merge/publish gate requires; see
`REVIEW_STANDARDS.md` and the module's review checklist.
```

### 2.2 The matrix (worked example for a mid-size backend/platform team)

| # | Task class | Default route | Signer | Evidence to merge/ship | Rationale |
|---|---|---|---|---|---|
| 1 | Internal CRUD endpoint, no auth change, non-billing | **AI→H** | Author + 1 reviewer | Tests + review checklist ticked + conventional-commit | Low blast radius; AI drafts are reliable here; cheap to revert. |
| 2 | External-facing API change (contract, response shape, status codes) | **Pair** | Author + 2 reviewers (one API owner) | Tests + review checklist + API changelog entry + versioning note | Breaks consumers if wrong. Reversibility is low once a client upgrades. |
| 3 | Database schema migration | **H** | Author + DBA + 1 senior IC | Migration dry-run log + rollback script + review checklist | Hard to reverse in prod. AI frequently invents column types / nullability. |
| 4 | Auth / authz / rate-limit code | **H** | Author + security reviewer | Threat-model note + review checklist (high-risk tier) + security sign-off | Security-critical. Keep human-authored to preserve attestation chain. |
| 5 | Prompt template or model identifier change (shipped to prod) | **H→AI** | Author + prompt owner + eval owner | Eval run id + diff vs. baseline + review checklist | Change must pass the commissioned eval suite before merge (Mod-103 Ex-04). AI may critique the prompt but doesn't rewrite it blind. |
| 6 | Refactor within one file / one module, no behaviour change | **AI→H** | Author + 1 reviewer | Tests + review checklist + "no behaviour change" assertion from author | Mechanical; well-scoped; tests catch regressions. |
| 7 | Cross-module refactor, >3 files or >150 lines | **Pair** | Author + 2 reviewers | Tests + review checklist + ADR if the refactor shifts a boundary | Scope-creep risk from AI is real; pair keeps the diff honest. |
| 8 | Unit / integration test authoring for existing code | **AI→H** | Author + 1 reviewer | Coverage delta + review checklist | Author still owns that the tests assert the right behaviour, not just green. |
| 9 | Evaluation suite authoring for new prompt / model | **H→AI** | Author + eval owner | Suite committed + baseline committed + review checklist | Eval design is judgment-heavy. AI can scaffold cases, human owns the rubric (Mod-103 Ex-04/05). |
| 10 | ADR (architecture decision record) | **H→AI** | Author + 1 senior IC + team manager | ADR merged + linked from the PR that implements it (Mod-103 Ex-03) | AI can critique a draft ADR; it should not author one blind. |
| 11 | Internal design doc, runbook, or 1:1 summary | **AI→H** | Author (owner of the thing being documented) | Review checklist (docs) ticked | Low external blast radius; author owns factual accuracy. |
| 12 | Customer-facing content (release notes, status posts, external blog) | **H→AI** | Author + team manager + comms reviewer | Review checklist (docs, high-risk tier) + tone check | Reputation surface. AI may critique but not draft. |
| 13 | Incident postmortem (Mod-109) | **H** | Author (incident commander) + manager | Postmortem template + action items filed + no AI-authored prose in root-cause / contributing-factors sections | Blameless postmortem authorship is a people act, not a drafting task. AI may summarize the timeline from logs, not the analysis. |
| 14 | Performance review / 1:1 feedback / hiring notes (Mod-108) | **DND** | Manager only | — | Covered by a separate hiring-and-feedback policy. Do not delegate authorship of judgments about a person to an AI tool. |
| 15 | Governance artifact (policy, compliance mapping, legal response) | **DND** | Team manager + governance / legal | — | Accountability cannot be delegated outside a named human; AI tools do not have signing authority. |

### 2.3 How to read a row

Row 5 ("prompt template or model identifier change") says, in English:

> When someone on this team changes a prompt template or swaps the model identifier in prod code, the human drafts the change (the AI assistant may be used to critique the draft) — route code **H→AI**. The author signs the PR. The prompt owner signs the PR. The eval owner signs the PR. Before merge, the author must attach the eval run id, show the diff versus the baseline, and tick the review checklist. The reason this row is `H→AI` and not `AI→H` is that prompts are high-leverage artifacts: a bad prompt affects every request; AI assistants frequently drift tone or quietly remove safety instructions when "cleaning up" a prompt; and the commissioned eval gate (Mod-103 Ex-04) is the enforcement mechanism.

Every row should be legible like this. If a row can't be read aloud as a one-paragraph decision, it's under-specified and needs to be split.

### 2.4 "Promotion" and "demotion" rules

The matrix needs a written mechanism for moving a row between routes:

- **Promoting a row from `H` to `AI→H`** (giving AI more scope): requires one month of successful `H` operation, an ADR explaining what changed in tooling or evidence that warrants the promotion, and sign-off from the row's signer list.
- **Demoting a row** (pulling scope back): any signer can demote unilaterally after a severity-Medium-or-higher miss, with a writeup within seven days.

Promotion is slow; demotion is fast. This asymmetry is deliberate: it reflects the fact that an undetected bad merge is more expensive than a slightly-over-reviewed good merge.

## 3. Decision rationale (why this shape and not others)

- **Routing by task class, not by assistant capability.** "What can the model do?" is a moving target; "what is the team willing to let it do unsupervised?" is a team-owned decision that doesn't move every time a model is released. The matrix is stable where the model landscape isn't.
- **Three routes, not two.** A binary "human / AI" split hides where the actual value is: AI as a reviewer for human-authored artifacts (H→AI) is often higher-leverage than AI as a drafter. Treating those as separate rows surfaces where the team should invest in tooling (test generators, critic prompts, eval scaffolds).
- **"DND" (do-not-delegate) rows exist on purpose.** Hiring, feedback, incident postmortem analysis, and governance sign-offs are not productivity tasks; they're acts that carry accountability weight. Letting an agent draft them degrades the contract between manager and team. This aligns with NIST AI RMF's **Govern 1.1 / 2.1 / 4.1** framing (responsibilities for AI actors are documented; roles and responsibilities are communicated; accountability structures are in place).
- **Evidence column, not just route.** "AI did it" without evidence is a social claim; "AI did it + checklist + eval run id + reviewer attestation" is an auditable fact. The evidence requirement is the enforcement mechanism; the route is just the default.
- **Promotion requires an ADR, demotion doesn't.** Writing an ADR to add scope forces explicit reasoning; demoting under incident response needs to be fast. (ADR practice aligns with the Mod-103 Ex-03 standards-as-code approach.)
- **"Default to `H` for anything not on the matrix."** New task classes pop up constantly. The safe default is the one that keeps accountability with a named human until the team has decided otherwise.

## 4. Validation steps

1. **Backfill test.** Take the last 20 merged PRs on this team. Can you assign each to a row in the matrix in under 10 seconds? If more than ~15% end up in "none of the above", add rows until the coverage is >85%.
2. **Shadow run.** For two weeks, have the team add the matrix row ID to the PR description of every merge ("row 6 — single-file refactor"). At the end of the two weeks, count: rows that were never used (candidates for removal), rows used far more than expected (candidates for subdivision), rows that got routing exceptions ("marked row 1 but actually added auth logic").
3. **Adversarial review.** Ask a senior IC and an adjacent-team manager to read the matrix and try to find a plausible ticket class that isn't on it or that's routed wrong. Their edits are your gap list.
4. **Incident replay.** For any AI-adjacent miss the team has had in the last two quarters, ask: "would this row have caught it?" If not, strengthen the evidence column for that row.
5. **Printout test.** Print the matrix on one page. If it doesn't fit, cut rows or merge them. Matrices that don't fit on a page don't survive contact with reality.
6. **Sign-off.** Each team member signs that they've read and acknowledged the matrix. Keep the signature list in the same page; new joiners sign during onboarding.

## 5. Rubric

Grade a submitted matrix 0–3 per row.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Row count discipline** | >25 rows, no structure | 15–25 rows | 8–15 rows, grouped | 8–15 rows, grouped, each row distinguishable from its neighbours in one sentence |
| R2 | **Route granularity** | Only H / AI | 3 routes | 3 routes with worked examples | 3 routes + DND row + promotion/demotion rules |
| R3 | **Evidence requirements** | None | "Review" vague | Checklist link + reviewer named | Checklist + eval / ADR / threat-model where applicable + named reviewer |
| R4 | **Risk-tier alignment** | Not tiered | Tiered by file path | Tiered by effect of the change | Tiered by effect × reversibility, with worked thresholds |
| R5 | **DND coverage** | No DND rows | Hiring / feedback only | Hiring + governance + postmortem | Hiring + governance + postmortem + any domain-specific sensitive class |
| R6 | **Promotion / demotion mechanism** | None | "We'll revisit" | Promotion requires ADR | Promotion requires ADR + demotion is fast + cadence is named |
| R7 | **Fit to real backlog** | Generic rows, no team context | A few team-specific rows | Clearly derived from the team's actual tickets | Backfill-tested against ≥20 recent tickets; coverage reported |
| R8 | **Review cadence & ownership** | None | Owner named | Owner + quarterly cadence | Owner + cadence + incident-triggered off-cycle revision |

**Pass bar: 16/24.** Below that, iterate before publishing to the team.

## 6. Common mistakes

- **One-size-fits-all routing** ("AI assists everywhere, humans review") — this is route-by-slogan, not route-by-task. It collapses the moment two tickets with different risk profiles arrive.
- **Routing by file path instead of by effect.** "Anything under `src/auth/`" is a coarse proxy for risk; effect-based rows ("changes auth / authz code anywhere in the repo") catch the real cases.
- **No DND rows.** Teams that don't write down what they refuse to delegate end up delegating it by accident the first time it's convenient.
- **Evidence column is just "code review".** Code review is the baseline; the whole point of the matrix is to say *what else* is required for the AI-touched cases.
- **Promoting rows too fast.** The temptation after one quiet month is to move everything to AI-drafts. Hold the line; promotion should feel slow.
- **Matrix that's impossible to look up.** If finding the right row takes more than a few seconds, the team will ignore the matrix and default to tribal knowledge.
- **No signer column.** Without named signers, the matrix is a policy document; with named signers it's a contract.
- **Matrix frozen at v1.** The absence of a cadence is the strongest signal that the matrix will go stale within a year.
- **Letting the AI tool "sign off" on routing.** Only humans route tasks to themselves; agents don't self-approve the route they take.
- **Omitting the "default to H for anything not on the matrix" rule.** Without that fallback, novel tasks silently default to whatever the last PR did.

## 7. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function (especially Govern 1.1, 2.1, 4.1) motivates the named-signer, documented-responsibility, and review-cadence columns of the matrix.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the risk framing that justifies keeping security-sensitive rows (auth / prompts / data access) human-led; surface in the evidence column.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy; used to sanity-check that security-adjacent rows are not routed to AI drafts without a human-authored threat-model attestation.

<!-- follow-up: once the team has shadow-run the matrix for a quarter, add an "observed exceptions" column showing the % of merges that deviated from the default route and the reasons. That data drives the first promotion/demotion cycle. -->
