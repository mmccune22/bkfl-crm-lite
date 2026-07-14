# START HERE — BK FastLane Developer Handoff

One page. Everything the development team needs lives in two GitHub repos plus one
shared media folder. The repos are the single source of truth for every rule and spec —
if a document conflicts with this structure, the repo version wins.

## The two repos

**1. CRM window — `github.com/mmccune22/bkfl-crm-lite`** (this repo)
- `index.html` — the working prototype. THE spec for behavior: every screen, task rule,
  stage transition, and email template is implemented and demo-able.
  Live demo: https://mmccune22.github.io/bkfl-crm-lite
- `docs/india-handoff-checklist.md` — the master decision record. Every architectural
  decision, the nine universal task rules, the Bundle Rule, the Client Portal design,
  what's decided vs. still open. Read this second (after this page).
- `docs/ai-intake-summary-spec.md` — the content spec for the client-facing AI Intake
  Summary: sections, means-test rules, exclusions, who changes what.
- `docs/upload-center-intake-brief.md` — the Client Portal build brief (kept in sync
  with the intake repo's DOC-LOGIC §8a).
- `docs/bkfl-intake-flow-spec-v1.docx` — original day-one spec; superseded in parts,
  pending a v2 pass (see checklist §2).
- In-app rulebooks (deliberate — the prototype teaches itself): Settings → Tasks,
  Settings → Lead Stages, Settings → Documents (the Master Document Rulebook, 19
  numbered rules), Settings → Communications.

**2. Intake window — `github.com/mmccune22/mccune-legal-intake-mockups`**
- `Intake Pages/` — every intake screen as working mockups, incl. `client-portal.html`.
- `Intake Pages/DOC-LOGIC.md` — document trigger rules, tiers, skip reasons, the
  Bundle Rule (§6.1), Client Portal spec (§8a).
- `Intake Pages/FIELD-MAP.md` — every intake field mapped to form destinations.
- Live mockups via the repo's GitHub Pages.

## The shared contract between the windows

The `doc_type` id and tier are the contract (verified in sync 2026-07-13: 32 ids, 1:1
both directions). The `files[]` bundle per doc_type (Bundle Rule). The five skip-reason
codes. The Upload/Client Portal API pair (checklist §2). Labels may differ per audience;
ids never do.

## Media folder (not in git)

Walkthrough videos and any large assets live in the shared Drive folder:
**[LINK TO SHARED FOLDER — Matt: paste here]**
- [ ] CRM walkthrough video(s) — Matt: list with links
- [ ] Intake walkthrough video(s) — Matt: list with links
Everything text-based stays in the repos — do not maintain copies of specs in Drive.

## Reference repo (context only — NOT in v1 scope)

`bkfl-dashboard` (the full CRM) is future-vision reference. Do not build from it.

## Working process

Branches, never main (see checklist §6). Matt answers product/legal questions. No AI
prompt ships without passing its golden test set. Definition of done per feature:
matches prototype behavior.
