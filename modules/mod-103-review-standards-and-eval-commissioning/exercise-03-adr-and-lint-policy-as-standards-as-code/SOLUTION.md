# Exercise 03 — The ADR Template and Lint-Policy Package as Standards-as-Code

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **implementation** (committable template + lint rules) + **design** (rationale + rubric)

## 1. Solution overview

"Standards-as-code" means the team's standards are enforceable by machines, not
just invoked in retrospectives. This exercise produces two linked artifacts:

- An **ADR (Architectural Decision Record) template** that is short enough to be
  written in-flow, rigorous enough that reading it six months later tells you
  *why* a choice was made, and structured enough to be grepped, counted, and
  cross-referenced. ADRs are themselves a standards-as-code mechanism: the
  binding decisions a team has made live in the repo, versioned, next to the
  code they govern.
- A **lint-policy package** — a pre-commit / CI-runnable set of rules that
  enforce the subset of those standards that *can* be enforced mechanically
  (file layout, ADR presence, forbidden imports, prompt-template location,
  secret scanning).

The pair is the deliverable: a template that captures *judgement* decisions and a
lint package that enforces the *mechanical* consequences of those decisions. The
template without lint rots (nobody checks it). Lint without a template produces
"why is this rule here?" every six months.

Design decisions this solution encodes:

1. **ADRs are Markdown with YAML frontmatter.** Frontmatter means status,
   date, owner, and supersedes-chain are machine-readable without a parser.
2. **Status is a closed set, not freeform.** `proposed | accepted |
   deprecated | superseded`. The lint rule rejects unknown values.
3. **Each ADR owns one decision.** Two ADRs cover two decisions. Monster ADRs
   rot — people stop writing them. One-decision-per-file also makes the
   `supersedes:` chain readable.
4. **Lint rules are opinionated but each has a `why`.** Lint rules exist to
   close the loop between an ADR ("we don't use Redis") and the code
   ("imports from `redis` are forbidden").
5. **Lint is *pre-commit* first, CI second.** Pre-commit catches the mistake
   before it enters the PR. CI is the backstop.
6. **The package is runnable on a plain Python repo without new runtimes.**
   The solution uses `ruff` + `pre-commit` + a single Python validator for
   ADR frontmatter. No new language, no new build tool.

## 2. Implementation — the artifacts

### 2.1 Directory layout

```
docs/adrs/
  README.md
  0000-template.md
  0001-record-architecture-decisions.md
  0002-chosen-python-version.md
  ...

.pre-commit-config.yaml
pyproject.toml                 # ruff config
scripts/
  lint_adrs.py                 # ADR frontmatter + supersedes-chain validator
  forbidden_imports.py         # ad-hoc rule for ADR-tied bans (e.g. "no redis")
```

### 2.2 ADR template — `docs/adrs/0000-template.md`

```markdown
---
# ADR frontmatter — all fields required unless marked optional.
id: 0000                           # four-digit, matches filename prefix
title: Short noun phrase           # the decision itself, not the problem
status: proposed                   # proposed | accepted | deprecated | superseded
date: 2026-11-11                   # ISO-8601, date the status was last changed
owner: team-manager-copilot        # team or named individual
tags: []                           # free-form; useful: [security, llm, infra]
supersedes: null                   # null or four-digit id of the ADR this replaces
superseded_by: null                # null or four-digit id of the ADR that replaces this
---

# ADR-0000: {{ title }}

## Context

What situation forced this decision? Describe the forces at play: user need,
constraint, incident, cost, performance target, legal requirement. Write
enough that a new engineer in six months can read this section alone and
understand why you had to choose.

Keep it to 3–8 sentences. If it is longer, you are probably describing a
*project*, not a *decision* — split.

## Decision

State the decision in one sentence, in the active voice. "We will …" or
"We won't …". If the decision has sub-parts, list them as bullets. Do not
argue for the decision here; that is the next section.

## Consequences

What follows from this decision? Include:

- **Positive consequences**: what we gain.
- **Negative consequences**: what we give up or pay for.
- **Follow-up work**: ADRs that must be written, code that must be deleted,
  lint rules that must be added. Link them if they already exist.
- **Reversal cost**: if we had to undo this in 12 months, how expensive?

## Alternatives considered

At least one alternative per decision, with a one-paragraph *why not*. If no
alternatives exist the decision was probably not a decision — question
whether this ADR is needed.

## References

Links to issues, incidents, prior art, external docs, benchmarks. One line
per reference.
```

