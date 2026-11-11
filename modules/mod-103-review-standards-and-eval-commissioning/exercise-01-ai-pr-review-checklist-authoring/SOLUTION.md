# Exercise 01 — Authoring the AI PR / Document Review Checklist

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **design** (authoring deliverable + decision rationale + rubric)

## 1. Solution overview

The deliverable is a short, opinionated **"review this before you merge / publish AI-authored work"** checklist that a team manager can drop into a repo's `.github/PULL_REQUEST_TEMPLATE.md`, a Notion runbook, or a doc-review wiki page. A good checklist is:

- **Short enough that reviewers actually run it** — target **8–12 items**, not 40. The purpose is to catch the mistakes AI assistants *reliably make*, not to recreate a full code-review manual.
- **Phrased as checkable claims** ("I verified X", "I confirmed Y"), not vague principles ("be careful about hallucinations").
- **Segmented by risk**: items that can block a merge vs. items the author should answer in the PR description.
- **Scoped to the artifact type**: code PRs and prose documents share some concerns (sourcing, hallucination, PII) but differ on others (compiles/passes tests vs. factual claims cited).

The core design decisions this solution encodes:

1. **The checklist reflects the AI-specific failure modes, not generic good-engineering hygiene.** Regular review already covers "did you write a test?" — we don't duplicate that. We surface the modes that *AI-assisted* output fails in differently: fabricated identifiers, inherited license-violating snippets, plausible-sounding but invented citations, prompt-injection-vulnerable templates, PII leaked into logs/training data.
2. **"Human must assert the claim" is the merge gate.** The author is signing that *they* read and understood the diff. We never let the AI assistant self-certify. This is the accountability leg that governance frameworks (NIST AI RMF Govern function) rely on.
3. **Risk tiers drive required rigor.** Not every PR is production-critical. The checklist differentiates *low-risk* (internal tool, docs typo-fix), *medium*, and *high-risk* (security/auth code, customer-facing prose, prompts shipped to prod) — and the high-risk column gets extra items (eval evidence, security review, written author acknowledgement).

## 2. Implementation — the checklist

### 2.1 Checklist for AI-assisted **code PRs**

```markdown
## AI-assist review checklist (code)

> Fill this out when any part of this PR was drafted, generated, autocompleted,
> or heavily edited with an AI coding assistant. Tick every box you can honestly
> tick; any unticked box needs an explanation.

### Author attestation
- [ ] I personally read every line of the diff and can explain what it does.
- [ ] I disclose the AI tools used (model + feature, e.g. "Claude Code CLI, agent mode").
- [ ] I ran the full test suite locally or in CI and it passes.

### Correctness
- [ ] Every imported identifier, function, flag, or API endpoint exists in the
      current version of the dependency (checked against docs or source, not
      inferred from the assistant's completion).
- [ ] No "plausible but wrong" literals: hardcoded URLs, UUIDs, example tokens,
      regex patterns are either from an authoritative source or clearly test fixtures.
- [ ] Error handling covers the paths I actually care about, not every
      hypothetical one the assistant suggested. I removed defensive code for
      conditions that cannot occur.

### Security
- [ ] No secret, key, token, or customer identifier was pasted into the
      assistant's context or committed by it.
- [ ] If this PR adds a prompt, LLM call, or tool wiring: untrusted input is
      not concatenated into the system prompt or executed as a tool call
      without an allowlist / parser in between.
- [ ] If this PR adds code pulled from an assistant's suggestion that looks
      copied from a well-known project, I verified the license.

### Review-worthiness
- [ ] The PR description states *why*, not just *what*, and references the
      ticket/issue that commissioned the work.
- [ ] I did not accept scope creep from the assistant: the diff is limited to
      the stated task. Refactors unrelated to the ticket are out or are a
      separate PR.
```

### 2.2 Checklist for AI-assisted **documents / prose**

