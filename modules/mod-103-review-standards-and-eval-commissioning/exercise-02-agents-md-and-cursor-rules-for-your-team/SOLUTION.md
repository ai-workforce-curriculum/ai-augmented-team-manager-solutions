# Exercise 02 — AGENTS.md and Cursor Rules for Your Team

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **implementation** (runnable repo artifacts) + **design** (decision rationale + rubric)

## 1. Solution overview

The deliverable is two repo-committed files that encode your team's *standards-as-code* for AI coding assistants:

- `AGENTS.md` — a repo-root instruction file read by assistants that honour the `AGENTS.md` convention (e.g., OpenAI Codex-CLI-style agents, Claude Code, Continue, Aider, and others). It is the **portable** surface — the file you'd expect any new assistant to read when you drop it into your repo.
- `.cursor/rules/*.mdc` — Cursor-specific rules files (either project rules or always-apply rules). These attach to Cursor's agent / chat / inline edit features and support MDC frontmatter for scoping (`alwaysApply`, `globs`, `description`).

The design goal is that **a new engineer on the team and a new AI assistant get the same operating manual.** Where Cursor rules diverge from `AGENTS.md`, it should be only because Cursor exposes features (glob scoping, `@`-referenced files) that other tools don't.

Design decisions this solution encodes:

1. **Single source of truth, light forking.** The team maintains one authoritative `AGENTS.md`. The Cursor rules are **thin pointers** into `AGENTS.md` plus the Cursor-specific scoping (globs, `@file` includes). This avoids drift.
2. **Instructions are *checkable*, not aspirational.** Every rule should be verifiable by reading the diff ("tests live under `tests/`, imports use absolute paths from `src/`") rather than a value statement ("write high-quality code").
3. **The file says what the agent cannot know from the repo layout.** Anything an assistant could reasonably infer from `package.json`, `pyproject.toml`, `tsconfig.json`, or 20 lines of existing code *does not go in the file*. Repetition trains the agent to tune out.
4. **Explicitly scope the file's authority.** Items are tagged as **must** (do not merge if violated), **should** (default unless justified), **avoid** (known dead-end the team has already tried).
5. **Keep it under ~400 lines.** Longer files exceed many assistants' effective context budget for standing instructions and quietly get truncated.

## 2. Implementation — the artifacts

### 2.1 `AGENTS.md` (committed at repo root)

```markdown
# AGENTS.md — Operating instructions for AI coding agents

This file is the authoritative instruction set for any AI assistant working in
this repository. It is read by Claude Code, Codex, Continue, Aider, and Cursor
(via `.cursor/rules/`). Human engineers: this file doubles as the team's
contributor guide for AI-assisted work.

## 1. What this repository is

A FastAPI service that exposes the `team-manager-copilot` API: ticket intake,
weekly rollups, one-pager drafts. Primary language: Python 3.11. Secondary:
TypeScript (admin UI under `web/`).

- Production target: Kubernetes, Linux x86_64. Do not introduce platform-
  specific code (macOS-only shell tools, Windows paths).
- Line-of-business data flows through this service. Treat every log line, DB
  query, and prompt template as potentially carrying PII.

## 2. Non-negotiables (must)

Violating any of these should block the PR regardless of what else is good:

- **No secrets, credentials, API keys, or customer identifiers in code,
  comments, test fixtures, prompt templates, or sample JSON.** Use the
  `tests/fixtures/scrubbed/` directory and `scripts/scrub.py` helper.
- **Do not weaken auth, authz, or rate-limit code without a linked ADR.** See
  `docs/adrs/` for the format.
- **Do not add dependencies without updating `pyproject.toml`'s pinned
  versions AND regenerating `requirements.lock`.** Run `make lock`.
- **Do not disable tests, lints, or type checks to make a commit green.** Fix
  the underlying issue or explain in the PR description why the suppression
  is justified.
- **Do not `git push --force` any branch starting with `main`, `release/`,
  or `prod/`.**

## 3. How the code is laid out (what you do not need to re-discover)

- `src/team_manager/api/` — FastAPI routers. One file per resource.
- `src/team_manager/domain/` — pure business logic. No I/O. Tests here are
  fast and run on every change.
- `src/team_manager/adapters/` — DB, LLM, external HTTP. Mock these in unit
  tests; integration-test them in `tests/integration/`.
- `src/team_manager/prompts/` — prompt templates. Changes here require an
  eval run (see §7).
- `web/` — Next.js admin UI. Separate `pnpm` workspace.
- `tests/unit/`, `tests/integration/`, `tests/evals/` — the three test tiers.
- `scripts/` — operator scripts. Shell is `bash`; Python scripts use
  `#!/usr/bin/env python3.11`.