### 2.3 Sample worked ADR — `docs/adrs/0019-removed-redis.md`

```markdown
---
id: 0019
title: Removed Redis as a request-caching layer
status: accepted
date: 2026-03-14
owner: team-manager-copilot
tags: [infra, perf]
supersedes: 0007
superseded_by: null
---

# ADR-0019: Removed Redis as a request-caching layer

## Context

ADR-0007 introduced Redis to cache weekly-rollup queries. Over Q4 we observed
three distinct incidents in which stale cache entries produced visibly wrong
rollups to the manager UI. Average query latency without the cache was 180ms
at p95; the user-perceived cost of correctness bugs outweighed the latency
benefit.

## Decision

We will remove Redis from the request path. All rollup queries hit Postgres
directly. The `redis` client dependency is removed from `pyproject.toml`.

## Consequences

- **Positive**: one fewer moving part; no invalidation bugs; simpler deploy
  (fewer Helm charts); lower attack surface.
- **Negative**: p95 latency on the rollup endpoint increases from ~40ms
  (cache hit) to ~180ms. We accept this.
- **Follow-up work**: remove Redis entries from Terraform (tracked in
  INFRA-412); add a lint rule banning `redis` imports.
- **Reversal cost**: medium. We'd need to re-provision, re-add the client,
  and solve the invalidation issue that forced this ADR. Not trivial, which
  is the point.

## Alternatives considered

- **Keep Redis, add explicit invalidation hooks on every write path.**
  Rejected: we attempted this and found the invalidation surface area grew
  with every new query shape. Too fragile.
- **Keep Redis but drop TTL to 30 seconds.** Rejected: measured latency win
  disappeared; user-observable staleness window remained non-zero.

## References

- INC-2026-11 (Nov 2026 rollup incident)
- INC-2026-18 (Dec 2026 rollup incident)
- ADR-0007 (introduced Redis — superseded by this ADR)
- Grafana query-latency dashboard, Q4 (<internal link>)
```

### 2.4 `scripts/lint_adrs.py` — ADR frontmatter validator

