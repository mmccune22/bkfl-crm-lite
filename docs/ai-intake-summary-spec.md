# AI Intake Summary — Content Specification

THE definition of what the AI Intake Summary encapsulates. This file is the control
surface: to change what goes to the client, change this file first — the prototype
(`buildIntakeSummaryHtml` in index.html), the production template, and the intake-analyst
agent prompt all follow it. (Matt, 2026-07-13.)

## What it is

1. The AI Intake Summary is the ANALYSIS of a submitted intake — distinct from the
   Intake Package, which is the raw, immutable record of every answer. The Summary is
   anchored to the locked Package.
2. It is debtor-facing but firm-gated: it reaches the debtor only as the attachment to
   the "Review & approve AI Intake Summary email" Task — created immediately on
   submission, never repeating. Nothing is sent without law firm approval.
3. One document, three views: the Lead's AI tab, the approval-modal attachment, and the
   PDF are renders of the same content. If they ever differ, that is a defect.

## Sections, in order

1. **Header** — "Prepared for {name} · Intake submitted {date} · Reviewed and approved
   by {firm}".
2. **Disclaimer** — first, before any numbers. AI-assisted, preliminary only, not legal
   advice, your attorney will personally review, figures may change.
3. **What You Told Us** — the debtor's goals and most urgent concern, quoted back.
4. **Your Numbers at a Glance** — monthly income, monthly expenses, total assets, total
   debt split secured / unsecured / priority. Deterministic sums from the Package.
5. **Preliminary Means Test** — plain language only:
   - The arithmetic is DETERMINISTIC CODE (CMI × 12 vs. the median-income table for
     household size — reference data, versioned). Agents EXTRACT pay stub figures;
     the orchestrator CALCULATES. No LLM math on numbers feeding sworn schedules.
   - States its own basis in human terms: "based on 3 of 6 months of pay stubs."
   - HARD GATE: no pay stubs → no means-test result. The section says so and asks
     for them. Partial stubs → result shown with explicit low-confidence language.
   - Wording pattern: "Based on the pay stubs you provided, your household income
     appears to be below/above the {state} median. Your attorney will confirm this."
     Never a promise of chapter eligibility or outcome.
6. **Counseling Class** — status.
7. **What Happens Next** — review expectation, a note that a separate email with a
   secure client portal link will follow IF documents are needed, and the invitation
   to correct anything wrong.

## Explicitly EXCLUDED from the client document

- **Missing-documents list** (decided 2026-07-13). The Summary Task fires immediately at
  submission, but the Chase List is BUILT by the firm's document review (rulebook rule 11)
  — it doesn't authoritatively exist yet. Missing documents have exactly one voice: the
  Document Request emails and the Client Portal. The Summary only sets the expectation
  that a separate email may follow.
- **Complexity score** — firm's eyes only; lives on the Lead's AI tab (decided
  2026-07-13: telling a stressed client "your case scored complex" invites fee anxiety
  the firm hasn't chosen to address yet).
- **Confidence scoring internals** (the number, the formula) — the Summary expresses
  confidence only as plain-language basis statements (see §5). The score itself is
  firm-facing, on the AI tab.
- **Raw intake answers** — that is the Intake Package, a separate document.
- **Exemption analysis and equity characterization** (decided 2026-07-13). The Summary
  never computes equity, applies exemptions, or states that any asset is protected or
  unprotected — the highest-liability content an auto-generated letter could carry
  (scheme election, residency look-back, title, valuation are legal judgment). "Your
  Numbers at a Glance" is purely descriptive: declared values, summed deterministically.
  Exemption analysis belongs to the future analysis layer (firm-facing, Matt-authored
  prompts, post-handoff) — not to the intake Summary in any version.
- Any advice, chapter recommendation, or outcome promise.

## The firm-facing companion (Lead's AI tab)

The AI tab shows the Summary (same render) PLUS the firm-only analysis panels:
Means Test detail with the numbers, Confidence score (data-completeness driven:
pay stub months + tax-document match), and Complexity score (rules Matt authors as
agent prompts: debt level, income relative to state median, total assets, etc.).

## Who changes what

| Change | Where |
|---|---|
| Which sections exist / order / exclusions | This file → dev change |
| Wording, tone, judgment inside AI-written text | Intake-analyst agent prompt (English file Matt authors, versioned, gated by golden tests) |
| Median tables and reference data | Reference bundle (versioned data files) |
| Prototype render | `buildIntakeSummaryHtml` in bkfl-lite/index.html — ask in the CRM session |