## 4. How we write Python (should)

- Imports are absolute from `team_manager.*`. No relative imports beyond
  single-dot inside a package module.
- Public functions have type hints. `from __future__ import annotations` is
  at the top of every module to enable PEP 604 unions on Python 3.11.
- Prefer dataclasses or `pydantic.BaseModel` over bare dicts for anything
  that crosses a module boundary.
- Prefer explicit exceptions over returning `None` to signal failure. We
  catch at the router layer, not inside `domain/`.
- Tests use `pytest`. One behaviour per test. Fixture scope is `function`
  unless there is a measured reason to share.

## 5. How we write TypeScript (should)

- Strict TypeScript (`"strict": true`, `noUncheckedIndexedAccess": true`).
- React components in `web/app/`; shared types in `web/lib/types.ts`.
- No `any`. If you need an escape, use `unknown` plus a narrowing check.
- Fetch wrappers live in `web/lib/api/`. Components do not call `fetch`
  directly.

## 6. Things the team has tried and will not re-try (avoid)

Agents: do not propose any of these as refactors.

- **Switching the ORM.** We moved from SQLAlchemy to a thin asyncpg wrapper
  deliberately (see `docs/adrs/0012-move-off-orm.md`). Proposing a new ORM
  needs an ADR *and* a conversation, not a drive-by refactor.
- **Adding Redis for request caching.** We tried; latency gains were lost to
  cache-invalidation bugs. See `docs/adrs/0019-removed-redis.md`.
- **Introducing a global middleware for "AI safety" that wraps every LLM
  call.** We prefer explicit wrappers at the call site. See
  `docs/adrs/0024-no-llm-middleware.md`.

## 7. If you change a prompt template or a model (must)

- Open the PR against `src/team_manager/prompts/` and include the associated
  eval run id (`tests/evals/<suite>/run-<id>.json`) in the PR description.
- No prompt change ships without a passing promptfoo / eval-suite run.
  See `tests/evals/README.md` for how to run.
- If the change is a model swap (e.g. `claude-sonnet-4-5` →
  `claude-sonnet-4-6`), name the before/after model in the PR title.

## 8. Commit and PR etiquette (should)

- Conventional-commits prefixes: `feat:`, `fix:`, `chore:`, `docs:`, `test:`,
  `refactor:`.
- PR description includes **why**, not just **what**. Link the ticket.
- If an AI assistant drafted the PR, disclose it. Use the review checklist
  at `.github/PULL_REQUEST_TEMPLATE.md` (that template lives in Exercise 01).

## 9. How you (the agent) should behave

- Do not invent library APIs. If you need a function and are not sure it
  exists, open the source or run `grep` instead of guessing.
- Do not propose large refactors when asked to fix a bug. Fix the bug.
  Suggest the refactor in the PR description instead.
- Prefer short, scoped commits. If a task touches more than ~150 lines
  across more than 3 directories, stop and ask whether to split.
- When uncertain about a design decision, write the ADR draft (see Exercise
  03) and ask for review rather than picking.
- You may read any file in this repo. You may not read `.env`, `secrets/`,
  or files matched by `.gitignore` beyond what `git ls-files` reports.

## 10. How to update this file

- This file is reviewed quarterly by the team's AI-practice lead.
- Proposed changes go through PR review like any code file.
- An item that has been in "avoid" for > 12 months should be re-evaluated,
  not kept forever.
```

### 2.2 `.cursor/rules/00-agents-pointer.mdc`

```mdc
---
description: Points Cursor at the repo's canonical AGENTS.md instructions.
alwaysApply: true
---

The authoritative operating instructions for this repo live in
`@AGENTS.md`. Read and apply that file for every task in this repo.

Cursor-specific scoping below adds glob-targeted rules; it does not
override `AGENTS.md`. When the two conflict, escalate to the human
reviewer rather than silently resolving — this signals the rules file
needs to be updated.
```

### 2.3 `.cursor/rules/10-python.mdc`

```mdc
---
description: Python coding conventions for src/team_manager/**.
globs:
  - src/team_manager/**/*.py
  - tests/**/*.py
