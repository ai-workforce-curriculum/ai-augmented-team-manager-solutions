# Exercise 05 — LLM-as-Judge, Promptfoo, LangSmith: The Reading-Level Walkthrough

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **implementation** (runnable configs + walkthrough) + **design** (rationale + rubric)

## 1. Solution overview

This walkthrough stands up the eval commissioned in Exercise 04 — the reading-level eval for the weekly-summary prompt — using **Promptfoo** as the local / CI runner and **LangSmith** as the hosted trace + eval surface. It demonstrates the three evaluator types a team manager will encounter in practice:

1. **Deterministic assertions** — e.g. "the output is valid JSON", "contains no PII pattern", "token count ≤ 450". Fast, free, cannot be wrong.
2. **Programmatic metrics** — e.g. Flesch-Kincaid grade level computed in Python. Deterministic given the formula.
3. **LLM-as-judge rubric scoring** — a separate model grades outputs against a rubric. Non-deterministic; requires calibration against a small human-rated set.

The design claim is that **you want all three in one config**, because each layer catches different failure modes and each has a different cost / reliability tradeoff. Running only (3) is slow, expensive, and undercalibrated; running only (1) and (2) misses semantic failures (hallucinations, unsupported claims, bad tone).

Design decisions this solution encodes:

1. **Promptfoo for CI, LangSmith for debugging and trend-tracking.** Promptfoo is a good gate; LangSmith is a good magnifier. They complement each other, don't replace each other.
2. **Judge prompt calibrated against a small human-rated set.** Un-calibrated judges are noise. Twenty human-labelled examples is the minimum to establish a kappa signal.
3. **Thresholds in a separate config, not in the script.** Thresholds change as models drift; the config should be editable without touching the runner.
4. **Hold-out dataset never seen during prompt tuning.** This is enforced by directory layout and a CI rule: PRs that touch both `dataset/holdout/` and `prompts/` in the same commit get flagged for review.
5. **The eval is deterministic in the sense that matters.** Judge temperature fixed; dataset order fixed; thresholds versioned; judge prompt committed. Flakiness is bounded.

## 2. Implementation — the artifacts

### 2.1 Directory layout

```
tests/evals/summary/
  README.md
  promptfooconfig.yaml
  thresholds.yaml
  BASELINE.md
  dataset/
    dev/
      transcripts.jsonl          # 100 examples
    holdout/
      transcripts.jsonl          # 40 examples (DO NOT EDIT during tuning)
    calibration/
      human_labels.jsonl         # 20 examples, human-rated 1..5
  prompts/
    summary.v2.yaml              # the prompt under test
    judge.v1.yaml                # the judge prompt
  scripts/
    reading_grade.py             # deterministic Flesch-Kincaid metric
    pii_check.py                 # deterministic PII pattern check
    calibrate_judge.py           # Cohen's kappa calibration
    run_langsmith.py             # optional: push run to LangSmith
  runs/
    2026-11-10.json              # output of one real run, for review
```

### 2.2 `promptfooconfig.yaml` — the Promptfoo config

```yaml
# Reading-level eval for summary.v2.
# Run locally: `promptfoo eval -c tests/evals/summary/promptfooconfig.yaml`
description: "team-manager-copilot weekly summary — reading-level eval"

prompts:
  - file://prompts/summary.v2.yaml

providers:
  - id: anthropic:claude-sonnet-4-6
    config:
      temperature: 0.2
      max_tokens: 700

# Use hold-out for gate numbers; dev for day-to-day iteration.
tests:
  - vars:
      transcripts_file: dataset/holdout/transcripts.jsonl
    assert:
      # Layer 1 — deterministic hard gates.
      - type: javascript
        value: |
          // token count ≤ 450 (secondary metric, hard gate per thresholds.yaml)
          const tokens = output.split(/\s+/).length;
          return tokens <= 450;
      - type: python
        value: |
          # PII pattern check, pass = 1.0 across the set.
          from pii_check import has_pii
          return not has_pii(output)

      # Layer 2 — programmatic metric: reading grade level.
      - type: python
        value: |
          from reading_grade import flesch_kincaid_grade
          g = flesch_kincaid_grade(output)
          return 8.0 <= g <= 10.5

      # Layer 3 — LLM-as-judge: factual support from source transcript.
      - type: llm-rubric
        provider: anthropic:claude-opus-4-7
        value: |
          You are grading whether the SUMMARY is factually supported by
          the TRANSCRIPT. Return a JSON object: {"score": 1..5,
          "reasoning": "..."}. Score 5 = every claim in the summary is
          present in the transcript, verbatim or paraphrased. Score 1 =
          summary contains claims the transcript does not support.

          TRANSCRIPT:
          {{transcript}}

          SUMMARY:
          {{output}}
        threshold: 4

# Thresholds for the overall suite pass/fail live in thresholds.yaml.
# Promptfoo's CI hook reads that file via the wrapper script.
```