```python
#!/usr/bin/env python3.11
"""Lint the docs/adrs/ directory.

Checks:
  1. Every .md file (except README.md) has valid YAML frontmatter.
  2. Required frontmatter fields are present with valid values.
  3. The four-digit prefix in the filename matches the `id:` field.
  4. Status is one of: proposed, accepted, deprecated, superseded.
  5. supersedes / superseded_by chain is consistent (symmetric, no cycles).
  6. Only one ADR holds a given id.

Exits non-zero on any failure. Intended as a pre-commit hook and CI check.
"""
from __future__ import annotations

import re
import sys
from dataclasses import dataclass
from pathlib import Path

import yaml  # PyYAML, listed in dev dependencies

ADR_DIR = Path("docs/adrs")
FRONTMATTER_RE = re.compile(r"\A---\n(.*?)\n---\n", re.DOTALL)
VALID_STATUSES = {"proposed", "accepted", "deprecated", "superseded"}
REQUIRED_FIELDS = {"id", "title", "status", "date", "owner", "tags",
                   "supersedes", "superseded_by"}
ISO_DATE_RE = re.compile(r"\A\d{4}-\d{2}-\d{2}\Z")


@dataclass
class Adr:
    path: Path
    frontmatter: dict


def load_adrs() -> list[Adr]:
    adrs: list[Adr] = []
    for path in sorted(ADR_DIR.glob("*.md")):
        if path.name in {"README.md"}:
            continue
        text = path.read_text(encoding="utf-8")
        m = FRONTMATTER_RE.match(text)
        if not m:
            fail(f"{path}: missing YAML frontmatter")
        try:
            data = yaml.safe_load(m.group(1)) or {}
        except yaml.YAMLError as e:
            fail(f"{path}: invalid YAML frontmatter: {e}")
        if not isinstance(data, dict):
            fail(f"{path}: frontmatter must be a mapping")
        adrs.append(Adr(path=path, frontmatter=data))
    return adrs


def check_fields(adr: Adr) -> list[str]:
    errors: list[str] = []
    fm = adr.frontmatter
    missing = REQUIRED_FIELDS - fm.keys()
    if missing:
        errors.append(f"missing required fields: {sorted(missing)}")
    # id matches filename prefix
    expected_id = int(adr.path.stem.split("-", 1)[0])
    if fm.get("id") != expected_id:
        errors.append(f"id={fm.get('id')!r} does not match filename prefix "
                      f"{expected_id!r}")
    # status
    if fm.get("status") not in VALID_STATUSES:
        errors.append(
            f"status={fm.get('status')!r} not in {sorted(VALID_STATUSES)}")
    # date
    date = fm.get("date")
    if not (isinstance(date, str) and ISO_DATE_RE.match(date)):
        errors.append(f"date={date!r} is not ISO-8601 YYYY-MM-DD")
    # supersedes/superseded_by must be None or int
    for field in ("supersedes", "superseded_by"):
        val = fm.get(field)
        if val is not None and not isinstance(val, int):
            errors.append(f"{field}={val!r} must be null or int")
    return errors


def check_chain(adrs: list[Adr]) -> list[str]:
    errors: list[str] = []
    by_id: dict[int, Adr] = {}
    for adr in adrs:
        adr_id = adr.frontmatter.get("id")
        if not isinstance(adr_id, int):
            continue  # caught by check_fields
        if adr_id in by_id:
            errors.append(f"duplicate id {adr_id} in {adr.path} and "
                          f"{by_id[adr_id].path}")
            continue
        by_id[adr_id] = adr

    # symmetry: if A supersedes B, B.superseded_by must be A.
    for adr in adrs:
        supers = adr.frontmatter.get("supersedes")
        if supers is None:
            continue
        other = by_id.get(supers)
        if other is None:
            errors.append(f"{adr.path}: supersedes={supers!r} but no such ADR")
            continue
        back = other.frontmatter.get("superseded_by")
        if back != adr.frontmatter.get("id"):
            errors.append(
                f"{adr.path}: supersedes {supers} but "
                f"{other.path} has superseded_by={back!r}")

    # cycles: supersedes forms a DAG terminating at proposed/accepted roots.
    visited: dict[int, str] = {}  # id -> 'visiting' | 'done'
    def dfs(node: int, trail: list[int]) -> None:
        state = visited.get(node)
        if state == "done":
            return
        if state == "visiting":
            errors.append(f"supersedes cycle involving ids {trail + [node]}")
            return
        visited[node] = "visiting"
        other = by_id.get(node)
        if other:
            supers = other.frontmatter.get("supersedes")
            if isinstance(supers, int):
                dfs(supers, trail + [node])
        visited[node] = "done"
    for adr in adrs:
        adr_id = adr.frontmatter.get("id")
        if isinstance(adr_id, int):
            dfs(adr_id, [])
    return errors


def fail(msg: str) -> None:
    print(f"ERROR: {msg}", file=sys.stderr)
    sys.exit(1)


def main() -> int:
    if not ADR_DIR.is_dir():
        fail(f"{ADR_DIR} does not exist")
    adrs = load_adrs()
    all_errors: list[tuple[Path, str]] = []
    for adr in adrs:
        for err in check_fields(adr):
            all_errors.append((adr.path, err))
    for err in check_chain(adrs):
        all_errors.append((ADR_DIR, err))
    if all_errors:
        for path, err in all_errors:
            print(f"{path}: {err}", file=sys.stderr)
        return 1
    print(f"OK: {len(adrs)} ADR(s) validated.")
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

### 2.5 `scripts/forbidden_imports.py` — ADR-tied import ban

```python
#!/usr/bin/env python3.11
"""Fail on imports that are forbidden by an accepted ADR.

Each entry maps a top-level module name to the ADR that forbids it. The
error message must cite the ADR so engineers know *why* the rule exists.
"""
from __future__ import annotations

import ast
import sys
from pathlib import Path

FORBIDDEN = {
    "redis": "docs/adrs/0019-removed-redis.md",
    # Add more as ADRs accrete. Keep the right-hand path accurate.
}


def imports_in(path: Path) -> set[str]:
    try:
        tree = ast.parse(path.read_text(encoding="utf-8"))
    except SyntaxError:
        return set()
    names: set[str] = set()
    for node in ast.walk(tree):
        if isinstance(node, ast.Import):
            for alias in node.names:
                names.add(alias.name.split(".", 1)[0])
        elif isinstance(node, ast.ImportFrom) and node.module:
            names.add(node.module.split(".", 1)[0])
    return names


