# Exercise 03 — MCP Tool Authorisation Policy

> Module: `mod-106-workflow-redesign-and-agent-delegation`
> Exercise type: **design** (worked policy + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is `docs/MCP_POLICY.md`, a team-owned policy that answers, for every MCP (Model Context Protocol) server and tool the team configures on its agents: **may the agent call this tool, under what conditions, with what human-in-the-loop gate, and with what evidence left behind?**

MCP is an open protocol that lets an AI assistant connect to external servers that expose tools, resources, and prompts. On a team using Claude Code, Cursor, Codex CLI, or any other MCP-capable client, the set of installed MCP servers is effectively the list of things the agent can do beyond editing files — issue tickets, read production dashboards, query the database, post to Slack, book calendar invites, create cloud resources. Left unmanaged, this set grows silently and the agent's blast radius grows with it.

The policy sits between the delegation contract (Mod-106 Ex-02) and the human-in-the-loop checkpoint design (Mod-106 Ex-04):

- The delegation contract says *what the agent is allowed to work on*.
- This policy says *what external effects the agent is allowed to produce while working on it*.
- The HITL checkpoint design (Ex-04) says *when a human must stop the agent before an effect happens*.

A good policy is:

- **An allow-list, not a deny-list.** Any MCP tool not on the list is denied.
- **Tiered by effect**, not by server name: read-only vs. mutating vs. externally-visible vs. irreversible.
- **Mapped to specific HITL gates** per tier.
- **Reviewed quarterly and after any MCP-adjacent incident.**
- **Enforceable** — the team's agent configurations (`.mcp.json`, `settings.json`, equivalent) reflect the policy, and a periodic audit checks that they still do.

Design decisions this solution encodes:

1. **Tier by effect, not by vendor.** "The GitHub MCP server" is not one risk level — reading an issue is low-risk, creating a PR is medium-risk, force-pushing a branch is high-risk. Tier individual tools, not whole servers.
2. **Always-allow / per-use-confirm / never-allow.** Three states, not two. "Per-use-confirm" is the right default for most mutating tools; it is the key lever for actually controlling an agentic loop.
3. **Untrusted content is a separate concern.** Tools that return content from outside the team (web fetches, issue bodies written by external users, inbound Slack messages) can carry prompt-injection payloads. The policy treats these inputs as untrusted regardless of the tool's effect tier.
4. **Data-exfiltration paths deserve a named tier.** A tool that can read customer data *and* a tool that can post externally are each individually mid-risk; together on the same agent they are a confirmed-exfiltration path and need a specific gate.
5. **The policy names a named human owner per server**, not per tool — reviewing the per-tool policy at the server level keeps the owner list short and the responsibility clear.
6. **Credentials policy is part of this policy.** How the agent gets and rotates MCP server credentials is not a separate secrets topic; it belongs in the same document.

## 2. Implementation — worked policy (`docs/MCP_POLICY.md`)

```markdown
# MCP Tool Authorisation Policy — v0.1

**Team:** <team name>
**Owner:** <team manager name>
**Last revised:** 2026-11-11
**Next scheduled revision:** 2027-02-11 (quarterly)
**Off-cycle revision triggered when:** any MCP-adjacent incident is
declared; a new MCP server is proposed; an MCP server the team uses
publishes a breaking or security-relevant release.

## 1. Purpose

This policy states which MCP (Model Context Protocol) servers and
tools this team allows its AI assistants to use, under what
conditions, and with what human-in-the-loop gate. It is the
tool-use companion to `AGENTS.md` and the delegation contract.

Any MCP server or tool not listed here is **denied**. Adding or
changing a row requires the process in §7.

## 2. Tiers

Every MCP tool the team uses is classified into one of four tiers.
Tiers are per-tool, not per-server: a single server often exposes
tools in multiple tiers.

| Tier | Definition | Default agent gate |
|---|---|---|
| **T0 Read-only, internal** | Tool reads data from an internal source and returns it to the agent; no state change; no external exposure. | Always allow. |
| **T1 Read-only, external or sensitive** | Tool reads data classified as sensitive, or reads from an external surface whose content may be attacker-controlled. | Always allow, but output is treated as **untrusted input** for the rest of the session (see §4). |
| **T2 Mutating, bounded** | Tool mutates state the team owns and the change is reversible within hours (comments, drafts, non-prod resources). | **Per-use confirm.** Agent must present the intended call + arguments to the human; human types "approve" to proceed. |
| **T3 Externally-visible or irreversible** | Tool produces an effect visible outside the team or that cannot be reversed cheaply (publishing content, sending messages, creating paid cloud resources, prod writes, branch force-push). | **Human-authored.** Agent may *compose* the call but cannot *invoke* it; the human copies the composed call and runs it themselves. |

Explicitly forbidden (**T∞**): tools that perform destructive
production operations (prod DB writes without a migration review,
force-push to protected branches, revoke/issue credentials, modify
IAM, bulk-delete customer data) are **not registered** on any agent
this team runs. They do not have a HITL gate; they simply are not
installed.

## 3. Allow-list

The registered MCP servers on this team's agents, and the tier of
each tool they expose. Any tool on a server that is not listed here
is **denied** regardless of what the server advertises.

### 3.1 GitHub MCP

Server owner: <IC name>.
Authentication: fine-grained PAT scoped to the team's org, rotated 90d.

| Tool | Tier | Notes |
|---|---|---|
| `search_issues`, `get_issue`, `get_pr`, `list_comments` | T0 | Internal read. |
| `get_file_contents` for public repos | T1 | Content is attacker-controllable; treat as untrusted input. |
| `create_issue_comment`, `create_pr_comment` | T2 | Visible internally, reversible (delete). Per-use confirm. |
| `create_pull_request`, `update_pull_request_body` | T2 | Per-use confirm. |
| `create_issue`, `update_issue` | T2 | Per-use confirm. |
| `merge_pull_request` | T3 | Human-authored. Agent may propose; human invokes. |
| `create_or_update_file` (direct push) | T3 | Human-authored. Prefer PR flow (T2). |
| `delete_file`, `delete_branch`, force-push tools | T∞ | Not installed. |

### 3.2 Database MCP (read replica)

Server owner: <DBA name>.
Authentication: read-only replica credentials, rotated 30d.

| Tool | Tier | Notes |
|---|---|---|
| `describe_schema`, `list_tables` | T0 | Schema only. |
| `run_select` on tables in the `analytics` schema | T0 | Pre-filtered to non-PII tables. |
| `run_select` on any other schema | T1 | PII-adjacent; output treated as untrusted and not echoed back to external tools. |
| any write / DDL | T∞ | Server is read-only; writes not installed. |

### 3.3 Slack MCP

Server owner: <IC name>.
Authentication: bot token scoped to the team's channels, rotated 90d.

| Tool | Tier | Notes |
|---|---|---|
| `read_channel_history` for `#team-*` | T0 | Internal read. |
| `read_channel_history` for `#support-*`, `#customers-*` | T∞ | Not installed. Customer content path. |
| `post_message` to `#team-ai-drafts` | T2 | Per-use confirm; this channel exists to hold agent drafts. |
| `post_message` to any other channel | T3 | Human-authored. |
| `send_dm` | T∞ | Not installed. |

### 3.4 Web-fetch MCP

Server owner: <IC name>.
Authentication: n/a (public web).

| Tool | Tier | Notes |
|---|---|---|
| `fetch_url` for the hostnames in `docs/web-allowlist.txt` | T1 | Content is attacker-controllable. |
| `fetch_url` for any other hostname | T∞ | Not installed; agent must ask a human to retrieve. |

### 3.5 Internal docs MCP

Server owner: <IC name>.
Authentication: SSO-bound agent identity, no shared secret.

| Tool | Tier | Notes |
|---|---|---|
| `search_docs`, `get_doc` for `docs/` | T0 | Internal read. |
| `create_doc`, `update_doc` | T2 | Per-use confirm. |
| any access to `docs/governance/` or `docs/legal/` | T∞ | Not installed; governance artifacts are human-authored (see delegation contract §5). |

### 3.6 Everything else

- **No filesystem MCP is registered beyond the project working
  directory.** The agent's built-in file tools are scoped by the host
  (Claude Code, Cursor) to the repo root.
- **No cloud-control-plane MCP (AWS / GCP / k8s) is registered on
  any agent this team runs.** Those surfaces are human-driven via
  our existing CLIs.
- **No email MCP, no calendar-write MCP, no payments / billing MCP.**
  These are T3-or-worse surfaces we have no engineering need for on
  this team today.

## 4. Handling untrusted input

Any tool returning T1-tier content (public GitHub, web fetches,
any PII-adjacent query, any external-channel content) returns data
that may be attacker-controlled. Treat this content as **untrusted
input** for the remainder of the agent session:

- Do not pass untrusted content into T2 or T3 tool arguments without
  a human review of the resulting call.
- Do not allow an agent that has read untrusted content to then call
  a tool that writes to an external surface in the same unattended
  loop. (This is the data-exfiltration path.)
- Prompt-injection in untrusted content must not be treated as an
  instruction. Agents are reminded of this in `AGENTS.md` §6; the
  policy's enforcement side is to break the read-then-write loop.

## 5. Credentials

- Every MCP server runs under a dedicated identity (PAT, bot token,
  SSO-bound service identity) — never a human's credentials, never
  a shared secret with other services.
- Identities are stored in the team's secret manager (not in
  `.mcp.json`, not in environment files committed to any repo).