### 2.3 `thresholds.yaml` — single source of truth for pass/fail

```yaml
# Thresholds for the summary.v2 eval. Edit here, not in code.
#
# Primary gate: pass-rate on hold-out, using Wilson 95% CI lower bound.
primary:
  metric: pass_rate_holdout_wilson_lower
  min: 0.90
  on_fail: block_merge

# Secondary gates.
secondaries:
  - metric: pii_pass_rate
    min: 1.00
    on_fail: block_merge
  - metric: median_token_count
    max: 450
    on_fail: warn
  - metric: mean_reading_grade
    report_only: true

# Judgement zone: primary in [0.80, 0.90).
judgement_zone:
  min: 0.80
  max: 0.90
  owner: priya@example.com
  requires_meeting_with: design-lead
```

### 2.4 `prompts/summary.v2.yaml` — prompt under test

```yaml
# Minimal, repo-owned prompt file. Version via git, not via mutation.
system: |
  You are the team-manager-copilot. You summarise a weekly standup
  transcript into a concise written update for the team's manager.
  Write at an 8th–10th grade reading level. Do not invent people, dates,
  decisions, or outcomes. If the transcript is cut off or ambiguous,
  say so explicitly.
user: |
  TRANSCRIPT:
  {{transcript}}

  Produce a summary of the week's work. Format:
  - 1-sentence TL;DR.
  - 3–5 bullet points of what happened.
  - 1–3 bullets of risks or blockers, or "No material risks." if none.
  - A one-sentence "next week" focus.
```

### 2.5 `prompts/judge.v1.yaml` — the judge prompt (used by `llm-rubric`)

```yaml
# Judge prompt lives in version control and is calibrated against the
# calibration/ dataset. Changes to this file trigger a re-calibration run.
system: |
  You are an impartial grader. You will be given a TRANSCRIPT and a
  SUMMARY. Grade whether every claim in SUMMARY is supported by
  TRANSCRIPT. Return JSON: {"score": <1..5>, "reasoning": "<=50 words>"}.

  Scoring rubric:
    5 — Every claim in SUMMARY is present (verbatim or paraphrased) in
        TRANSCRIPT. No invented facts.
    4 — Minor paraphrase that slightly over-reaches but no material
        fabrication.
    3 — At least one claim is inferable but not clearly supported.
    2 — At least one claim contradicts the transcript OR is a material
        fabrication.
    1 — Multiple unsupported or contradictory claims.

  You must not reward brevity or writing quality — only factual support.
```

### 2.6 `scripts/reading_grade.py`

```python
"""Flesch-Kincaid grade level, for deterministic reading-level checks.

Implementation note: the Flesch-Kincaid formula is well-documented and
public-domain; the specific syllable-counting heuristic below is a
simple one that is good enough for English prose summaries. For
production, swap in a vetted library (textstat) and pin the version.
"""
from __future__ import annotations

import re

VOWEL_GROUP = re.compile(r"[aeiouy]+", re.IGNORECASE)


def count_syllables_in_word(word: str) -> int:
    word = word.lower().strip("'")
    if not word:
        return 0
    groups = VOWEL_GROUP.findall(word)
    count = len(groups)
    if word.endswith("e") and count > 1:
        count -= 1
    return max(count, 1)


def flesch_kincaid_grade(text: str) -> float:
    sentences = max(len(re.findall(r"[.!?]+", text)), 1)
    words = re.findall(r"[A-Za-z']+", text)
    if not words:
        return 0.0
    syllables = sum(count_syllables_in_word(w) for w in words)
    # Flesch-Kincaid grade level formula.
    return (0.39 * (len(words) / sentences)
            + 11.8 * (syllables / len(words))
            - 15.59)


if __name__ == "__main__":  # sanity check
    sample = ("The team shipped two features. Retention improved. "
              "We rolled back the search change.")
    g = flesch_kincaid_grade(sample)
    print(f"grade: {g:.2f}")
```