def main(argv: list[str]) -> int:
    if len(argv) <= 1:
        return 0
    bad: list[tuple[Path, str, str]] = []
    for arg in argv[1:]:
        path = Path(arg)
        if path.suffix != ".py" or not path.is_file():
            continue
        for mod in imports_in(path) & FORBIDDEN.keys():
            bad.append((path, mod, FORBIDDEN[mod]))
    if bad:
        for path, mod, adr in bad:
            print(f"{path}: import of forbidden module {mod!r} "
                  f"(see {adr})", file=sys.stderr)
        return 1
    return 0


if __name__ == "__main__":
    raise SystemExit(main(sys.argv))
```

### 2.6 `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: detect-private-key
      - id: check-added-large-files

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: local
    hooks:
      - id: lint-adrs
        name: Lint ADR frontmatter and supersedes chain
        entry: python scripts/lint_adrs.py
        language: system
        pass_filenames: false
        files: ^docs/adrs/.*\.md$

      - id: forbidden-imports
        name: Fail on ADR-forbidden imports
        entry: python scripts/forbidden_imports.py
        language: system
        pass_filenames: true
        types: [python]
```

### 2.7 `pyproject.toml` — ruff section (abbreviated)

```toml
[tool.ruff]
line-length = 100
target-version = "py311"

[tool.ruff.lint]
select = [
  "E", "F", "W",        # pycodestyle + pyflakes
  "I",                  # isort
  "B",                  # bugbear
  "S",                  # bandit — security
  "UP",                 # pyupgrade
  "RUF",                # ruff-specific
  "SIM",                # simplify
  "T20",                # no print()
  "DTZ",                # datetime: enforce tz-aware
]
ignore = [
  "E501",               # line length handled by formatter
]

[tool.ruff.lint.per-file-ignores]
"tests/**" = ["S101"]   # asserts allowed in tests

[tool.ruff.lint.flake8-bugbear]
extend-immutable-calls = ["pydantic.Field"]
```

### 2.8 `docs/adrs/README.md`

```markdown
# Architecture Decision Records

Every non-trivial decision that will outlast a single PR is captured here.

## Writing a new ADR

1. Copy `0000-template.md` to a new file: next free four-digit id, title
   slugged. Example: `0023-adopt-promptfoo-for-prompt-evals.md`.
2. Fill in frontmatter and sections. Status starts at `proposed`.
3. Open a PR tagged `adr`. Reviewers: at minimum one other IC and the
   team-practice lead.
4. On merge with approvals, flip status to `accepted` as a follow-up
   commit (or in the same PR if everyone agreed in review).

## Deprecating or superseding

- To deprecate: set status to `deprecated`, update `date`, leave in place.
- To supersede: write the new ADR; set its `supersedes:` to the old id;
  set the old ADR's `superseded_by:` to the new id; set the old ADR's
  status to `superseded`.

