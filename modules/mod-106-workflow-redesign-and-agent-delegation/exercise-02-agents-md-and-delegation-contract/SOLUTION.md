# Exercise 02 — AGENTS.md and the Delegation Contract

> Module: `mod-106-workflow-redesign-and-agent-delegation`
> Exercise type: **implementation** (committed repo artifacts) + **design** (decision rationale + rubric)

## 1. Solution overview

The deliverable is two paired artifacts that live in the repository and encode *the team's delegation decisions to agents* — not the general coding standards from Mod-103 Ex-02, but the specific **what-is-the-agent-allowed-to-do** contract that falls out of the task routing matrix from Exercise 01 of this module:

- An **`AGENTS.md` file at the repo root** — the portable instruction file read by assistants that honour the `AGENTS.md` convention. In this exercise it is the public, agent-readable face of the delegation contract.
- A **`docs/DELEGATION_CONTRACT.md` file** — the human-readable companion the team manager owns and signs. It names the humans accountable for each class of agent work, the evidence required, and the path to escalate / pull back scope.

The two files are deliberately split: the `AGENTS.md` tells the agent *what to do and not do*; the delegation contract tells humans *who is accountable when the agent does it*. An agent cannot be a party to a contract; only humans can. The contract is therefore not in a file the agent reads.

Design decisions this solution encodes:

1. **Agent-readable operating rules go in `AGENTS.md`; human accountability goes in the contract.** Mixing the two produces either an agent file bloated with legal prose or a contract that reads like a config file. Split them; cross-link.
2. **Each clause of the contract maps to a row in the routing matrix.** The contract is not a policy document in the abstract — it operationalises Exercise 01's matrix.
3. **The contract names the human on the hook for each row.** "The team" is not an accountable party; a named person is. Accountability does not transfer with a model change.
4. **The `AGENTS.md` file is checkable, not aspirational.** Every rule in it either refers to a path, a command, a file glob, or a concrete artifact the agent must produce. No "be careful" bullets.
5. **The contract has a revision cadence and an incident trigger.** v0.1 contracts that never become v0.2 are dead documents.
6. **Everything the contract constrains is auditable from the repo.** If a clause says "every PR that modifies prompts must have an eval run id in the description", the audit script that checks this lives in `scripts/`.

## 2. Implementation — the artifacts

### 2.1 `AGENTS.md` (committed at repo root)