### 2.7 `scripts/pii_check.py`

```python
"""Pattern-based PII checks. Intentionally strict — false positives are
preferable to leaked PII in a public summary."""
from __future__ import annotations

import re

EMAIL_RE = re.compile(r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b")
PHONE_RE = re.compile(r"\b(?:\+?\d{1,3}[-.\s]?)?\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}\b")
# SSN-like pattern — US only. Extend for other locales.
SSN_RE = re.compile(r"\b\d{3}-\d{2}-\d{4}\b")


def has_pii(text: str) -> bool:
    return bool(EMAIL_RE.search(text) or PHONE_RE.search(text)
                or SSN_RE.search(text))
```

### 2.8 `scripts/calibrate_judge.py` — Cohen's kappa against humans

```python
"""Measure inter-rater agreement between the LLM judge and human labels.

Pass bar: Cohen's kappa > 0.6 on the calibration set. Below that, the
judge prompt needs tuning before it is used as a gate."""
from __future__ import annotations

import json
import sys
from collections import Counter
from pathlib import Path

CALIBRATION = Path("tests/evals/summary/dataset/calibration/human_labels.jsonl")


def cohens_kappa(a: list[int], b: list[int]) -> float:
    assert len(a) == len(b) and a, "need equal, non-empty label vectors"
    labels = sorted(set(a) | set(b))
    n = len(a)
    po = sum(1 for x, y in zip(a, b) if x == y) / n
    ca, cb = Counter(a), Counter(b)
    pe = sum((ca[l] / n) * (cb[l] / n) for l in labels)
    if pe == 1.0:
        return 1.0
    return (po - pe) / (1 - pe)


def run_judge(transcript: str, summary: str) -> int:
    """In the real repo this calls the model via the SDK. Stubbed here
    so the file is standalone-parseable; replace with a real call."""
    raise NotImplementedError("wire to Anthropic SDK in real repo")


def main() -> int:
    rows = [json.loads(l) for l in CALIBRATION.read_text().splitlines()]
    human = [r["human_score"] for r in rows]
    judge = [run_judge(r["transcript"], r["summary"]) for r in rows]
    k = cohens_kappa(human, judge)
    print(f"Cohen's kappa (judge vs. human) on N={len(rows)}: {k:.3f}")
    if k < 0.6:
        print("FAIL: kappa below 0.6; tune judge.v1.yaml before gating.",
              file=sys.stderr)
        return 1
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

### 2.9 `scripts/run_langsmith.py` — optional LangSmith integration

```python
"""Push a Promptfoo run's outputs as a LangSmith dataset-run for
trend-tracking and side-by-side comparison with historical runs.

LangSmith is used here for *debugging and longitudinal view*, not as the
merge gate. The merge gate stays Promptfoo + thresholds.yaml."""
from __future__ import annotations

import json
import os
import sys
from pathlib import Path

try:
    from langsmith import Client
except ImportError:
    print("langsmith not installed; skip (optional path).", file=sys.stderr)
    raise SystemExit(0)


def main(run_file: str) -> int:
    client = Client()  # uses LANGCHAIN_API_KEY / LANGCHAIN_ENDPOINT env vars
    data = json.loads(Path(run_file).read_text())

    project_name = os.environ.get("LANGSMITH_PROJECT",
                                  "team-copilot-summary-eval")
    # Create (or get) a dataset keyed to the eval name. In production
    # the dataset id is pinned via env var so runs append consistently.
    dataset = client.create_dataset(
        dataset_name=f"summary-eval-{data['timestamp'][:10]}",
        description=f"Promptfoo run exported {data['timestamp']}",
    )
    for row in data["results"]:
        client.create_example(
            inputs={"transcript": row["inputs"]["transcript"]},
            outputs={"summary": row["output"]},
            dataset_id=dataset.id,
            metadata={
                "pass": row["pass"],
                "judge_score": row.get("judge_score"),
                "reading_grade": row.get("reading_grade"),
                "project": project_name,
            },
        )
    print(f"exported {len(data['results'])} rows to LangSmith "
          f"dataset {dataset.id}")
    return 0