The lint rule in `scripts/lint_adrs.py` checks the chain is symmetric.
```

## 3. Validation steps

1. **Unit-test the validator.** Create fixtures in `tests/unit/test_lint_adrs/`
   covering: valid ADR, missing frontmatter, bad status, mismatched id,
   broken supersedes chain, cycle. Run `pytest tests/unit/test_lint_adrs/`.
2. **Dry-run on the real corpus.** `python scripts/lint_adrs.py` should pass on
   the existing ADRs. If it fails, fix the ADRs — the whole point is that
   non-conforming historical ADRs become visible.
3. **Dry-run forbidden imports.** Add a scratch file that imports `redis`.
   Run `python scripts/forbidden_imports.py scratch.py`. Must fail with the
   ADR path in the error message. Delete the scratch file.
4. **Pre-commit smoke test.** `pre-commit install && pre-commit run --all-files`.
   Confirm each hook runs and no false positives fire.
5. **CI parity.** The CI config (e.g. `.github/workflows/lint.yml`) runs the
   same hooks. Verify the two paths produce the same result on at least one
   intentionally bad commit.
6. **Template fitness.** Ask a random IC to write a real ADR from the
   template without guidance. If they ask the same clarifying question a
   second IC also asked, the template needs another clarifying sentence.
7. **Grep the frontmatter.** Confirm you can grep the ADRs — e.g.
   `grep -l 'status: accepted' docs/adrs/*.md` — and that tagging
   (`grep -l 'tags: \[.*security.*\]'`) actually works across the corpus.

## 4. Rubric

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **ADR template completeness** | Missing Context / Decision / Consequences | Has 3 sections | Has 4 sections | Context, Decision, Consequences, Alternatives, References — all with guidance |
| R2 | **Frontmatter machine-readability** | Freeform or none | YAML, but no validator | YAML + validator enforces required fields | YAML + validator enforces required fields + chain + unique id |
| R3 | **Status discipline** | None | Freeform status | Closed set, not enforced | Closed set, enforced by lint |
| R4 | **Supersedes/superseded_by** | Not present | Free text | Fields exist; symmetric check by hand | Fields exist; automated symmetric + acyclic check |
| R5 | **Worked example ADR** | None | Toy example | Real-looking example, one decision | Real-looking example that itself passes the lint, with one-decision scope |
| R6 | **Lint package scope** | Formatter only | Formatter + style lint | Formatter + style + ADR validator | Formatter + style + security lint (bandit/S) + ADR validator + ADR-tied bans |
| R7 | **Pre-commit AND CI** | Neither | One or the other | Both, but divergent configs | Both, same hooks, same versions pinned |
| R8 | **Each lint rule justifies itself** | No rationale | Rationale in a design doc elsewhere | Rationale in comments | Rationale in error messages (the rule *tells the engineer* why it fired, e.g. cites the ADR) |
| R9 | **Maintenance ergonomics** | No README | Template-copy README | README with full lifecycle guidance | README with lifecycle + deprecation + supersede flow + lint pointer |

**Pass bar: 20/27.**

## 5. Common mistakes

- **ADRs as blog posts.** Three-page, two-decision, retrofit-after-the-fact essays are not ADRs. One decision, short, written close to the decision.
- **Status as free text.** `in-progress`, `tbd`, `wip` — the lint has nothing to grab onto. Keep a closed set.
- **No `supersedes` chain.** A standards-as-code repo without the chain degenerates into a pile of contradictory accepted ADRs. The chain is what makes "what do we currently believe?" grepable.
- **Lint rules with no `why`.** A failing lint that says "no." makes engineers suppress rather than learn. Every rule message should name the ADR, the incident, or the external standard that motivated it.
- **Letting the formatter and the style linter fight.** Pick one of (formatter does line length, linter does logic), configure consistently, pin the versions.
- **No security lint.** Omitting bandit / ruff `S` rules on a service that handles PII is a governance gap. Ship it from day one and allowlist noisily, not silently.
- **Pre-commit without CI backstop.** Engineers can skip local hooks. CI is what makes the rule binding.
- **Treating `ruff --fix` as a migration tool.** `--fix` is for mechanical cleanups. Sweeping fixes that change semantics belong in their own PR with an ADR.
- **Forgetting the human loop.** Standards-as-code encodes the *mechanical* consequences of standards. The *judgement* layer is still human — code review, ADR review, the review checklist (Exercise 01).

## 6. References

- [Michael Nygard, "Documenting Architecture Decisions" (2011)](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) — the original ADR format this template adapts.
- [adr.github.io](https://adr.github.io/) — community-maintained reference to the MADR variant and multiple published ADR implementations.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function's **Govern 1.2** (policies and procedures) and **Govern 4.2** (teams document AI risks) motivate the standards-as-code formalism.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the lint package's security rules (bandit via `ruff` `S` rules) protect against classes of issue that intersect with ML03 (Model Inversion), ML06 (AI Supply Chain), and others in the ML Top 10.
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — LLM03 (Supply Chain) and LLM05 (Supply-Chain / Improper Output Handling) motivate the forbidden-imports mechanism and the pinned-versions discipline.
- [MITRE ATLAS](https://atlas.mitre.org/) — used to validate that the lint package addresses adversarial-ingress categories (unvetted dependencies, credentials-in-code).
- [Ruff documentation](https://docs.astral.sh/ruff/) — the official docs for the `ruff` configuration used above.
- [pre-commit documentation](https://pre-commit.com/) — hook configuration reference.
