# Exercise 04 — Human-in-the-Loop Checkpoint Design

> Module: `mod-106-workflow-redesign-and-agent-delegation`
> Exercise type: **design** (worked checkpoint map + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **checkpoint map** that, for every agentic workflow the team runs, names:

1. The points in the workflow where a named human must stop the agent before it proceeds.
2. What the human must *see* at each checkpoint (the information they need to decide).
3. What the human must *do* (the shape of the decision — approve / reject / amend / escalate).
4. What is logged at the checkpoint for later audit.
5. The default outcome if the human does not respond in time.

The checkpoint map is the operational joint between the task routing matrix (Ex-01, which says *who leads* a task), the delegation contract (Ex-02, which says *who is accountable* for an agent's output), and the MCP tool authorisation policy (Ex-03, which says *what the agent is technically permitted to invoke*). This exercise says **when** the human has to stop the loop, and what good "stopping" actually looks like.

Design decisions this solution encodes:

1. **Checkpoints are inserted at state transitions, not at tool calls alone.** A good checkpoint is one that sits where a decision will be hard to reverse — "about to merge", "about to post", "about to run a migration" — not just before any mutating call.
2. **Each checkpoint names a *decision*, not a *review*.** "Review this" is weak; "approve / reject / amend" is decision-shaped and short enough to stay cheap.
3. **Checkpoints have a timeout, and the timeout has a default.** An HITL gate that can block forever becomes a gate that gets bypassed.
4. **The default on timeout is tier-dependent, not uniform.** For low-risk steps the default may be "proceed with logged auto-approval"; for high-risk steps the default is always "abort, do not proceed".
5. **"What the human sees" is prescribed.** Reviewers are only as good as the information surfaced to them. The map specifies the artifact (diff, proposed message, proposed command, evidence bundle), not just the fact of the stop.
6. **Checkpoints are enforced in the agent runtime**, not left to etiquette. "The human should notice before merging" is not a checkpoint.
7. **Each checkpoint is auditable after the fact.** Every approval or rejection leaves a line in the session log: who approved, when, with what artifact in view.

## 2. Implementation — worked checkpoint map

### 2.1 Structure

The map is a table of checkpoints, grouped by workflow. For every checkpoint:

| Field | What it says |
|---|---|
| **ID** | A stable identifier (`CP-<workflow>-<n>`) so audit logs can reference it. |
| **Workflow** | The agentic workflow it belongs to. |
| **Trigger** | The state transition that fires the checkpoint. |
| **Decision maker** | The role and named signer. |
| **What is shown** | The exact artifact surfaced to the human. |
| **Decision shape** | The buttons / commands the human has: usually `approve` / `reject` / `amend` / `escalate`. |
| **Timeout** | How long the agent waits. |
| **Default on timeout** | What happens if the timeout expires. Always **abort** for high-risk checkpoints. |
| **Log line** | What is written to the session log when the checkpoint resolves. |

### 2.2 The map — worked example across five common workflows

#### Workflow A — AI-drafted PR for an internal CRUD endpoint (routing-matrix row 1)

| ID | Trigger | Decision maker | What is shown | Decision shape | Timeout | Default on timeout | Log line |
|---|---|---|---|---|---|---|---|
| CP-A-1 | Agent has produced a diff and is about to open a PR. | PR author (the human who dispatched the agent). | Full diff + test-run output + review-checklist stub + routing-matrix row id. | `approve` / `amend` / `abort`. | No timeout — blocks until human responds. | n/a. | `CP-A-1 approved by <user> at <ts>, diff sha <sha>`. |
| CP-A-2 | Reviewer is about to approve the PR on GitHub. | Code reviewer (named human, not the author). | Diff + review checklist as ticked by author + author's attestation line. | `approve` / `request-changes`. | No timeout. | n/a. | `CP-A-2: reviewer <user> decision <approve/request-changes> at <ts>`. |

**Note.** CP-A-2 is not an agent-runtime checkpoint; it's the existing GitHub review gate. The map lists it to show the end-to-end chain and make it clear that the agent's checkpoint flow terminates at a human review the team already runs.

#### Workflow B — Prompt / model identifier change (routing-matrix row 5)

| ID | Trigger | Decision maker | What is shown | Decision shape | Timeout | Default on timeout | Log line |
|---|---|---|---|---|---|---|---|
| CP-B-1 | Human has produced a proposed prompt diff; agent is about to run the eval suite. | Prompt owner. | Proposed diff + which eval suite will run + baseline reference. | `approve` / `amend` / `abort`. | 15 min. | Abort. | `CP-B-1 ...`. |
| CP-B-2 | Eval run finishes; agent is about to attach the run id to the PR. | Prompt owner + eval owner. | Pass/fail vs. baseline, per-case deltas, failure examples. | `attach-and-open-PR` / `reject-and-redraft` / `escalate`. | 30 min. | Abort. | `CP-B-2 ...`. |
| CP-B-3 | PR opened; reviewer about to merge. | Prompt owner + eval owner (both named on PR). | Full PR diff + eval run id + delta vs. baseline + review checklist (high-risk tier). | `approve` / `request-changes` / `escalate-to-manager`. | No timeout. | n/a. | `CP-B-3 ...`. |

#### Workflow C — Cross-module refactor, pair mode (routing-matrix row 7)

| ID | Trigger | Decision maker | What is shown | Decision shape | Timeout | Default on timeout | Log line |
|---|---|---|---|---|---|---|---|
| CP-C-1 | Agent proposes editing a file not on the originally scoped list. | PR author (at keyboard). | The new file path, why the agent wants to edit it, the proposed change. | `expand-scope` / `deny` / `split-PR`. | No timeout (pair mode). | n/a. | `CP-C-1 ...`. |
| CP-C-2 | Agent has produced a diff >100 lines. | PR author. | Diff + stats + routing-matrix row check. | `approve-and-open-PR` / `request-split` / `abort`. | No timeout. | n/a. | `CP-C-2 ...`. |

#### Workflow D — Agent posting a draft to `#team-ai-drafts` (MCP policy T2)

| ID | Trigger | Decision maker | What is shown | Decision shape | Timeout | Default on timeout | Log line |
|---|---|---|---|---|---|---|---|
| CP-D-1 | Agent is about to call Slack MCP `post_message` to `#team-ai-drafts`. | Human who dispatched the agent. | Target channel + exact message text + any attachments. | `send` / `edit-and-send` / `discard`. | 5 min. | Discard. | `CP-D-1 ...`. |

#### Workflow E — Agent attempting to invoke a T3 or T∞ tool (any workflow)

| ID | Trigger | Decision maker | What is shown | Decision shape | Timeout | Default on timeout | Log line |
|---|---|---|---|---|---|---|---|
| CP-E-1 | Agent attempts a T3 tool (externally-visible / irreversible) e.g. `merge_pull_request`, prod write, external post. | Named signer for the affected row. | Composed call + args + why the agent believes it's required + routing-matrix row check. | `compose-and-hand-to-human` / `deny`. The human invokes the tool from their own CLI; the agent never invokes. | 0 — agent cannot invoke; only compose. | Abort. | `CP-E-1 ...`. |
| CP-E-2 | Agent attempts a T∞ tool. | n/a — tool is not installed. | n/a. | n/a. | n/a. | n/a. | `CP-E-2 attempted blocked — tool not installed`. |

### 2.3 Operational specification — what "see" and "do" look like

For an interactive CLI agent (Claude Code, Codex, Cursor agent mode):

- "What is shown" renders in the terminal as a labelled block: `---- CP-A-1: Approve diff? ----` with the diff, test output, and routing row visible. The human types `approve`, `amend <instructions>`, or `abort`.
- The session log captures: checkpoint id, timestamp, user identity, decision, hash of the artifact shown. Logs are retained per the team's audit-retention policy and reviewed in the quarterly cadence from Ex-02.

For an asynchronous agent (an autonomous run that opens PRs overnight):

- "What is shown" renders as a PR description section and a status check named after the checkpoint id (e.g. "CP-A-1: author attestation pending"). The author resolves it by ticking a required box in the PR template or using a labelled comment command (`/approve-CP-A-1`).
- Timeouts, where set, convert to a labelled status that auto-closes the PR after the timeout window with a comment pointing to the checkpoint map.

### 2.4 Escalation

Every `escalate` decision has a named target in the checkpoint row (team manager or governance owner). Escalation is not optional; the human must either make the decision at this checkpoint or hand it to the named escalation target. The agent does not proceed on an escalation; it waits.

### 2.5 "Panic stop"

Every human in the session may at any time issue a single-command `abort-all` that terminates the current agent run, logs the reason, and holds all un-executed T2 / T3 tool calls. Panic stop is logged with a user identity and a timestamp; no user may remove a panic-stop entry from the log.

## 3. Decision rationale (why this shape and not others)

- **Checkpoints at state transitions, not at every tool call.** A mutating tool call is a cheap event; a state transition (about-to-merge, about-to-post) is the one where error cost spikes. Gating only the transitions keeps the number of checkpoints small enough that humans still attend to them.
- **Named decision shapes (approve / amend / abort).** "Review" is a verb, not a decision. Teams that put "review required" on their checkpoint UIs get "LGTM" replies. Naming the decision shape forces the reviewer to pick one; `amend` is especially important because it is the one that keeps the agent useful.
- **Timeouts with tier-dependent defaults.** An HITL gate that blocks forever gets removed; an HITL gate whose default is "proceed anyway" after N seconds is worse than no gate. Tier-dependent defaults (abort on high-risk, proceed-with-log on low-risk drafting steps) are the only honest compromise.
- **Prescribed artifacts.** Reviewer quality depends on what's in front of them. Specifying "full diff + test output + routing row id" at CP-A-1 forces the agent to assemble the right evidence bundle before stopping; without that specification, reviewers skim.
- **Agent never invokes T3 tools.** This is the architectural control, not a policy: the agent composes the call, the human invokes. It turns "we trust the gate" into "the gate cannot be bypassed because the tool isn't wired to the agent". See Mod-106 Ex-03 §2 T3.
- **Session logs are append-only and reviewed.** Without audit, checkpoints become performative. The quarterly review of session logs (ties into Ex-02 §8 enforcement) is the mechanism that keeps the checkpoint map honest.
- **Panic stop is a first-class control.** Any human in the session can abort. This is the counterweight to the "agent is running fast" mode; without an abort path, teams default to shutting the whole agent down the first time something goes wrong, which is an over-correction.
- **Escalation is not optional.** The failure mode of "approve or escalate" without a named target is quiet drops. Named escalation targets per checkpoint prevent this.
- **Checkpoints mapped to NIST AI RMF Manage functions.** Specifically Manage 2.3 (AI risks and benefits are monitored and controlled across a defined lifecycle, with mechanisms to respond and recover), Manage 2.4 (mechanisms to maintain the value of deployed AI systems), and Govern 1.5 (ongoing monitoring and review). The map is the artifact that answers "how do we implement those controls on this team?"

## 4. Validation steps

1. **Coverage check.** For every row of the routing matrix (Ex-01), there is at least one checkpoint in the map that fires before the irreversible step of that row. Rows with no checkpoint are either DND (and need no agent-side gate) or gaps in the map.
2. **Dry-run each workflow.** Walk each of Workflows A–E on paper and confirm the human sees what the map says they see, and the available decisions match. Pay particular attention to the asynchronous case (PR-based review): is the "what is shown" actually rendered in the PR, or is the reviewer expected to open another tab?
3. **Timeout rehearsal.** For every checkpoint with a timeout, simulate the human not responding. Confirm the default-on-timeout fires and is logged. Pay particular attention to high-risk checkpoints: default must be `abort`.
4. **Panic-stop drill.** Run an agent on a benign task; a second team member issues `abort-all`. Confirm the agent stops within a few seconds and the panic-stop log line appears.
5. **Session-log audit.** Run through one week of agent session logs. For every T2 or T3 tool call recorded, there is a matching checkpoint approval line. Missing approvals are config bugs or policy violations — track down which.
6. **Ergonomics check.** Count the checkpoints a typical PR flow hits. If it's more than ~3, the agent stops too often and reviewers will start approving reflexively. If it's 0, the gates aren't firing. Tune.
7. **Escalation reachability.** For every `escalate` option, confirm the named target is reachable within the timeout. "Escalate to governance owner" with no on-call signal is a dead target.
8. **Fire drill after an incident.** For any AI-adjacent miss on this team, add the question "would an existing checkpoint have caught it?" to the postmortem. If not, add a checkpoint or strengthen the artifact shown at an existing one.

## 5. Rubric

Grade a submitted checkpoint map 0–3 per row.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Coverage vs. routing matrix** | Random gates, no mapping | Some rows covered | Every non-DND row has a gate | Every non-DND row has a gate AND DND rows are marked as agent-not-permitted |
| R2 | **Checkpoints at state transitions** | Gated at every tool call, noisy | Mixed | Gated at transitions, sparse | Transitions + "panic stop" + explicit T3 compose-only gate |
| R3 | **Decision shape named** | "Review" | Approve/reject | Approve/reject/amend | Approve/reject/amend/escalate with named target |
| R4 | **Timeouts & defaults** | None | Timeout only, no default | Timeout + default | Timeout + tier-dependent default (abort for high-risk) |
| R5 | **Prescribed artifacts** | "Show the work" | A couple of fields | Per-checkpoint required artifacts | Required artifacts + hash recorded + log line prescribed |
| R6 | **Runtime enforcement** | Etiquette only | Checkpoints in prose | Checkpoints enforced for interactive sessions | Enforced for interactive AND asynchronous (PR-based) agent runs |
| R7 | **Panic stop** | None | Mentioned informally | Panic stop exists, logged | Panic stop + append-only log + anyone-can-fire |
| R8 | **Session log & audit** | None | Logs exist | Logs reviewed ad-hoc | Logs reviewed quarterly per cadence; samples named |
| R9 | **Ergonomics** | Too noisy or too quiet | OK on happy path | Tuned to ~3 stops/PR | Tuned + exceptions documented + review signal from team |
| R10 | **Incident integration** | None | Added after one incident | Added to postmortem template | Postmortem explicitly asks "would an existing checkpoint have caught it?" |

**Pass bar: 22/30.**

## 6. Common mistakes

- **One checkpoint per tool call.** Produces a wall of confirmation prompts that reviewers auto-accept. Checkpoints must be at state transitions.
- **"Review required" with no decision shape.** Collapses to LGTM. Specify approve / amend / reject / escalate.
- **Timeouts whose default is "proceed anyway".** Reduces the gate to a notification. Default-abort for anything mutating or externally-visible.
- **Checkpoints without prescribed artifacts.** The reviewer sees whatever happened to be on screen. Prescribe the evidence.
- **Agent invokes T3 tools; a "confirm" dialog is the only gate.** The architectural control (agent composes, human invokes) is skipped. Dialogs are bypassable; not registering the tool isn't.
- **No panic stop.** Teams default to killing the whole agent when something goes wrong. Panic stop is the cheap intermediate.
- **No session log, or a log nobody reads.** Checkpoints decay when nobody audits.
- **Checkpoint map that never changes after an incident.** Postmortems must drive the map. If the map didn't change after a miss, the miss will recur.
- **Treating interactive and asynchronous agent runs the same.** Interactive agents gate in the CLI; asynchronous agents gate in the PR. The map must prescribe both.
- **Escalation with no named target.** "Escalate as needed" is a dead branch. Name the target per checkpoint and confirm reachability.

## 7. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — Manage 2.3 / 2.4 (monitoring and response mechanisms across the AI lifecycle) and Govern 1.5 (ongoing monitoring and review) are the authoritative framing for the checkpoint map's existence and cadence. Govern 1.1 / 2.1 / 4.1 support the named-signer and escalation requirements.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the risk framing for the high-risk checkpoints (data-handling, model-wiring, security-adjacent flows).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-technique taxonomy; sanity-checks that the T3 compose-only gate covers the adversary-controlled-prompt → unintended-effect paths.
- [Model Context Protocol specification](https://modelcontextprotocol.io/) — defines the tool surface the checkpoints sit on; pairs with Mod-106 Ex-03's authorisation policy.

<!-- follow-up: once the checkpoint map has been in use for a quarter, add an "observed checkpoint latency" column (p50 / p90 time to human decision). Latency is the ergonomics signal — if any checkpoint consistently waits >1 hour, either the shown artifact is too heavy or the named signer is wrong. -->