if __name__ == "__main__":
    raise SystemExit(main(sys.argv[1]))
```

### 2.10 `BASELINE.md` — the eval baseline

```markdown
# Summary eval — baseline

| Field | Value |
|---|---|
| Prompt | `summary.v2.yaml` @ `<git sha>` |
| Model (under test) | `claude-sonnet-4-6` |
| Model (judge) | `claude-opus-4-7` |
| Judge prompt | `judge.v1.yaml` @ `<git sha>` |
| Judge calibration κ | 0.71 (N=20) |
| Dataset | `holdout/transcripts.jsonl` @ `<sha256>` |
| Primary (Wilson lower) | 0.92 |
| Median token count | 412 |
| PII pass rate | 1.00 |

Updating this file requires:
1. A prompt or model change that improves primary by ≥ 0.02 on hold-out
   without regressing any other secondary.
2. A re-calibration run attached.
3. Review by the decision owner named in the commissioning rubric.
```

### 2.11 `README.md`

```markdown
# Summary eval — local recipe

Everything in this directory supports the eval for `summary.v2`.

## 10-minute local run

```bash
# install
pipx install promptfoo

# run (dev set — fast)
promptfoo eval -c tests/evals/summary/promptfooconfig.yaml \
  --vars-file tests/evals/summary/dataset/dev/transcripts.jsonl \
  -o tests/evals/summary/runs/local.json

# run (hold-out — the one that gates)
promptfoo eval -c tests/evals/summary/promptfooconfig.yaml \
  --vars-file tests/evals/summary/dataset/holdout/transcripts.jsonl \
  -o tests/evals/summary/runs/local-holdout.json

# gate against thresholds.yaml
python tests/evals/summary/scripts/gate.py \
  tests/evals/summary/runs/local-holdout.json
```

Any change to `prompts/summary.v2.yaml` or `prompts/judge.v1.yaml`
triggers the GH Action `evals-summary` on PR.

## When to touch the hold-out

