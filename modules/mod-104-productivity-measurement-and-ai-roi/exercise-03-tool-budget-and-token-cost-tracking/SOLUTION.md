# Exercise 03 — Tool Budget and Token-Cost Tracking

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **implementation** (runnable spec + policy artefact) + **design** (rationale + rubric)

## 1. Solution overview

The deliverable is a **tool-budget-and-token-cost tracking kit** for a single team: a budget policy, a cost-tracking data model, a minimal collection script, and a weekly review ritual. It is what a team manager hands to their director when asked "what is this costing us and how do you know?"

A good kit is:

- **Honest about the cost shape.** AI coding assistants have *per-seat* costs (vendor subscriptions) and *per-use* costs (API tokens, longer CI runs, cloud compute on agentic tasks). These sum differently per cohort and per month. Track both.
- **Instrumentable by one person in a week.** A tracking system that needs a data engineer to stand up is a tracking system that does not get built.
- **Reconciled with finance monthly.** Shadow spend that does not match the finance ledger is spend you cannot defend.
- **Paired with a value side.** Cost without value is a question; value without cost is a sales pitch. This exercise owns the cost half; Exercise 04 owns the value framing.

Design decisions this solution encodes:

1. **Budget at the team level, not the individual.** Individual caps create perverse incentives (don't use the tool on the hard problem) and do not match how costs actually accrue. Team-level caps with per-individual visibility strike the right balance.
2. **Separate "fixed seat cost" from "variable usage cost" from "second-order cost".** Fixed seats are easy; variable usage requires token-level accounting; second-order (longer CI, more cloud compute during agentic runs, review-time overhead) is usually the largest missed category.
3. **Cost telemetry is pulled, not pushed.** Vendors publish usage APIs; ingest those into one team-scoped table. Do not rely on individual developers remembering to log a spreadsheet.
4. **Forecast + variance, not just actuals.** A budget without a forecast is a reporting system. The useful artefact is a monthly forecast and a weekly variance-against-forecast.
5. **One policy, one dashboard, one ritual.** Three artefacts. Not a platform. The scope of this exercise is team-level governance, not org-level FinOps.

## 2. Implementation — the artefacts

### 2.1 Directory layout

```
tools/ai-budget/
  POLICY.md                      # team AI tool budget policy
  schema.sql                     # cost-tracking tables (SQLite / Postgres)
  collectors/
    collect_anthropic_usage.py   # pull Anthropic API usage
    collect_openai_usage.py      # pull OpenAI usage
    collect_vendor_seats.py      # pull per-seat cost from vendor admin API
    collect_ci_minutes.py        # pull agent-caused CI minutes
  ingest/
    load_month.py                # idempotent monthly load
  reports/
    weekly_variance.sql          # variance query for the Monday review
    monthly_rollup.sql           # monthly rollup for the exec pre-read
    cost_per_pr.sql              # cost / merged PR, per cohort
  dashboards/
    README.md                    # Grafana / Metabase panel layout
  BASELINE.md                    # month-0 numbers for later variance
```

### 2.2 `POLICY.md` — the team AI tool budget policy

```markdown
# Team AI Tool Budget Policy

## Scope
This policy covers AI coding assistants and AI developer tools paid for by
the <team name> team, including subscription seats and metered-API usage.

## Budget shape (monthly)
- **Fixed-seat line.** $<N> per active engineer per month, covering the
  team-standard assistant subscription. Reviewed yearly at budget planning.
- **Variable-usage line.** Up to $<M> / engineer / month of metered token
  spend, attributed to the team cost centre. Soft cap; see "overrun" below.
- **Second-order line.** A reserve equal to 20% of the sum of the two above,
  held centrally for CI-minute and cloud-compute overages caused by
  agentic workloads. Reviewed monthly.

## Attribution
- Every tool integration uses a team-scoped API key. Personal keys are
  prohibited for work-funded spend.
- Spend attribution flows: vendor usage API -> `cost_events` table ->
  team dashboard.

## Overrun handling
- 80% of the variable-usage line reached mid-month -> automatic Slack
  warning to the team manager.
- 100% reached -> no hard cut-off. Overruns are allowed if any one of:
  (a) they are on-task (shipping a committed deliverable),
  (b) they are discoverable in the weekly variance review, and
  (c) the team manager signs off within one business day.
- Sustained overrun (>110% for two consecutive weeks) -> the team manager
  escalates to the next budget cycle rather than cutting usage mid-sprint.

## Review ritual
- Weekly: 15-minute Monday variance review — manager + one IC. Reads the
  `weekly_variance.sql` output.
- Monthly: finance reconciliation — the dashboard's total must match
  finance's invoice totals within 2%. Variance over 2% triggers an audit of
  the collectors.
- Quarterly: budget rebase — compare actual run-rate vs. forecast, rebase
  next quarter's lines, document what moved.

## What this policy does NOT do
- It does not individualise cost accountability. The team is the unit.
- It does not throttle usage mid-sprint. Overruns escalate, they do not
  interrupt committed work.
- It does not measure value. Value is tracked in the Core 4 dashboard
  (see Exercise 01) and narrated in the exec pre-read (see Exercise 04).
```

### 2.3 `schema.sql` — the cost-tracking data model

```sql
-- One row per billable usage event. Append-only.
CREATE TABLE cost_events (
  event_id          TEXT PRIMARY KEY,         -- vendor-assigned or uuid
  ts                TIMESTAMPTZ NOT NULL,     -- event timestamp (UTC)
  vendor            TEXT NOT NULL,            -- 'anthropic', 'openai', 'github_copilot', ...
  product           TEXT NOT NULL,            -- 'api', 'seat', 'ci_minutes', 'cloud_compute'
  team              TEXT NOT NULL,            -- team cost centre
  actor             TEXT,                     -- user email if available, NULL for shared
  model             TEXT,                     -- e.g. 'claude-sonnet-4-6'; NULL for seats
  input_tokens      BIGINT,
  output_tokens     BIGINT,
  cache_read_tokens BIGINT,
  cache_write_tokens BIGINT,
  units             NUMERIC(18,4),            -- generic unit for non-token products
  unit_name         TEXT,                     -- 'seat_month', 'ci_minute', 'gb_hour'
  cost_usd          NUMERIC(10,4) NOT NULL,   -- authoritative cost from the vendor invoice
  cost_source       TEXT NOT NULL,            -- 'usage_api' | 'invoice' | 'estimate'
  attribution       JSONB                     -- free-form tag bag: {repo, workflow, pr_id, ...}
);

CREATE INDEX cost_events_team_ts ON cost_events(team, ts DESC);
CREATE INDEX cost_events_vendor_ts ON cost_events(vendor, ts DESC);
CREATE INDEX cost_events_actor_ts ON cost_events(actor, ts DESC);

-- Monthly forecast per team × vendor × product, set at budget rebase.
CREATE TABLE budget_lines (
  team              TEXT NOT NULL,
  vendor            TEXT NOT NULL,
  product           TEXT NOT NULL,
  month             DATE NOT NULL,            -- first of month
  forecast_usd      NUMERIC(10,2) NOT NULL,
  soft_cap_usd      NUMERIC(10,2) NOT NULL,
  notes             TEXT,
  PRIMARY KEY (team, vendor, product, month)
);
```

### 2.4 `collectors/collect_anthropic_usage.py` — pull usage (sketch)

```python
"""Pull Anthropic API usage for the team's workspace and upsert into cost_events.

Run daily. Idempotent on event_id.
"""
import os
import uuid
import datetime as dt
from typing import Iterable
import httpx
import psycopg

ANTHROPIC_ADMIN_KEY = os.environ["ANTHROPIC_ADMIN_KEY"]
WORKSPACE_ID = os.environ["ANTHROPIC_WORKSPACE_ID"]
DB_DSN = os.environ["BUDGET_DB_DSN"]
TEAM = os.environ["TEAM_NAME"]


def fetch_usage(start: dt.date, end: dt.date) -> Iterable[dict]:
    """Yield one usage record per API call from the Anthropic Admin / Usage API.

    The exact endpoint shape is documented at
    https://docs.anthropic.com/ and is subject to change; adapt as needed.
    """
    # Illustrative — treat the real endpoint and schema as the source of truth.
    headers = {"x-api-key": ANTHROPIC_ADMIN_KEY, "anthropic-version": "2023-06-01"}
    params = {"workspace_id": WORKSPACE_ID, "start_date": start.isoformat(), "end_date": end.isoformat()}
    with httpx.Client(timeout=30) as client:
        r = client.get("https://api.anthropic.com/v1/organizations/usage_report/messages",
                       headers=headers, params=params)
        r.raise_for_status()
        for row in r.json().get("data", []):
            yield row


def to_cost_event(row: dict) -> dict:
    return {
        "event_id": row.get("id") or str(uuid.uuid4()),
        "ts": row["timestamp"],
        "vendor": "anthropic",
        "product": "api",
        "team": TEAM,
        "actor": row.get("user_email"),
        "model": row.get("model"),
        "input_tokens": row.get("input_tokens", 0),
        "output_tokens": row.get("output_tokens", 0),
        "cache_read_tokens": row.get("cache_creation_input_tokens", 0),
        "cache_write_tokens": row.get("cache_read_input_tokens", 0),
        "cost_usd": float(row["cost"]["amount"]),
        "cost_source": "usage_api",
        "attribution": {"api_key_id": row.get("api_key_id")},
    }


UPSERT = """
INSERT INTO cost_events (
  event_id, ts, vendor, product, team, actor, model,
  input_tokens, output_tokens, cache_read_tokens, cache_write_tokens,
  cost_usd, cost_source, attribution
) VALUES (
  %(event_id)s, %(ts)s, %(vendor)s, %(product)s, %(team)s, %(actor)s, %(model)s,
  %(input_tokens)s, %(output_tokens)s, %(cache_read_tokens)s, %(cache_write_tokens)s,
  %(cost_usd)s, %(cost_source)s, %(attribution)s
)
ON CONFLICT (event_id) DO UPDATE SET
  cost_usd = EXCLUDED.cost_usd,
  cost_source = EXCLUDED.cost_source;
"""


def main() -> None:
    today = dt.date.today()
    start = today - dt.timedelta(days=2)       # 2-day lookback for late-arriving events
    with psycopg.connect(DB_DSN) as conn, conn.cursor() as cur:
        for raw in fetch_usage(start, today):
            cur.execute(UPSERT, to_cost_event(raw))
        conn.commit()


if __name__ == "__main__":
    main()
```

The OpenAI and GitHub Copilot collectors follow the same shape: pull the vendor's usage endpoint, normalise, upsert on a stable event id. Keep collectors boring and idempotent.

### 2.5 `reports/weekly_variance.sql`

```sql
-- Weekly variance review: $ spent vs. forecast through today-of-month.
WITH month_days AS (
  SELECT
    date_trunc('month', CURRENT_DATE)::date AS month_start,
    (date_trunc('month', CURRENT_DATE) + INTERVAL '1 month - 1 day')::date AS month_end
),
elapsed AS (
  SELECT
    ((CURRENT_DATE - month_start)::numeric + 1)
      / ((month_end - month_start)::numeric + 1)
      AS fraction_of_month
  FROM month_days
),
actuals AS (
  SELECT
    team, vendor, product,
    SUM(cost_usd) AS spent_mtd
  FROM cost_events, month_days
  WHERE ts >= month_start AND ts <  month_start + INTERVAL '1 month'
  GROUP BY team, vendor, product
),
expected AS (
  SELECT
    team, vendor, product, forecast_usd, soft_cap_usd,
    forecast_usd * (SELECT fraction_of_month FROM elapsed) AS expected_mtd
  FROM budget_lines
  WHERE month = (SELECT month_start FROM month_days)
)
SELECT
  e.team, e.vendor, e.product,
  COALESCE(a.spent_mtd, 0) AS spent_mtd,
  e.expected_mtd,
  COALESCE(a.spent_mtd, 0) - e.expected_mtd AS variance_usd,
  CASE
    WHEN e.expected_mtd = 0 THEN NULL
    ELSE (COALESCE(a.spent_mtd, 0) - e.expected_mtd) / e.expected_mtd
  END AS variance_pct,
  e.soft_cap_usd,
  CASE
    WHEN COALESCE(a.spent_mtd, 0) >= e.soft_cap_usd THEN 'OVER_SOFT_CAP'
    WHEN COALESCE(a.spent_mtd, 0) >= e.soft_cap_usd * 0.8 THEN 'APPROACHING_SOFT_CAP'
    ELSE 'OK'
  END AS status
FROM expected e
LEFT JOIN actuals a USING (team, vendor, product)
ORDER BY variance_pct DESC NULLS LAST;
```

### 2.6 `reports/cost_per_pr.sql` — the cross-cut that matters

```sql
-- Cost per merged PR, by cohort, last 30 days.
-- Requires cost_events.attribution to include {pr_id: ...} for API events
-- that were made by the assistant during work on a specific PR. Where
-- attribution is missing, the row is excluded from the "attributable" cohort
-- and reported separately as "unattributed".

WITH pr_costs AS (
  SELECT
    attribution->>'pr_id' AS pr_id,
    actor,
    SUM(cost_usd) AS pr_cost_usd
  FROM cost_events
  WHERE ts >= CURRENT_DATE - INTERVAL '30 days'
    AND attribution ? 'pr_id'
  GROUP BY attribution->>'pr_id', actor
),
prs AS (
  -- joined from the team's PR system (GitHub, GitLab); illustrative
  SELECT pr_id, actor, cohort, merged_at
  FROM merged_prs
  WHERE merged_at >= CURRENT_DATE - INTERVAL '30 days'
)
SELECT
  prs.cohort,
  COUNT(DISTINCT prs.pr_id) AS prs_merged,
  AVG(pc.pr_cost_usd) AS avg_cost_per_pr,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY pc.pr_cost_usd) AS p50_cost_per_pr,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY pc.pr_cost_usd) AS p90_cost_per_pr
FROM prs
LEFT JOIN pr_costs pc USING (pr_id, actor)
GROUP BY prs.cohort
ORDER BY p90_cost_per_pr DESC NULLS LAST;
```

### 2.7 Weekly review ritual (the 15-minute Monday meeting)

Agenda, in order:

1. **Red lights first.** Read `weekly_variance.sql` output; discuss any row marked `OVER_SOFT_CAP` or `APPROACHING_SOFT_CAP`.
2. **What drove the variance?** Open `cost_per_pr.sql` for the cohort responsible. Is it one engineer on one hard problem (fine, document), a systematic uptick (investigate), or a bug in the collector (fix)?
3. **Any second-order costs?** CI minutes, cloud compute. Check the reserve line.
4. **Decision.** Absorb, escalate to budget rebase, or adjust the forecast.
5. **Write it down.** One-line note in `BASELINE.md` or the team's run-log so the quarterly rebase has a history.

## 3. Validation steps

1. **Finance reconciliation.** At month-end, sum `cost_events.cost_usd` by vendor for the month and compare to the vendor invoices routed through finance. Variance > 2% means a collector is mis-mapping; fix before quoting internal numbers.
2. **Collector idempotence.** Run the collector twice for the same day. Row count in `cost_events` must not change. If it does, the upsert on `event_id` is broken.
3. **Backfill test.** Delete the last seven days of one vendor's rows; re-run the collector; verify the data returns and cost totals match pre-delete.
4. **Dry-run the overrun flow.** Set a fake `soft_cap_usd` low enough to trigger `OVER_SOFT_CAP`; walk the Monday review. Confirm that the escalation path reaches the team manager and that no mid-sprint throttling happens automatically.
5. **Cost-per-PR sanity check.** Pull one week's `cost_per_pr.sql` output. If any single PR's `pr_cost_usd` looks surprising, open the PR's attribution events and verify they belong to that PR. Mis-attribution is the most common silent error.
6. **Second-order coverage.** Pick one agentic workflow that ran in CI this month and verify its CI minutes were ingested into `cost_events`. If not, the second-order line is under-measuring, which inflates perceived ROI.

Validation is **budgetary**: the numbers must survive comparison with finance, and the policy must survive one real overrun.

## 4. Rubric

Use this to grade a submitted tool-budget kit. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Policy clarity** | No written policy | Policy exists, but ambiguous on attribution and overrun | Policy names attribution + overrun + review ritual | All of 2 + explicit "what this policy does NOT do" section |
| R2 | **Cost-model completeness** | Fixed seats only | Fixed + variable API | Fixed + variable + second-order (CI minutes, cloud compute) | All three + reserve + reconciliation requirement with finance |
| R3 | **Collection infrastructure** | Spreadsheet, manually updated | One collector | Collectors per vendor + idempotent upsert | All of 2 + daily cron + backfill-safe + monitored |
| R4 | **Attribution granularity** | Team totals only | Team + actor | Team + actor + PR / workflow via attribution tag | All of 2 + separate cohort / repo / task-type dimensions |
| R5 | **Variance reporting** | Actuals only | Actuals vs. forecast monthly | Weekly variance + soft-cap flagging | Weekly variance + flagging + audit trail of why each red light was absorbed or escalated |
| R6 | **Overrun handling design** | Hard cut-off mid-sprint | Soft cap, informal | Soft cap + explicit escalation + team-manager sign-off | All of 2 + "sustained overrun" rule + no mid-sprint throttling |
| R7 | **Finance reconciliation** | None | Mentioned vaguely | Monthly reconciliation step | Monthly reconciliation + explicit tolerance (e.g., 2%) + audit trigger when exceeded |
| R8 | **Ritual and ownership** | No ritual | Owner but no cadence | Weekly review + monthly reconciliation + quarterly rebase | All of 2 + agenda + attendees + written output into the quarterly rebase |

**Pass bar: 16/24.** Below that, iterate before sharing with finance or exec.

## 5. Common mistakes

- **Tracking only API tokens.** Vendor seats are the largest line for many teams; CI minutes and agent-driven cloud compute are the fastest-growing line for teams running agentic workflows. Omitting either is a silent under-count of ~20–50%.
- **Individualising cost caps.** Caps per engineer encourage avoidance of the tool on the hard problems where it is most valuable. Team-level caps with per-individual *visibility* are the right default.
- **Pushing responsibility to developers to self-report.** A spreadsheet-driven budget collapses within a month. Pull from the vendor usage APIs.
- **Reporting raw spend without a denominator.** A cost number with no "per merged PR" or "per active engineer" denominator is not interpretable month-over-month.
- **Letting attribution rot.** If the API-call attribution tag (PR id, workflow) is optional and not enforced, 70% of events become "unattributed" and the cost-per-PR query becomes noise. Instrument attribution in the tool wiring, not after the fact.
- **No reconciliation with finance.** The single fastest way to lose budget credibility is for the dashboard and the invoice to disagree. Monthly reconciliation is non-negotiable.
- **Hard mid-sprint throttling.** Cutting off a tool when the team is mid-sprint is both operationally bad and politically expensive. Soft caps + escalation + sign-off is the pattern.
- **Treating budget as a cost-only artefact.** Cost without value is a half-told story; the exec audience pairs this kit with Exercise 04's ROI narrative.

## 6. References

- Anthropic API documentation — pricing, token accounting, and workspace usage endpoints: <https://docs.anthropic.com/>
- OpenAI API documentation — usage and billing endpoints: <https://platform.openai.com/docs/>
- GitHub Copilot admin / billing documentation: <https://docs.github.com/copilot>
- FinOps Foundation — *FinOps Framework*: <https://www.finops.org/framework/> — the industry-standard vocabulary for cloud / SaaS cost management; this kit implements the *Inform* and *Optimize* phases at team scope.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the *Measure* function includes tracking resource cost of AI systems; this kit is this exercise's operationalisation of that at team scope.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [MITRE ATLAS](https://atlas.mitre.org/) — referenced because cost events can leak sensitive information (API keys, PII in attribution tags); the `attribution` column must be reviewed for sensitive content before export.
- SPACE / DX Core 4 — see Exercise 01. The cost side of ROI pairs directly with the productivity side of these frameworks.

<!-- follow-up: once three months of cost_events are available, add a `reports/anomaly_detection.sql` that flags week-over-week spend anomalies above a threshold, and wire it into the Monday review. -->
