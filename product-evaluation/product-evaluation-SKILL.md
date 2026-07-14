---
name: product-evaluation
description: >
  Produce a product evaluation report using sequential loops for scoping,
  criteria definition, data gathering, scoring, claim verification, and
  synthesis. Invoke when the user wants to compare, evaluate, benchmark,
  or select between products, vendors, or tools.
when_to_use: >
  Use when the user wants a product evaluation, vendor comparison, tool
  selection report, build-vs-buy analysis, or competitive benchmark.
  Triggers on keywords: evaluate, compare, vendor selection, product
  comparison, benchmark, which tool should we use, buyer's guide.
---

# Product Evaluation Report Agent

Evaluate products against weighted criteria and produce a report where every
factual claim is traced to a source and every source is classified by type.

The subject of the evaluation is: **$ARGUMENTS**

If `$ARGUMENTS` is empty, ask the user what products or category to evaluate
before proceeding.

---

## Phase 0 — Scope Check (pre-loop, runs once)

Do not begin evaluating until these are answered. Ask the user directly if
any are unclear — do not assume.

1. **Who is the audience?** (engineering team, procurement, exec sponsor, end users)
2. **What decision does this inform?** (adopt/reject, replace incumbent, shortlist for pilot)
3. **What is the constraint envelope?** (budget ceiling, timeline, compliance, headcount, existing stack)
4. **What are the candidate products?** If not specified, propose a shortlist and get approval before continuing.
5. **What is the freshness window?** (how recent must pricing and version data be — default: 90 days)

Write answers to `scope.md`. Every downstream loop reads from this file.

Skipping this phase is why most evaluation reports are thorough and useless.

---

## Phase 1 — Criteria Loop (max 3 iterations)

Define what "good" means **before** looking at any product. This prevents
post-hoc rationalization of a favorite.

1. Draft evaluation criteria derived from `scope.md`
2. Assign each criterion a weight and a class: `MUST-HAVE` or `NICE-TO-HAVE`
3. Critique the criteria:
   - Is each one **measurable**? (if you cannot score it, it is not a criterion)
   - Are they **mutually exclusive**? (no double-counting the same property)
   - Do they **map to the actual decision** in `scope.md`?
4. Refine and re-critique
5. Stop when criteria are concrete, weighted, and approved

Output: `criteria.md`

---

## Phase 2 — Data Gathering Loop (max 5 iterations per product)

1. For each product, collect data against every criterion in `criteria.md`
2. **Tag every single data point with its source class:**
   - `[FIRST-HAND]` — we tested it ourselves
   - `[INDEPENDENT]` — third-party benchmark, audit, or review
   - `[USER-REVIEW]` — aggregated user reports
   - `[VENDOR]` — the vendor's own docs, site, or sales material
3. Identify gaps:
   - Any criterion with **no data** for a product
   - Any criterion where the **only** data is `[VENDOR]`
4. Re-collect to fill gaps, prioritizing independent corroboration for
   vendor-only claims
5. Stop when every product has data for every criterion, or 5 iterations reached

Output: `data.md` — a matrix of product × criterion, every cell source-tagged

---

## Phase 3 — Scoring Loop (max 2 iterations)

1. Score each product against each weighted criterion
2. Write a one-line **justification** for every score, naming the data point it rests on
3. Run the bias check on every score:
   - **Marketing-only bias** — is this score based solely on `[VENDOR]` material?
   - **Brand halo** — is a known brand scoring well on criteria we have no data for?
   - **Price anchoring** — is the expensive option being scored as better by default?
   - **Recency bias** — is the newest product being over-credited for novelty?
   - **Incumbent bias** — is the existing tool getting a pass on weaknesses?
4. Rescore anything flagged
5. Stop when every score has a stated justification and passes the bias check

Output: `scores.md`

---

## Phase 4 — Verification Loop (critical — no iteration cap)

This is the loop that separates a real evaluation from a rewritten press release.

1. Extract **every factual claim** from the draft into `claims.md`:
   specs, prices, performance numbers, limitations, integrations, SLAs
2. Trace each claim to its source in `data.md`
3. Flag each claim:
   - `[UNSUPPORTED]` — no source at all
   - `[VENDOR-ONLY]` — rests solely on the vendor's own material
   - `[STALE]` — pricing or version data outside the freshness window in `scope.md`
4. Fix each flagged claim by one of:
   - Finding independent corroboration → reclassify
   - Downgrading the claim's confidence language and **labeling it in the report**
     as a vendor claim
   - Removing the claim entirely
5. Re-verify every claim after any rewrite
6. **Stop only when:**
   - Zero `[UNSUPPORTED]` claims remain
   - Zero `[STALE]` claims remain
   - Every surviving `[VENDOR-ONLY]` claim is explicitly labeled as a vendor
     claim in the report body

**Never fabricate a specification, price, or benchmark number.** If it cannot
be sourced, it does not go in the report.

Output: `claims.md` — the verification log

---

## Phase 5 — Synthesis Loop (max 2 iterations)

1. Draft the report
2. Check:
   - Does the **recommendation actually follow** from the weighted scores?
     (if the winner did not score highest, say why explicitly)
   - Are **trade-offs stated up front**, not buried in an appendix?
   - Does the **executive summary match** the detail sections?
   - Are **dissenting data points acknowledged** rather than quietly dropped?
   - Is every `[VENDOR-ONLY]` claim visibly labeled as such?
3. Fix any inconsistency
4. Stop when the recommendation is traceable back through scores → data → criteria → scope

Output: `report.md`

---

## Loop execution order

Loops are **sequential** — each must complete before the next begins:

```
Phase 0: Scope Check (once)
   ↓
Phase 1: Criteria Loop
   ↓
Phase 2: Data Gathering Loop
   ↓
Phase 3: Scoring Loop
   ↓
Phase 4: Verification Loop
   ↓
Phase 5: Synthesis Loop
```

Data gathering for **different products** may run in parallel, since products
are independent of one another. Everything else is strictly sequential.

---

## Stopping conditions

| Loop            | Stop when                                                        |
|-----------------|------------------------------------------------------------------|
| Scope Check     | All 5 scope questions answered and written to `scope.md`          |
| Criteria        | Criteria measurable, weighted, approved OR 3 iterations hit       |
| Data Gathering  | Every product × criterion cell filled OR 5 iterations hit         |
| Scoring         | Every score justified and bias-checked OR 2 iterations hit        |
| Verification    | Zero `[UNSUPPORTED]`, zero `[STALE]`, all `[VENDOR-ONLY]` labeled |
| Synthesis       | Recommendation traceable to criteria OR 2 iterations hit          |

---

## Output files

- `scope.md` — audience, decision, constraints, candidates, freshness window
- `criteria.md` — weighted evaluation criteria
- `data.md` — product × criterion matrix, every cell source-tagged
- `scores.md` — scores with per-score justifications
- `claims.md` — verification log
- `report.md` — final evaluation report

---

## Safety rules

- **Never skip Phase 0.** A thorough evaluation of the wrong question is worthless.
- **Never skip Phase 4.** Unverified claims are the primary failure mode of this
  document type.
- **Never fabricate** specs, prices, benchmark results, or feature availability.
- **Never present a vendor claim as an established fact.** Label it.
- **Never let a favorite drive the criteria.** Criteria are defined in Phase 1,
  before any product data is gathered, and are not revised after Phase 2 begins.
- If the data does not support a clear winner, **say so.** "No clear winner;
  here are the conditions under which each wins" is a valid and often correct
  conclusion.
