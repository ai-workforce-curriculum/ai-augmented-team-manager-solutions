# Exercise 03 — AI Incident Database Case Read And Adapt

> Module: `mod-109-accountability-incident-response-and-blameless-postmortems`
> Exercise type: **design** (reading protocol + adaptation worksheet + grading rubric)

## 1. Solution overview

The deliverable is a short, honest **case read-and-adapt**: the team
manager picks a published AI incident, extracts the mechanism, and
answers *"would this have happened on our team, what would we have
seen, and what would we change?"*

A good read-and-adapt is:

- **Externally anchored, internally honest.** The case is from a public,
  cited source (e.g., the AI Incident Database, a reportable regulator
  filing, a vendor's own disclosure). The adaptation is to *your team's*
  surfaces, runbook, evals, and permissions — with the humility that
  your team is not the team in the case.
- **A mechanism exercise, not a case-study exercise.** The goal is not
  to opine on who was at fault in a public incident. The goal is to
  extract the *mechanism*, map it to your team's taxonomy, and test
  your runbook and evals against it.
- **Scoped to actionable output.** The write-up ends with at most
  three changes to your team's runbook, eval harness, or routing
  contract — and at least one entry on the "we are not changing, and
  here is why" list.
- **Blame-free about the external team.** You do not know their
  constraints, their taxonomy, or their decision chain. The
  read-and-adapt names mechanisms, not judgements.

Design decisions this solution encodes:

1. **Use primary sources only.** A blog post *about* an incident is not
   the incident; it is someone else's adaptation. Pull the original
   AI Incident Database (AIID) entry, the original vendor post, the
   original regulator filing, or the original press release. Multi-step
   telephone games degrade the mechanism.
2. **Name what is not knowable from the public record.** Published
   incidents rarely document the root-cause-analysis to the depth your
   own postmortem (exercise-02) requires. The adaptation must call out
   where it is making an inference and where it would need more
   information if this happened at your team.
3. **The adaptation is a dry-run of exercise-01 and exercise-02.** You
   are testing your runbook and postmortem template against a real
   mechanism you did not invent. If the runbook has no trigger-table
   row that matches, the runbook has a gap. If the postmortem template
   cannot hold the mechanism, the template has a gap.
4. **Reading a case does not entitle you to policy change on its own.**
   One external case is a prompt to look; it is not a sample size. The
   exercise explicitly separates "worth watching" from "worth acting
   on."

## 2. Implementation — the reading protocol

### 2.1 Protocol (what the learner runs)

1. **Pick a case from a primary source.** Candidate sources, in
   descending order of trust:
   - **AI Incident Database** (incidentdatabase.ai) — each incident has
     a canonical entry with linked primary reports. *Prefer the AIID
     entry over the linked article; the entry structures the metadata.*
   - **A regulator or auditor filing** (FTC, EU DSA / EU AI Act notice,
     NIST publication, ICO decision, etc.).
   - **A vendor's own written post-mortem or disclosure**, if named
     and dated.
   - **Press reports**, only if the primary source is not public.
   If the case cannot be traced to a primary source, pick a different
   case.
2. **Record case metadata.** Date, surface (end-user product / internal
   tool / research / regulator), affected population, model/system class.
3. **Extract the mechanism.** Use the shape from exercise-02 §4:
   `<precondition>` + `<trigger>` → `<model/system behaviour>` → `<external harm>`.
   If the public record does not support this shape at all layers,
   mark the layer with `<!-- inferred: … -->` and keep going.
4. **Label with shared taxonomies.** OWASP ML Top 10, MITRE ATLAS,
   NIST AI RMF (see refs below). Use specific IDs.
5. **Run the three "would this hit us?" questions.** Each gets a
   one-paragraph answer:
   - *Does our architecture have the same precondition?*
   - *Does our review / eval harness detect the trigger class?*
   - *Does our runbook contain a trigger-table row for this
     mechanism?*
6. **Decide.** No more than three concrete changes. At least one entry
   on the "not changing, and here is why" list. Owners + dates per
   exercise-02's rubric.
7. **File.** The case read-and-adapt lives in the team's governance
   folder, tagged with the taxonomy labels for mod-110 roll-up.

### 2.2 Worked example — the template, filled in abstractly

> **Note on the worked example.** The exercise is to *read a real
> case* and adapt from it. The reference solution deliberately does not
> enumerate real-world incidents with invented detail, because the
> point of this exercise is primary-source discipline. Instead, the
> worked example below is a **template you fill in while reading a
> real case** — one you choose from AIID or a regulator filing. The
> structure is what is being graded.

```markdown
# AIID Case Read — <AIID incident #NNN> — <short title>

**Read by:** <role>
**Date read:** YYYY-MM-DD
**Primary source:** <AIID entry URL or regulator-filing URL>
**Other sources consulted (secondary):** <optional list>

## 1. Case metadata (from primary source)

- **Incident date:** YYYY-MM or exact where known
- **Surface:** <consumer product | internal tool | research publication | regulator-flagged>
- **Model / system class:** <LLM | recommender | computer-vision | tabular / scoring | agentic multi-tool>
- **Affected population:** <who; scale if stated>
- **Public harms stated:** <categories from the primary source, verbatim>

Do not paraphrase the primary source's harm statement. Copy it in
verbatim; interpretation goes later.

## 2. Mechanism as extracted from the primary source

> Chain:
> `<precondition>` + `<trigger>` → `<model/system behaviour>` → `<external harm>`

For each layer:

- **Precondition:** <what was true about the system before the
  incident; e.g. "model had tool permissions to send email; retrieval
  index contained PII chunks">. Mark `<!-- inferred: … -->` for
  anything not in the primary source.
- **Trigger:** <the specific input / event that activated the
  precondition>.
- **Behaviour:** <what the model/system did>.
- **Harm:** <quoted from primary source>.

## 3. Shared-taxonomy labels

- **OWASP ML Top 10:** <ID + label>, with a sentence of justification.
- **MITRE ATLAS tactic(s) / technique(s):** <AML.TAxxxx> / <AML.T0xxx>,
  with justification.
- **NIST AI RMF function / category:** <Govern|Map|Measure|Manage>,
  <subcategory>, with justification.
- **Any items the primary source labels directly:** copied in.

Use IDs, not family names. If no label fits, record that as a gap:
"the taxonomy has no row for <X>; we will raise this to our
governance partner."

## 4. Would this hit us? (three questions)

### 4.1 Precondition parity

Does our architecture have the same precondition? Specifically:

- The equivalent of <their tool permissions> on our team is <ours>.
  <Same shape? Smaller? Larger?>
- The equivalent of <their retrieval corpus / training set> on our
  team is <ours>. <Same shape? Smaller? Larger?>
- The equivalent of <their deployment surface> is <ours>.

Answer: **yes / partial / no**. If *partial* or *no*, state
precisely *what differs* that reduces our exposure.

### 4.2 Detection parity

If our team hit this trigger tomorrow, would we detect it?

- Pre-deploy: which eval in the mod-103 harness would catch the
  trigger class? If none, that is a gap.
- In-production: which monitor/alert would fire? If none, that is a
  gap.
- Post-fact: which log would preserve evidence? If none, that is a
  gap.

Answer: **yes / partial / no**, with the specific missing artifacts.

### 4.3 Runbook parity

Does our runbook (exercise-01) have a trigger-table row that matches
this mechanism?

- Quote the row if it exists, with its default severity.
- If it does not exist, this is the biggest gap: your team cannot
  declare this incident shape on sight today. Add a row.

## 5. What we will change (at most three)

| ID | Change | Links to | Owner (role) | Target | Notes |
|---|---|---|---|---|---|
| C1 | <change> | <runbook § / eval / contract / etc.> | <role> | YYYY-MM-DD | <why this and not more> |

Fewer is better. Three is a ceiling, not a target.

## 6. What we are not changing, and why

- <item>, because <reason>.
- <item>, because <reason>.

This section demonstrates that the case was read as evidence, not as
an instruction set. Teams that leave this section empty are reading
cases as prescriptions.

## 7. What is not knowable from the public record

Name what you inferred and what you could not reconstruct.

- <unknown 1>; would require <artifact we would have on our team>
- <unknown 2>; would require <artifact we would have on our team>

## 8. Governance roll-up (for mod-110)

- **Taxonomy labels:** copied from §3.
- **Changes applied:** copied from §5 as governance-tracked actions.
- **Watch items:** anything that did not become a change but informs
  future reads.
```

### 2.3 Why the protocol looks like this

- **Primary-source gating is non-negotiable.** AI-incident coverage
  degrades fast across retellings. The AIID is in part a primary-source
  index for exactly this reason; use it as the index, then read the
  linked primary reports, not the AIID summary alone. (Treat the AIID
  as the catalog, not the authority on any single incident's root
  cause.)
- **Mechanism before judgement.** If the first thing you write is "they
  should have done X," you have not understood the system. Mechanism
  extraction forces you to understand before you prescribe. The same
  discipline is why exercise-02's postmortem puts §4 Mechanism before
  §7 What-we-did-not-do-well.
- **Three questions, in that order.** Precondition parity first because
  if your architecture is fundamentally different, the rest of the
  comparison is noise. Detection parity second because if you would
  not even see it, nothing else matters. Runbook parity last because
  it is the thing you can most cheaply close.
- **Ceiling of three changes.** External cases are prompts, not
  mandates. A team that writes seven action items from one external
  case has fallen into the trap this exercise is designed to prevent.
- **Explicit "not knowable" section.** The AIID and most regulator
  filings record outcomes, not full root-cause chains. The team-adapt
  write-up has to be honest about where it is inferring, or it will
  mislead the reader into treating inference as fact.

## 3. Validation steps

To validate a completed read-and-adapt before filing:

1. **Primary-source check.** The URL in the header resolves to a primary
   source. If the primary source is paywalled or taken down, there is
   a cached copy linked and dated.
2. **Verbatim harm check.** §1's harm statement is a direct quote from
   the primary source. Any paraphrase is a weakening of the record.
3. **`<!-- inferred: … -->` discipline.** Every inferred step in §2
   is marked. The unmarked claims should be defensible against the
   primary source.
4. **Taxonomy ID check.** Every label in §3 is an ID from the
   referenced framework, not a family name. (E.g., "ML09 Output
   Integrity Attack" not "OWASP ML".)
5. **Parity-question discipline.** All three questions in §4 are
   answered. "N/A" is not an acceptable answer.
6. **Change count.** §5 has no more than three rows. If you felt you
   needed more, the case probably revealed systemic work that should
   go through its own exercise-02 postmortem, not a read-and-adapt.
7. **Not-changing reasoning.** §6 is not empty.
8. **Blame-free re the external team.** The document does not name
   individuals at the external team, does not speculate about their
   culture, and does not claim to know what their reviewers approved.

## 4. Rubric

Use this to grade a read-and-adapt. Each criterion scores 0–3. Pass
bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Primary-source grounding** | Secondary source only | Primary cited, used secondarily | Primary cited and used | Primary cited, used, and verbatim harm quoted |
| R2 | **Mechanism extraction** | Judgements, no mechanism | Mechanism sketched | Full chain, mostly supported | Full chain + inferred steps marked + layer-by-layer traceability |
| R3 | **Taxonomy labels** | Absent | Family name only | Specific IDs | IDs from OWASP + ATLAS + NIST with justification per label |
| R4 | **Precondition parity** | Not answered | Yes/no, no detail | Yes/no + what differs | Yes/no + what differs + why that reduces/increases exposure |
| R5 | **Detection parity** | Not answered | Vague | Pre-deploy or in-prod gap named | Pre-deploy + in-prod + post-fact gaps all named with specific missing artifacts |
| R6 | **Runbook parity** | Not answered | Noted | Row quoted or missing-row flagged | Row quoted + severity defended, or row added with owner/date |
| R7 | **Change discipline** | > 3 changes, no reasoning | > 3 changes, reasoned | ≤ 3 changes | ≤ 3 changes + owners + dates + reasoning per change |
| R8 | **Not-changing reasoning** | Empty | One item | Items with reasoning | Items with reasoning that a later reader could re-decide against |
| R9 | **Blame-free re external team** | Name-calling | Mixed | Mechanism-only | Mechanism-only + explicit "not knowable" section honoured |

## 5. Common mistakes

- **Reading a blog post instead of the primary source.** Someone else's
  framing of the incident is not the incident. If you cannot find a
  primary source, pick a different case.
- **Treating one case as a sample.** One external incident is a
  prompt-to-look, not a basis for team-wide policy change. The three-
  change ceiling exists to enforce that.
- **Inventing root-cause detail.** The public record for most AI
  incidents does not include the depth your own postmortem would
  require. Mark `<!-- inferred -->` and move on; do not fabricate.
- **Judging the external team.** "They should have had a better eval
  harness" is a judgement you do not have evidence for. The exercise
  is about your team, not theirs.
- **Taxonomy drift.** Using family names ("OWASP ML") instead of
  specific IDs makes the labels useless for mod-110 roll-up.
- **Skipping the parity questions.** The three questions are the entire
  value of the exercise. If they are answered with "N/A," you have
  written a book review, not an adaptation.
- **No changes and no watch items.** A case you read and did nothing
  about is a case you did not actually adapt from. At minimum, a watch
  item belongs in §6 or in §5 with target = "review in 90 days."
- **Confusing celebrity incidents with representative incidents.** The
  cases you have already heard about are selected for virality, not for
  mechanism diversity. Mix in less-publicised AIID entries or regulator
  filings — the mechanism coverage is broader.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function expects documented incident-learning
  processes; this exercise is one. The *Map* function's context-of-use
  categories drive the "precondition parity" question in §4.1.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the ML01–ML10 labels are the vocabulary used in §3 of the
  read-and-adapt so that external incidents aggregate with internal
  ones.
- [MITRE ATLAS](https://atlas.mitre.org/) — tactics and techniques
  (AML.TAxxxx / AML.T0xxx) for adversarial-threat labelling. External
  incidents labelled against ATLAS give the team a comparison surface
  with the threat-intel community.

## 7. Open follow-up

Once the team has read ~10 cases, cluster the mechanisms and check
which OWASP ML Top 10 rows are underrepresented in your team's
adapted-change list. That skew is a governance input for mod-110,
not just this exercise's artifact.