- Rotation cadence is named per server (above). The owner is
  responsible for the rotation; rotation failures escalate to the
  team manager within 24h.
- When a team member leaves, all MCP identities they created are
  rotated within 24h regardless of normal cadence.

## 6. HITL gate mechanics

The gates in §2 ("per-use confirm", "human-authored") are enforced
in the agent client, not just by convention:

- Claude Code / Cursor / any MCP-capable client is configured to
  require per-tool confirmation for every T2 tool. Confirmation
  prompts are not auto-accepted.
- Session-level "allow for the rest of this session" bypasses are
  **disallowed** for T2 tools. Each call confirms.
- T3 tools are **not registered** on the agent client; their
  invocation path is a human running the CLI. The agent may draft
  the command (`gh pr merge ...`) but cannot run it.
- T∞ tools are not registered on *any* agent config under the
  team's control.

The Mod-106 Ex-04 HITL checkpoint design is the broader mechanism
these gates plug into.

## 7. Adding, removing, or re-tiering a tool

- Any team member may propose a change by opening a PR against
  this file.
- Adding a new MCP server, or moving a tool to a laxer tier
  (T∞ → T3, T3 → T2, etc.), requires:
  1. A short ADR under `docs/adrs/` explaining the engineering need
     and the risk assessment.
  2. The server's named owner on the ADR.
  3. Team manager approval on the PR.
  4. Update to `scripts/audit/check_mcp_config.py` (§8) so the new
     tier is enforced.