Don't. The hold-out is the only guard against overfitting the eval. If
you must refresh it (e.g. your transcripts no longer represent your
teams), do it as its own PR, do not change `prompts/` in the same PR,
and re-baseline `BASELINE.md`.
```

## 3. Validation steps

1. **Run end-to-end locally.** `promptfoo eval -c tests/evals/summary/promptfooconfig.yaml` completes and produces a run JSON in under 15 minutes on a laptop. If it takes longer, check batching and model concurrency settings.
2. **Judge calibration.** Run `python tests/evals/summary/scripts/calibrate_judge.py`. Cohen's kappa against humans must be ≥ 0.6. Below that, do **not** gate on the judge yet — tune the judge prompt first.
3. **Threshold-gate smoke test.** Write a dummy run JSON where primary = 0.85 (judgement zone). The gate script must exit non-zero AND print the judgement-zone owner's name. Write another with primary = 0.95; the gate script must exit zero.
4. **PII smoke test.** Add one `transcripts.jsonl` row containing a real-looking email. Confirm the PII assertion fails that row and that `pii_pass_rate < 1.00` blocks the merge.
5. **CI gating.** Open a trivial PR that changes `summary.v2.yaml`. The GH Action must run. Open a second PR that changes only `README.md`; the action should not run (saves cost).
6. **LangSmith export is optional.** Running without `LANGCHAIN_API_KEY` should not break the main gate. Confirm by unsetting the env var and running.
7. **Hold-out isolation check.** Add a CI rule that a PR touching both `dataset/holdout/*` and `prompts/*` in the same commit is labelled `holdout-touched` and auto-requested for a second review. Dry-run it.
8. **Reproducibility.** Rerun the same config twice. The two runs should agree on every deterministic layer (layers 1 and 2). Layer 3 (judge) will vary; confirm variance is within the calibrated range.
9. **Baseline drift.** On a quarterly cadence, re-measure κ. If it drops below 0.6, pause gating and recalibrate — the judge may have silently regressed under a model update.

## 4. Rubric for grading a submitted walkthrough

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Three evaluator layers present** | One layer | Two layers | Three layers | Three layers, each justified |
| R2 | **Judge calibration** | None | Prose claim of calibration | Calibration script exists | Calibration script + kappa threshold + human-rated set versioned |
| R3 | **Thresholds externalised** | Hard-coded | Constants in a script | Separate YAML | Separate YAML + versioned + judgement zone |
| R4 | **Hold-out discipline** | Single dataset | Split but no enforcement | Hold-out present, hand-review enforced | Hold-out present, CI rule guards it |
| R5 | **Promptfoo config correctness** | Absent / broken | Config exists but not runnable | Runnable locally | Runnable locally + in CI + wires all three layers |
| R6 | **LangSmith integration** | None | Mentioned | Optional script present | Optional script present + clearly delineated as debug-only, not gate |
| R7 | **Deterministic metrics implemented** | None | One metric | Two metrics | Reading-grade + PII + token-count all implemented |
| R8 | **Reproducibility** | None | "Pin versions" prose | Dataset hashed | Dataset hashed + prompts versioned + models named + regen script |
| R9 | **README usable** | None | Prose | 10-minute recipe | 10-minute recipe + hold-out etiquette + CI trigger explained |
| R10 | **Baseline file** | None | A number | A table | Table + update rules + κ included |

**Pass bar: 22/30.**

## 5. Common mistakes

- **Only an LLM-as-judge.** Judges are slow, costly, and non-deterministic. Mixing in the deterministic layers catches the 60–80% of regressions that don't need a model to see.
- **Unversioned judge prompt.** The judge prompt silently drifts, κ silently drops, gates silently become noise. Keep judge prompts in git and rebaseline.
- **Judge model = model under test.** The judge inherits the same biases as the model it's grading. Use a different model, ideally a stronger one.
- **No calibration.** If you don't measure agreement with humans, you don't know whether your gate is signal or coin-flip.
- **Dev-set numbers reported as gate.** The gate must be on the hold-out. The hold-out must not have been looked at during prompt tuning.
- **Temperature not pinned.** A `temperature: 0.7` eval has a flakiness floor you cannot debug. Pin.
- **Thresholds in Python.** Thresholds move; code reviews are heavier than YAML diffs. Externalise.
- **No judgement zone.** Numbers land near the threshold often enough that an auto-ship / auto-reject binary produces noisy decisions.
- **LangSmith as the gate.** LangSmith is a tracing / observability product; if your gate lives there, your CI cannot block merges deterministically. Use it for debugging, not as the merge gate.
- **One-off eval.** An eval that runs once is a measurement, not a gate. Wire it to CI and a schedule.
- **No PII check on the summary.** Summarisers are PII factories in the worst case. Ship the check, even if your stub is pattern-based.
- **Running evals on real user data without disclosure.** The commissioning rubric (Exercise 04) covers this; the walkthrough relies on scrubbed data by construction.
- **Not budgeting compute.** A nightly judge-heavy eval can easily outstrip prod compute. Measure once, decide cadence.
- **Making the local recipe run for an hour.** No one will run it. Budget 10 minutes for the dev run, 15 for hold-out.

## 6. References

- [Promptfoo documentation](https://www.promptfoo.dev/docs/) — configuration syntax (`providers`, `tests`, `assert`, `llm-rubric`, thresholds) used throughout this walkthrough.
- [LangSmith documentation](https://docs.smith.langchain.com/) — hosted traces, datasets, and evaluation runs. Used here as the debugging / longitudinal surface, not as the CI gate.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Measure function (2.3 TEVV, 2.5 validity and reliability) motivates the three-layer measurement design and the hold-out discipline.
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — LLM06 (Sensitive Information Disclosure) motivates the PII layer; LLM05 / LLM09 (Supply Chain / Misinformation) motivate factual-support judging.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the hold-out / membership-inference concerns (ML04) motivate the "never look at the hold-out" rule.
- [MITRE ATLAS](https://atlas.mitre.org/) — used to validate that the eval's security dimensions (PII leakage, prompt-template injection surface) cover the categories most likely to apply to a summarisation workflow.
- Flesch, R., *A new readability yardstick* (1948) and Kincaid et al. (1975) — public-domain readability formulas on which the deterministic reading-grade metric rests. (Descriptive reference; the exercise does not rely on specific numerical thresholds from these papers beyond what the formula emits.)

<!-- follow-up: the exact Cohen's kappa threshold (0.6) is a common convention in inter-rater-agreement literature but is applied here without citing a specific paper. If an auditor asks, replace with the convention most used in your team's prior evals, or cite Landis & Koch 1977 directly after confirming their category boundaries match this use. -->