```markdown
## AI-assist review checklist (documents)

> Fill this out for drafts generated with an AI assistant: incident reports,
> design docs, runbooks, 1:1 summaries, external posts, commissioned research.

### Author attestation
- [ ] I read every paragraph end-to-end and would be willing to defend the
      claims in it to the people named in it.
- [ ] I disclose the assistance used. If this doc is external-facing,
      disclosure appears in the doc itself, per our team's AI-use policy.

### Factual integrity
- [ ] Every statistic, quote, named person, named incident, product feature,
      and dated event is either (a) cited to a source I personally opened,
      (b) pulled from an internal system I named, or (c) labeled as example
      / hypothetical.
- [ ] Every URL in the document resolves and points to the content implied by
      the surrounding text.
- [ ] I corrected or removed the assistant's hedging where it claimed
      "studies show" / "experts recommend" without a traceable source.

### Scope and tone
- [ ] The doc does not include people's names, performance commentary, or
      sensitive customer data that should not be in this venue.
- [ ] The tone matches our team's voice (not generic AI "corporate neutral").

### Safety
- [ ] If this doc will be fed to downstream automation (e.g., ingested into a
      RAG index, posted to a channel that triggers alerts), I considered
      whether its content could be acted on by an agent without further review.
```

### 2.3 Risk-tier overlay

| Tier | Trigger | Added requirements |
|---|---|---|
| **Low** | Internal-only, no customer impact, non-sensitive. | Core checklist only. |
| **Medium** | Customer-visible, non-auth, non-billing, or any doc going outside the team. | Core checklist + a second human reviewer (one is enough, but the second must also attest they read the diff/doc — not just "LGTM"). |
| **High** | Auth / authz / billing / PII handling / prompts and model wiring shipped to prod / public comms. | Medium + evidence of running the relevant eval suite (see Exercise 04) + security sign-off per the OWASP-mapped item set (see Exercise 06). |

### 2.4 Decision rationale (why these items and not others)

- **Author attestation as the first section.** This is the accountability contract in miniature. Every AI-use policy I've worked under reduces, in the end, to "a human staked their reputation on this merge." Making it a tick-box up top signals to the author: "You, not the tool, are the author of record." This aligns with NIST AI RMF **Govern 1.1** (responsibilities for AI actors are documented) and **Govern 4.1** (organizational practices and personnel to foster accountable AI).
- **"Every imported identifier exists."** This is the single most common AI-coding failure mode: the assistant invents or stale-caches an API name. Catching it is cheap in review and expensive in prod. One checkbox > ten words of policy.
- **Prompt-injection item sits in Security, not in a separate "AI-only" column.** Teams that bolt an "AI section" onto their review process tend to let AI-specific items decay as novelty. Folding the prompt-injection item into the security section signals *this is a normal security concern you check every time*, mapping to OWASP LLM01 (prompt injection) — see Exercise 06 for the full mapping.
- **License-check item exists because assistant output can be near-verbatim copies of GPL / AGPL / proprietary code** present in training data. The team manager's exposure here is not theoretical; it is a legal-risk category that should sit in the review template, not in a separate legal checklist nobody opens.
- **Scope-creep item exists because assistants love to refactor adjacent code.** The review burden that creates is real and distinct from the task actually commissioned. A short checkbox puts the discipline on the author, not the reviewer.
- **The document checklist's "URL resolves" item exists because invented-but-plausible URLs are the single most common doc-authoring failure.** The item is cheap: open each URL once. The downside of skipping it is public, embarrassing, and often reaches customers.

## 3. Validation steps

To validate this checklist against a real draft PR or doc:

1. **Dogfood on one of your last five AI-assisted PRs.** Walk the checklist against a merged PR, as if you were the reviewer. If any item would have *caught a real bug* you merged, it belongs. If any item would have been a nuisance with no realistic failure mode in your codebase, drop it or demote it to the "answer in PR description" layer.
2. **Dry-run on a hypothetical high-risk PR.** Pick a scenario — e.g. a change to the password-reset flow drafted with an AI assistant — and walk the checklist. If the high-risk tier doesn't produce extra actions here, your tiering is weak.
3. **Review the length.** If you can't read the whole checklist aloud in under 60 seconds, it is too long and reviewers will stop reading. Cut.
4. **Peer review the checklist itself.** Ask one senior IC and one adjacent team manager to redline. Their marginal changes are often the "I would never have thought of that" items.
5. **Pilot for two weeks on one team.** Count: how many PRs ticked every box? How many had an "unticked box, explained" pattern? How many were merged with the checklist blank (process failure)? Adjust.