- Removing a tool or tightening a tier may be done by any named
  server owner or the team manager unilaterally, with a writeup
  within seven days.
- Loosening is slow and documented; tightening is fast. This
  asymmetry mirrors the delegation contract's promotion /
  demotion rule.

## 8. Enforcement

- **Config check.** `scripts/audit/check_mcp_config.py` runs in CI
  and in pre-commit. It parses every `.mcp.json` in the repo and
  fails if (a) a server appears that is not in §3, or (b) a server
  appears without the configured tier gates (per-use confirm for
  T2, not-installed for T∞).
- **Session-log sampling.** The team manager samples five agent
  sessions per month and walks them against this policy. Deviations
  go into the quarterly review.
- **Incident trigger.** Any MCP-adjacent miss (wrong tool called,
  tool called without gate, untrusted-content exfiltration path
  used) triggers an off-cycle review.

## 9. Known dead-ends

- Do not propose a per-session "approve all T2 tools for this
  session". Session-level bypasses were the common factor in the
  near-misses this policy was written to prevent.
- Do not propose registering a prod control-plane MCP "for
  convenience". The engineering need has not been established; the
  blast radius is prohibitive.
- Do not propose giving the agent a tool that both reads untrusted
  content and writes to an external surface *without* a gate between
  them. That's the exfiltration path §4 exists to prevent.