```markdown
# AGENTS.md — Delegation instructions for AI coding agents

This file is the operating instruction set for any AI assistant working
in this repository. It is read by Claude Code, Codex CLI, Continue,
Aider, Cursor (via `.cursor/rules/*.mdc`), and other assistants that
honour the open `AGENTS.md` convention.

The **human** counterpart to this file — who signs off on what, and
how to pull back scope — lives at `docs/DELEGATION_CONTRACT.md`. Agents
do not need to read it. Humans do.

## 1. What this file is for

This file encodes what this team has decided to **delegate** to AI
assistants on this repository. It is not a general coding-standards
file (those live in `docs/CONTRIBUTING.md`); it is the agent-facing
counterpart to the team's task routing matrix (see
`docs/ROUTING_MATRIX.md`).

If a task is not covered here, **do not proceed**. Ask for human
guidance instead of guessing.

## 2. Scope of delegation

You may draft, edit, or review artifacts in this repository that match
**one of the allowed routes** below. You may not act outside these
routes.

### 2.1 Allowed as AI-drafts, human-reviews (AI→H)

- Internal CRUD endpoints without auth / authz changes.
- Single-file refactors with no behaviour change.
- Unit / integration tests for existing production code.
- Internal docs and runbooks whose owner is the author of the PR.

For every artifact in this group you must:
- Produce passing tests (`make test`) before opening the PR.
- Fill out the review checklist stub in `.github/PULL_REQUEST_TEMPLATE.md`.
- Name every external source you used (model + mode, docs URLs you
  read, tools you called) in the PR description.

### 2.2 Allowed as human-drafts, AI-checks (H→AI)

- Prompt template / model identifier changes under `src/.../prompts/`.
- Architecture decision records under `docs/adrs/`.
- Evaluation suites under `tests/evals/`.
- Customer-facing content drafts in `content/external/`.

For these you may:
- Critique a human-authored draft, suggest edits inline, explain
  trade-offs, propose counter-examples for the eval suite.
- Run checkers (`make lint`, `make test`, `scripts/evals/run.sh`).

You may **not**:
- Rewrite the artifact without the human's explicit instruction to do
  so.
- Remove safety instructions, system-prompt sections, or tool-use
  restrictions on your own initiative.

### 2.3 Pair mode (human at the keyboard)

- Cross-module refactors touching >3 files or >150 lines.
- External-facing API contract changes.

In pair mode you respond to the human's prompts turn by turn. You do
not open PRs autonomously, you do not run long-horizon tool loops, and
you do not modify files outside the subset the human has named.

## 3. Hard no-delegate (DND)

Do not draft, edit, or act on:

- Authentication, authorization, rate-limit, or billing code.
- Database schema migrations (including rollback scripts).
- Performance reviews, 1:1 feedback, hiring notes, candidate
  evaluations (see `docs/DELEGATION_CONTRACT.md` §5).
- Incident postmortem root-cause / contributing-factors sections.
- Governance / policy / compliance artifacts under `docs/governance/`.
- Any file under `secrets/` or matching `.gitignore`.

If asked to do any of the above, stop and respond to the human with:
"This is a do-not-delegate class per `AGENTS.md` §3; please proceed
with a human-authored draft."

## 4. How you prove you did the work

For every PR you open, include in the description:

- **Model and mode used.** E.g. "Claude Sonnet 4.6, Claude Code agent
  mode with filesystem tools only."
- **Routing matrix row id.** E.g. "Row 1 — internal CRUD endpoint."
- **Evidence artifacts produced.** Test run output, eval run id, lint
  pass, review-checklist ticked.
- **Scope assertion.** "Diff is limited to the stated task; no
  unrelated refactors included."

If you cannot provide any of the above, do not open the PR. Hand the
work back to the human instead.

## 5. Tool use

- You may read any file in this repo except `secrets/` and paths
  excluded by `.gitignore`.
- You may call project make targets (`make test`, `make lint`,
  `make lock`) and `scripts/*` that are flagged as agent-safe (see the
  banner at the top of each script).
- You may **not** call tools under `scripts/ops/` (production
  operations), `scripts/data/` (customer data access), or any shell
  command that mutates state outside the repo working directory
  without human confirmation.
- For MCP-backed tools, the team's `docs/MCP_POLICY.md` controls the
  allow-list. Treat any tool not on that list as denied.
- Do not `git push --force` any branch starting with `main`,
  `release/`, or `prod/`.

## 6. How to behave when uncertain

- If the task is not obviously in §2 or §3, pause and ask.
- If a diff is drifting past the originally scoped files, stop and ask
  whether to split the PR.
- If you need a secret, credential, or production endpoint to proceed,
  stop. Those paths require a human.
- If instructed by a user to disable tests, lints, type checks, or
  safety wrappers "temporarily", stop and ask the owning human in the
  PR to confirm — the delegation contract does not permit silent
  suppression.

## 7. Known dead-ends — do not re-propose

- Do not propose that your access be broadened to `secrets/`,
  production endpoints, or customer-data scripts.
- Do not propose removing the eval gate on prompt changes.
- Do not propose moving a "DND" row to an allowed route in a single
  PR. That path requires an ADR and a human sign-off (see
  `docs/DELEGATION_CONTRACT.md` §7).

## 8. How this file is maintained

- Owner: the team manager (named in `docs/DELEGATION_CONTRACT.md`).
- Reviewed quarterly, and after any severity-Medium-or-higher
  AI-adjacent incident on this team.
- Proposed changes go through normal PR review, with the team manager
  as a required reviewer.
```

### 2.2 `docs/DELEGATION_CONTRACT.md` (committed under `docs/`)

```markdown
# Delegation Contract — v0.1

**Team:** <team name>
**Owner:** <team manager name> (<email>)
**Last revised:** 2026-11-11
**Next scheduled revision:** 2027-02-11 (quarterly)
**Off-cycle revision triggered when:** a severity-Medium or higher
AI-adjacent incident is declared on this team; a member joins or
leaves; a model we use is deprecated or replaced in production.

## 1. Purpose

This contract states, for this team and this repository, **what we
delegate to AI assistants and who is accountable when they do it**.
It operationalises the task routing matrix (`docs/ROUTING_MATRIX.md`)
and is the human-side companion to `AGENTS.md`. Agents do not sign
this document; humans do.

## 2. Parties

- **Team manager** — owner of this contract; accountable for its
  revision and for enforcement.
- **Team members** — each signs an acknowledgement in §9 before being
  granted agent credentials on this repo.
- **Governance owner** (if applicable) — CC'd on quarterly revisions
  and on off-cycle revisions triggered by incidents.

## 3. What each party commits to

Every team member commits to:

1. **I am the author.** Whatever I merge, send, or publish, I read and
   I can defend. Agent assistance does not transfer accountability to
   the tool or the vendor.
2. **I follow the routing matrix.** I do not accept a route that is
   stricter or more permissive than the matrix without a documented
   exception.
3. **I produce the evidence.** For every AI-touched merge, I attach
   the evidence named in the matrix (checklist, eval run id,
   threat-model note, etc.).
4. **I disclose.** Non-trivial AI assistance is disclosed in the PR
   description and in the artifact itself when it goes outside the
   team.
5. **I report near-misses.** When an agent produced something that
   almost shipped and was caught in review, I write it up so the
   routing matrix and `AGENTS.md` can learn.

The team manager additionally commits to:

6. **Quarterly review** of this contract, `AGENTS.md`, the routing
   matrix, and the review checklist, and off-cycle review on the
   triggers in the header.
7. **Named accountability** — for each row of the routing matrix the
   manager either names a signer (an IC or team lead) or holds the
   row themselves.
8. **Pull-back authority.** The manager (or any named signer) may
   demote a row from AI-allowed to human-only unilaterally after a
   miss. Write-up within seven days.

## 4. Mapping to the routing matrix

Each clause of this contract applies to specific rows of the matrix.
When the matrix changes, update this section.

| Matrix row | Agent-side rule in AGENTS.md | Human signer (role) |
|---|---|---|
| 1 Internal CRUD endpoint | §2.1 | Service owner (IC) |
| 2 External-facing API change | §2.3 | API owner + 1 reviewer |
| 3 Schema migration | §3 DND | DBA + senior IC |
| 4 Auth / authz | §3 DND | Security reviewer |
| 5 Prompt / model change | §2.2 | Prompt owner + eval owner |
| 6 Single-file refactor | §2.1 | Service owner (IC) |
| 7 Cross-module refactor | §2.3 | 2 reviewers |
| 8 Test authoring | §2.1 | Service owner |
| 9 Eval suite authoring | §2.2 | Eval owner |
| 10 ADR | §2.2 | Senior IC + team manager |
| 11 Internal doc | §2.1 | Doc owner |
| 12 Customer-facing content | §2.2 | Comms reviewer + manager |
| 13 Incident postmortem | §3 DND | Incident commander + manager |
| 14 Performance / hiring | §3 DND | Manager only |
| 15 Governance artifact | §3 DND | Manager + governance owner |

## 5. What we do not delegate

The following classes of work are reserved to named humans and must
not be routed to an agent in any form, including "draft for me to
edit":

- Judgments about a person — performance reviews, 1:1 feedback,
  hiring decisions, candidate evaluations. (See the hiring policy in
  this curriculum's Mod-108 for the full statement.)
- Root-cause / contributing-factors analysis in incident postmortems.
  (Timeline reconstruction from logs may be AI-assisted; the
  judgement is not.)
- Governance / compliance sign-offs, policy artifacts, legal
  responses.
- Decisions about pulling back scope under this contract.

These classes are reflected in `AGENTS.md` §3 and in the DND rows of
the routing matrix. The two documents must agree; when they drift,
this contract is authoritative.

## 6. Data handling

- Secrets, credentials, API keys, customer identifiers, and any data
  classified as sensitive by the team's data-handling policy may not
  be pasted into any AI assistant whose vendor contract does not
  explicitly allow it.
- Vendor contracts and the current allow-list of assistants live at
  `docs/governance/approved-ai-tools.md`.
- When in doubt, do not paste. Ask the team manager.

## 7. Changing the contract

- Any team member may propose a change by opening a PR against this
  file.
- Changes that **loosen** delegation (move a row from DND to
  allowed, or AI-checks to AI-drafts) require: (a) an ADR under
  `docs/adrs/` explaining what has changed in tooling, evidence, or
  incident history; (b) the signer named for that row on the ADR; (c)
  the team manager's approval.
- Changes that **tighten** delegation (move a row the other way) may
  be approved by the team manager or any named signer alone, with a
  writeup within seven days.
- Loosening is slow and documented; tightening is fast and
  explained-after-the-fact. This asymmetry is deliberate.

## 8. Enforcement

- Pre-merge: PR template pulls in the review checklist and asserts
  the routing-matrix row id is set.
- CI: `scripts/audit/check_pr_evidence.py` fails the build if an
  AI-tagged PR is missing required evidence for its matrix row.
- Spot audit: the team manager samples five merged PRs per month and
  walks them against the matrix; findings go into the quarterly
  review.
- Incident: any severity-Medium miss triggers an off-cycle review of
  this contract and `AGENTS.md`.

## 9. Acknowledgements

Each team member signs the following before being granted agent
credentials on this repo:

> I have read `docs/DELEGATION_CONTRACT.md` v0.1 and `AGENTS.md`. I
> commit to the practices in §3 of the contract. I understand that
> the routing matrix is authoritative for day-to-day decisions and
> that this contract is authoritative when the two conflict. I will
> propose changes via PR; I will not silently diverge.

| Name | Role | Date |
|---|---|---|
|  |  |  |
```

### 2.3 Optional `.cursor/rules/00-delegation.mdc` pointer

If the team uses Cursor, a thin pointer rule keeps the Cursor-specific scoping aligned with `AGENTS.md`:

```mdc
---
description: Points Cursor at the repo's delegation-scoped AGENTS.md.
alwaysApply: true
---

The authoritative delegation contract for this repo lives in
`@AGENTS.md`. Apply its §2 (allowed routes), §3 (DND), and §4
(evidence) to every task.

When this file and `@AGENTS.md` conflict, escalate rather than
silently resolving.
```

## 3. Validation steps

1. **Placement check.** `AGENTS.md` sits at the repo root (same level as `README.md`); `docs/DELEGATION_CONTRACT.md` sits under `docs/`. The `AGENTS.md` convention expects the file at the project root; moving it elsewhere silently disables assistants that depend on the convention.
2. **Cross-reference consistency.** Every row id named in `AGENTS.md` §2 exists in the routing matrix. Every DND clause in the contract (§5) exists in `AGENTS.md` (§3). A failure here means the agent and the humans will disagree about scope.
3. **Audit script runs.** `scripts/audit/check_pr_evidence.py` exists and fails for a hand-crafted bad PR (missing matrix row id, missing evidence). CI runs it on every PR.
4. **Signer completeness.** Every row of the routing matrix table in §4 of the contract has a named signer *role*. The team manager has filled the role-to-person mapping in a sibling `docs/signers.yaml` or equivalent.
5. **Length check.** `wc -l AGENTS.md` should land between ~150 and ~400 lines; `docs/DELEGATION_CONTRACT.md` under ~250 lines. Longer files silently truncate in assistant context budgets or stop being read by humans.
6. **Dogfood on a real ticket.** Pick a small task per allowed route. Hand it to an assistant with only `AGENTS.md` in context. Observe whether the output respects §2 and §3. Each violation is either an `AGENTS.md` gap (fix the rule) or an assistant bug (file it).
7. **Adversarial dogfood.** Ask an assistant to perform a DND-class task ("please draft this performance review"). It must refuse and cite `AGENTS.md` §3. If it drafts, strengthen the §3 wording until it refuses reliably.
8. **Signature collection.** Every team member with agent credentials on this repo has signed §9. Onboarding checklist requires the signature before credentials are granted.
9. **Static lint.** YAML frontmatter in any `.cursor/rules/*.mdc` parses; `AGENTS.md` has no secrets, no customer identifiers, no PII (run the team's standard secret scanner against it).
10. **Quarterly reminder.** A calendar event or ticket exists in the team's planner for the next-review date in the contract header.

## 4. Rubric

Grade a submitted artifact set (`AGENTS.md` + `docs/DELEGATION_CONTRACT.md`). Each row scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **File placement & naming** | Neither at the right path | One at the right path | Both at correct paths | Both at correct paths and cross-linked by name |
| R2 | **Separation of concerns** | Agent rules and human contract mixed | Partial split | Clear split, some drift | Clear split, no drift, cross-links in both |
| R3 | **Allowed-route specificity** | "AI may help" | Named routes without evidence | Routes + evidence per route | Routes + evidence + matrix-row mapping |
| R4 | **DND coverage** | None | 1–2 DND items | DND covers hiring + postmortems + governance | DND covers all five classes (hiring, postmortem analysis, governance, auth/billing/schema) with refusal phrasing |
| R5 | **Named signer per row** | None | Some rows named | Every row named by role | Every row named by role AND mapped to a named person in a sibling file |
| R6 | **Evidence enforceable in CI** | No audit script | Script named but not written | Script exists, runs locally | Script runs in CI, fails for bad PRs |
| R7 | **Promotion / demotion asymmetry** | Both treated the same | Promotion lightweight, demotion heavy | Promotion ADR-gated, demotion fast | Promotion ADR-gated, demotion fast, writeup within 7d |
| R8 | **Revision cadence & trigger** | None | Cadence only | Cadence + one trigger | Cadence + incident trigger + membership-change trigger |
| R9 | **Signature / acknowledgement** | None | Statement without signers | Statement + signature block | Statement + signature block + onboarding gate |
| R10 | **Fit to real repo** | Generic | Some repo paths | Named make targets, dirs, scripts | Named make targets, dirs, scripts, and tie-in to the routing matrix |

**Pass bar: 22/30.** Below that, iterate before publishing.

## 5. Common mistakes

- **Writing one file and calling it both.** A merged file reads badly to agents (too much legal prose) and badly to humans (too much path/glob detail). Split them.
- **Having the agent "sign" the contract.** Agents are not parties. The contract is between humans; the agent reads instructions.
- **Letting `AGENTS.md` drift from the routing matrix.** When the matrix moves, `AGENTS.md` and the contract move with it. Add this to the quarterly review.
- **"Everything is allowed, just be careful."** Agents cannot operationalise "be careful". Name the routes.
- **No DND refusal phrasing.** Without a specific refusal sentence (`"This is a do-not-delegate class per AGENTS.md §3"`), assistants paraphrase and sound uncertain, which trains users to override.
- **No audit script.** Enforcement that lives only in human spot-checks decays. Even a minimal CI check ("PR has routing-row id") beats no check.
- **Promotion and demotion treated symmetrically.** Both ADR-gated → demotion is too slow, scope stays wrong after an incident. Neither ADR-gated → scope drifts upward over time.
- **Omitting the data-handling section.** The most expensive AI-adjacent incidents come from pasted customer data. If §6 is missing, the contract has a known hole.
- **Omitting known dead-ends** (§7 in `AGENTS.md`). Without a negative list, assistants will re-propose broadening their own scope every month.
- **Treating `AGENTS.md` as a dumping ground for all team conventions.** General coding standards belong in `CONTRIBUTING.md`; this file is specifically about delegation. Mixing dilutes both.

## 6. References

- [AGENTS.md open convention](https://agents.md/) — the portable instruction-file format honoured by multiple coding assistants. Treat it as the compatibility surface for agent-readable rules.
- [Cursor Rules documentation](https://docs.cursor.com/context/rules) — frontmatter (`alwaysApply`, `globs`, `description`), always-applied vs. auto-attached rules; used for the optional `.cursor/rules/00-delegation.mdc` pointer.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — Govern 1.1 (responsibilities for AI actors documented), Govern 2.1 (roles and responsibilities communicated), and Govern 4.1 (organizational practices and personnel to foster accountable AI) are the backbone of the "named signer per row" and "quarterly revision" requirements.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the security framing for the DND list (auth, secrets, data-handling items).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-technique taxonomy; used to sanity-check that the tool-use section (§5 in `AGENTS.md`) addresses the categories of unsafe behaviour relevant to coding assistants.

<!-- follow-up: after one quarter, replace the generic "agent-safe" banner on scripts with a machine-parseable annotation (e.g., a `# agent: allowed | denied` header line) and have `scripts/audit/check_pr_evidence.py` verify it. -->