Validation is **behavioural**, not stylistic — you are checking that the checklist changes what reviewers notice and what authors catch before pushing.

## 4. Rubric

Use this to grade a submitted checklist (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Length discipline** | > 25 items, one flat list | 15–25 items | 8–14 items, risk-tiered | 8–12 items, risk-tiered, segments justified |
| R2 | **AI-specific coverage** | Generic code-review items only | Mentions "hallucination" vaguely | Names 2+ AI-specific failure modes concretely | Items map 1:1 to the AI failure modes the team has actually seen, and others are deferred |
| R3 | **Attestation design** | No attestation layer | Author checks "LGTM" | Author attests to reading the diff | Author attests + discloses tools + discloses model/feature |
| R4 | **Security coverage** | None | Mentions secrets | Secrets + prompt injection item | Secrets + prompt injection + license + mapped to OWASP (see Exercise 06) |
| R5 | **Factual-integrity (doc) coverage** | None | "Check sources" generic | Named source + URL-resolves + tone/scope | Named source + URL-resolves + hedging-cleanup + venue/audience check |
| R6 | **Risk tiering** | Single flat list | Vague "use judgement" | 2 tiers with explicit triggers | 3 tiers with explicit triggers + extra requirements per tier |
| R7 | **Validation plan** | None | "Try it once" | Dogfood + review | Dogfood + dry-run + peer review + pilot metric |
| R8 | **Fit to team context** | Imported template, not adapted | One local example | Multiple references to the team's own code or docs | Checklist is unambiguously about *this* team's stack and venues |

**Pass bar: 16/24.** Below that, iterate before publishing to the team.

## 5. Common mistakes

- **Writing a 40-item checklist.** Length is the enemy of adoption. Everything past item 12 is a reviewer-education wiki, not a checklist.
- **Making the checklist AI-generic.** "Verify the AI output is correct" is not a checkable item. "Every imported identifier exists in the current docs" is.
- **Using the checklist as a policy document.** Policies explain *why*; checklists tell you *what to tick*. Keep rationale in a separate `REVIEW_STANDARDS.md` and link from the checklist.
- **Letting the AI tool "sign off" on items.** Attestation must be by a named human. If the checklist's items can be ticked by an agent running `review.sh`, the accountability leg is missing.
- **No tiering, or tiering by file path.** "Any file under `src/auth/`" is a weak trigger — the risk is in *what the change does*, not where it lives. Tier by the effect of the change.
- **Treating prose and code the same.** Prose failure modes (invented citations, fake quotes, URL rot) are different from code failure modes (invented APIs, licence pollution). Separate lists are cheaper than a confusing merged one.
- **Shipping and never revisiting.** A checklist that doesn't change after six months is a checklist that's stopped learning from your team's incidents. Review it after every notable AI-assist-related miss.
- **Omitting risk tiering for documents.** External posts and internal typo-fixes are not the same artifact. Use the same tier structure for prose.

## 6. References

- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the ML-security framing for the security section (see Exercise 06 for the full map).
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — the direct source for the prompt-injection / insecure-output / supply-chain items in the code checklist. (Official OWASP project; cited in addition to the ML Top 10 listed in the module source policy.)
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy; used to sanity-check that the security section covers the techniques most likely to apply to a code / doc authoring workflow.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function's accountability and transparency requirements are the backbone of the "author attestation" layer.

<!-- follow-up: once the team has run this checklist for a quarter, replace the "length discipline" rubric targets with the empirical median checklist length we actually see in merged PRs. -->