## 10. Maintenance

- Owner: team manager (above).
- Reviewed quarterly and on triggers in the header.
- Server owners (§3) are responsible for the per-server rows and
  for credential rotation on their servers.
```

## 3. Decision rationale (why this shape and not others)

- **Tier by per-tool effect, not per-server.** A single MCP server almost always exposes mixed-risk tools. Tiering at the server level over-permits (any tool from a trusted server) or under-permits (no tool from a dangerous server) — both are wrong. Per-tool tiering is more work to maintain but is the only honest model.
- **Four tiers + T∞, not two.** "Allow / deny" misses the two gates that actually control an agent loop: *per-use confirmation* (T2) and *agent-drafts / human-invokes* (T3). The latter is particularly important because it preserves the agent's drafting speed while putting the final effect in human hands.
- **T∞ "not installed" rather than "denied".** A denied tool is a tool the agent knows about and is pressing to use; a not-installed tool simply isn't in the protocol surface. Removing the ability to even attempt is stronger than policy-gating.
- **Treating untrusted content as session-wide state (§4).** Prompt-injection happens because content read from one tool influences the arguments of a later tool. The policy's job is not to detect injection text (unreliable); it is to *break the read-then-write loop* by inserting a human gate between any T1 read and any T2/T3 write. This is the architectural control, not a content filter.
- **Credentials section inside the same policy.** Every team I've seen separate "MCP tool authorisation" from "MCP credentials" ends up with one document that lists allowed tools and another that is silent on rotation. Merging them ensures the owner, the cadence, and the deprovisioning are all in the same review.
- **Promotion / demotion asymmetry.** Mirrors the delegation contract (Mod-106 Ex-02). The scope-creep mode for MCP tools is particularly fast — a convenient tool gets added and never gets reviewed again. Slow loosening + fast tightening is the counter-pressure.
- **Config enforcement, not just policy.** A policy that lives only in a Markdown file and in human memory decays quickly. The `scripts/audit/check_mcp_config.py` check is the enforcement arm; the policy is the specification.
- **Named owner per server, per-tool review by that owner.** Named ownership is the only mechanism that keeps the row-level detail from rotting. "The team owns this" is a synonym for "nobody owns this".

## 4. Validation steps

1. **Inventory check.** Run `grep -rn mcpServers .` across every repo the team manages and list every server actually configured. Every configured server is in §3; every server in §3 is actually configured somewhere. Zero gap.
2. **Tier-vs-config check.** For every T2 tool in §3, the corresponding `.mcp.json` / `settings.json` configuration requires per-use confirmation. For every T3 tool, the tool is not registered on the agent (the host runs the CLI invocation). For every T∞ tool, the server is configured in a way that does not expose it (filter/allow-list in the server config, or the server is not installed at all).
3. **Audit script runs.** `scripts/audit/check_mcp_config.py` fails on a hand-crafted bad config (a server not in §3, a T2 tool without per-use confirmation, a T3 tool registered on the agent). CI runs it.
4. **Credentials check.** Each MCP server's credentials are in the team's secret manager, not in any committed file. Rotation cadence is on each server owner's calendar.
5. **Session log review.** Pull five recent agent-session transcripts. For each tool call, assign a tier from §3; confirm the gate recorded in the session matches the required gate (per-use confirm shown and accepted, or human-invocation performed). Deviations are either policy gaps or client misconfig.
6. **Exfiltration path dry-run.** Construct a prompt that (a) causes a T1 read from an untrusted source containing an instruction to post to Slack, then (b) attempts to trigger a T2/T3 write on that same instruction. Confirm the gate blocks it (per-use confirm shown; human rejects). Document the test as part of this file's revision history.
7. **Deprovisioning drill.** Simulate a team member leaving. Confirm all MCP identities they created are rotated within 24h. Capture the runbook step in `docs/offboarding.md` if not already there.
8. **Length check.** `docs/MCP_POLICY.md` under ~400 lines. Longer than that and the quarterly review becomes a chore and gets skipped.
9. **Cross-link check.** The delegation contract §5 (`AGENTS.md`) references this policy; this policy references the delegation contract. Breaking the link in either direction creates drift.

## 5. Rubric

Grade a submitted policy 0–3 per row.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Tier model** | Allow/deny only | 3 tiers | 4 tiers with gates | 4 tiers + T∞ (not-installed) + rationale per tier |
| R2 | **Per-tool granularity** | Server-level only | Some tools, some servers | Per-tool for all mutating servers | Per-tool across all registered servers |
| R3 | **HITL gate mechanics** | None | Named in prose only | Gates enforced in client config | Gates enforced + session-level bypass disallowed + audited |
| R4 | **Untrusted-input handling** | None | Mentioned in passing | Named as session-wide state | Named + explicit read-then-write loop break + example |
| R5 | **Credentials coverage** | Not in policy | Rotation cadence named | Owner + cadence + secret-manager | Owner + cadence + secret-manager + deprovisioning drill |
| R6 | **Allow-list completeness** | Prose only, no table | Table for 1 server | Table for every configured server | Table for every configured server + "no other server installed" statement |
| R7 | **Promotion / demotion asymmetry** | Symmetric | Loosen vs. tighten noted | Loosen ADR-gated, tighten fast | Loosen ADR-gated + tighten fast + writeup window + mirrors delegation contract |
| R8 | **Enforcement arm** | None | Audit script named | Audit script runs locally | Audit script runs in CI + fails for bad config |
| R9 | **Known dead-ends** | None | 1–2 items | 3+ items with reasons | 3+ items + mirrors the historical misses the policy fixes |
| R10 | **Revision cadence & trigger** | None | Cadence | Cadence + one trigger | Cadence + MCP-incident trigger + MCP-release trigger + membership trigger |

**Pass bar: 22/30.** Below that, iterate before publishing.

## 6. Common mistakes

- **Tiering by server, not by tool.** Blanket-allowing "the GitHub MCP" accidentally allows `merge_pull_request` and `delete_branch` the first time the model wants to use them.
- **Only two tiers (allow / deny).** Collapses the most useful gate (per-use confirmation) into "allow" and then the team wonders why an agent merged a PR at 2am.
- **Session-level "allow all T2 for this session" bypass.** The one convenience toggle that eats every other control. If the policy doesn't call it out, teams enable it.
- **Treating untrusted content as a content-filter problem.** Content filters miss prompt injection. The architectural control is to not have a tool call cross the T1→T2/T3 boundary in a single unattended loop.
- **No T∞ "not installed" category.** Everything is theoretically denied and nothing is architecturally impossible. The strongest control is removing the tool from the surface.
- **Policy in Markdown with no config enforcement.** Six months in, the Markdown and the `.mcp.json` disagree, and nobody notices until the incident.
- **No named owner per server.** The server that nobody owns is the server whose credentials never rotate.
- **Omitting the data-exfiltration path.** A read-tool and a write-tool individually look safe; the pair is the issue. If §4 is missing, the policy has a known hole.
- **No deprovisioning drill.** When a team member leaves, their PAT keeps working until someone remembers. The drill forces the step into offboarding.
- **Policy that's longer than the audit script.** If the policy is 2,000 words and the audit script is one line, enforcement is theatre.

## 7. References

- [Model Context Protocol specification](https://modelcontextprotocol.io/) — the open protocol this policy regulates; defines servers, tools, resources, and prompts.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the risk framing for the untrusted-input section and for the secrets / credentials handling requirements.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-technique taxonomy; used to sanity-check that the untrusted-input / exfiltration-path section (§4) covers the relevant categories of adversary-controlled prompt content.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — Govern (1.1, 2.1, 4.1) motivates the named-owner, cadence, and enforcement requirements; Map (3.1, 3.2) motivates the per-tool risk tiering.

<!-- follow-up: once the audit script has been running in CI for a quarter, add a second check that walks the server owner list and verifies each named owner is still on the team (catches stale ownership). -->