---

Apply the Python section of `@AGENTS.md`. In particular:

- Absolute imports only. No relative imports beyond single-dot inside a
  package.
- `from __future__ import annotations` at the top of every module.
- Public functions need type hints.
- Prefer `pydantic.BaseModel` or dataclasses for inter-module data.
- Tests: `pytest`, one behaviour per test, fixture scope `function`
  unless justified.
- When adding a dependency, update `pyproject.toml` AND run `make lock`.

Do not introduce a new async framework (asyncio, trio, anyio) — we are
on asyncio only. See `docs/adrs/0008-asyncio-only.md`.
```

### 2.4 `.cursor/rules/20-typescript.mdc`

```mdc
---
description: TypeScript / Next.js conventions for the web/ admin app.
globs:
  - web/**/*.ts
  - web/**/*.tsx
---

Apply the TypeScript section of `@AGENTS.md`. In particular:

- Strict TS. No `any`. Use `unknown` with narrowing instead.
- React components in `web/app/`. Shared types in `web/lib/types.ts`.
- API calls go through wrappers in `web/lib/api/` — components do not
  call `fetch` directly.
- Prefer server components where possible; mark client components with
  `"use client"` at the top.
```

### 2.5 `.cursor/rules/30-prompts-and-evals.mdc`

```mdc
---
description: Rules for changes to prompt templates or LLM-facing code.
globs:
  - src/team_manager/prompts/**
  - src/team_manager/adapters/llm.py
  - tests/evals/**
---

Any change matched by these globs is an eval-gated change (see §7 of
`@AGENTS.md`).

Before proposing a diff:

1. If you modify a template file, locate the eval suite under
   `tests/evals/<suite>/` that covers it. If you cannot find one, say so
   and propose adding one rather than silently shipping.
2. If you modify a model identifier, update the config file AND the
   relevant eval baseline note (`tests/evals/<suite>/BASELINE.md`).
3. Do not add a new tool call, function call, or retrieval source
   without updating the prompt-injection section of the review
   checklist and the OWASP LLM mapping (see Exercise 06).
```

### 2.6 `.cursor/rules/90-do-not.mdc`

```mdc
---
description: Known dead-ends the team has already explored. Do not re-propose.
alwaysApply: true
---

These are closed questions. Do not propose them as refactors or
improvements; if you have a reason to re-open, write an ADR (see
Exercise 03) and ask for review.

- Do not reintroduce a general-purpose ORM. See
  `docs/adrs/0012-move-off-orm.md`.
- Do not introduce Redis or any shared cache. See
  `docs/adrs/0019-removed-redis.md`.
- Do not wrap every LLM call in a global "safety middleware". See
  `docs/adrs/0024-no-llm-middleware.md`.
- Do not migrate to an alternate async framework. We are asyncio-only.
```

## 3. Validation steps

1. **Repo placement check.** Both files sit at the paths assistants will look for:
   - `AGENTS.md` at the repository root (same level as `README.md`, not inside `docs/`).
   - `.cursor/rules/*.mdc` with MDC frontmatter — Cursor requires the `.mdc` extension and parses `alwaysApply`, `globs`, and `description` from frontmatter.
2. **Static lint of frontmatter.** For each `.mdc` file, confirm:
   - the YAML frontmatter block parses (three-dash fence top and bottom),
   - `alwaysApply` is `true` xor `globs` is set (never both; never neither),
   - all glob patterns match at least one real file in the repo. A script:
     ```bash
     for f in .cursor/rules/*.mdc; do
       python3 scripts/validate_mdc.py "$f" || exit 1
     done
     ```
3. **Pointer consistency check.** Grep for every claim in a `.cursor/rules/` file that references a line in `AGENTS.md`. If `AGENTS.md` doesn't actually have that section anymore, the rule points at a ghost — fix.
4. **Dogfood on a real task.** Pick a small ticket. Give an assistant *only* the ticket and this repo. Observe whether it violates any rule. Each violation is either (a) a real bug — fix in the assistant / prompt, or (b) the rule wasn't stated clearly — fix the rule.
5. **Length check.** `wc -l AGENTS.md` should land between ~150 and ~400. Shorter: you're probably missing load-bearing items. Longer: the file is bloating and assistants will truncate.
6. **No-secrets scan.** Run your existing secrets scanner (gitleaks, trufflehog, etc.) against both files. These files often grow sample configs that drift into real credentials.
7. **Review cadence.** Add a calendar reminder to the team manager: re-review quarterly, and **after every non-trivial AI-assist-related incident.**

## 4. Rubric

Grade a submitted artifact set (one `AGENTS.md` + any Cursor rules files). Score each row 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **`AGENTS.md` placement & format** | Not at repo root; wrong name | At root, no sections | At root, section headings | At root, section headings, clearly scoped intro, under 400 lines |
| R2 | **Non-negotiable vs. preference distinction** | Flat bullet list | Implicit priority | Labelled "must/should/avoid" | Labelled + each tier has justification |
| R3 | **Repo-specific content** | Looks generic, no code paths | One or two concrete paths | Named directories, fixture conventions, lock-file commands | Named paths + commands + ADR references + language of the actual codebase |
| R4 | **Cursor rules frontmatter correctness** | Missing or broken frontmatter | Frontmatter present, no globs/alwaysApply | Frontmatter + correct scoping | Frontmatter + correct scoping + globs validated against repo |
| R5 | **Single source of truth** | Rules duplicate / contradict AGENTS.md | Rules duplicate AGENTS.md verbatim | Rules reference AGENTS.md but drift | Rules reference AGENTS.md with pointer syntax and do not duplicate load-bearing content |
| R6 | **"Avoid" / dead-end list** | None | One item | 2–3 items with ADR links | 3+ items, each with ADR link + re-evaluation cadence |
| R7 | **Prompt / eval gating** | No mention | Mentions evals in prose | Dedicated rule file for prompt-paths | Dedicated rule + glob + explicit baseline pointer |
| R8 | **Agent behaviour rules** | Not covered | "Be careful" | "Do not invent APIs" + 1 more | Invent-APIs + scope-creep + ask-on-uncertainty + reading-permission |
| R9 | **Maintenance plan** | None | "Update as needed" | Named owner, cadence | Named owner, cadence, revisit trigger (incidents) |

**Pass bar: 20/27.**

## 5. Common mistakes

- **Putting values ("care about quality") in a rules file.** Values go in team culture docs; rules files should be checkable.
- **Duplicating the contents of `AGENTS.md` inside each `.mdc` file.** The two will drift. Point at `AGENTS.md` from the rules files; keep the substance in one place.
- **Globs that match nothing.** `src/python/**/*.py` when the actual directory is `src/team_manager/`. Agents silently ignore these.
- **Both `alwaysApply: true` and `globs:` set.** Pick one. The Cursor rules convention is: `alwaysApply` is for repo-wide rules, `globs` is for path-scoped rules.
- **Treating `AGENTS.md` as a dumping ground.** Every added paragraph costs attention budget. If an item is better documented in an ADR or a README, link rather than copy.
- **No "avoid" section.** The most valuable instructions are often negative: *things the team has already tried*. Without them, you re-litigate the same refactors every quarter.
- **No disclosure or ownership.** A rules file no one owns is a rules file no one updates. Name the maintainer in `AGENTS.md`.
- **Rules that only an infra-aware reader can parse.** If a mid-level IC on your team can't read the file and act on it, an AI assistant can't either.
- **Forgetting Windows / macOS devs.** `AGENTS.md` says "we target Linux x86_64" — that line alone saves a week of cross-platform regressions.

## 6. References

- [OpenAI AGENTS.md convention](https://agents.md/) — the open specification for `AGENTS.md` adopted by multiple coding assistants. Treat it as the compatibility surface.
- [Cursor — Rules for AI documentation](https://docs.cursor.com/context/rules) — covers `.cursor/rules/*.mdc` frontmatter (`alwaysApply`, `globs`, `description`), always-applied vs. auto-attached rules, and how rules compose.
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — supplies the risk model for the prompt-and-eval gating rule (LLM01 Prompt Injection, LLM02 Insecure Output Handling, LLM06 Sensitive Information Disclosure most directly apply).
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function motivates the single-source-of-truth design and the maintenance-cadence requirement.
- [MITRE ATLAS](https://atlas.mitre.org/) — used to sanity-check that the agent-behaviour section (reading-permission scope, ask-on-uncertainty) addresses the categories of adversarial / unsafe behaviours relevant to coding assistants.
