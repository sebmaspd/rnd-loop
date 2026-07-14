---
name: research-paper
description: >
  Write a research paper with iterative loops for research, drafting,
  fact verification, and consistency checking. Invoke when user wants
  to write, draft, or research an academic or long-form paper.
when_to_use: >
  Use when the user wants to write, draft, outline, or research an
  academic paper, long-form article, or structured research document.
  Triggers on keywords: research paper, academic writing, literature
  review, citations, thesis, draft paper, write paper.
---

# Research Paper Writing Agent

## Phase 1 — Research Loop (max 5 iterations)
1. Search for sources on $ARGUMENTS
2. Evaluate each source for credibility and relevance
3. Build a sources.md file with summaries of each source
4. Identify coverage gaps
5. Re-search to fill gaps
6. Stop when coverage is comprehensive or 5 iterations reached

## Phase 2 — Outline Loop (max 3 iterations)
1. Draft outline based on sources.md
2. Critique: does the argument flow logically?
3. Are all major angles covered?
4. Restructure if needed
5. Stop when outline is solid

## Phase 3 — Section Writing Loop
For each section in the outline:
1. Write the section using sources.md for citations
2. Self-critique: clarity, argument, citations, length
3. Rewrite weak areas
4. Move to next section, carrying context forward

## Phase 4 — Verification Loop (critical)
1. Extract every factual claim into claims.md
2. For each claim, find the supporting source
3. If no source found, flag as [UNSUPPORTED]
4. Rewrite or remove unsupported claims
5. Re-verify flagged claims after rewrite
6. Stop when claims.md has zero [UNSUPPORTED] flags

## Phase 5 — Consistency Loop (max 2 iterations)
1. Check abstract vs conclusion alignment
2. Check terminology consistency throughout
3. Check citation format uniformity
4. Fix any inconsistencies
5. Output final paper to paper.md

## Loop execution order
Loops are sequential — each must complete before the next begins:
Research → Outline → Section Writing → Verification → Consistency

## Output files
- sources.md — all gathered sources with summaries
- outline.md — approved outline
- claims.md — verified claims log
- paper.md — final paper

## Stopping conditions
| Loop             | Stop when                                  |
|------------------|--------------------------------------------|
| Research         | Gaps filled OR max 5 iterations hit        |
| Outline          | Critic approves OR max 3 iterations hit    |
| Section Writing  | All sections complete                      |
| Verification     | Zero [UNSUPPORTED] claims remain           |
| Consistency      | No issues found OR max 2 iterations hit    |

## Safety rules
- Never skip the Verification Loop — every factual claim must be traced to a source
- If a source cannot be found for a claim, rewrite or remove the claim entirely
- Do not fabricate citations — flag as [UNSUPPORTED] and surface to the user
- Always carry sources.md context into each section writing iteration
