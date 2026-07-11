# India Handoff Checklist — BK FastLane (CRM Lite + AI Intake)

Status key: ✅ have it · 🔶 exists, needs extraction or updating before handoff · ⬜ to be drafted when it's time

## 1. Working References (the prototypes ARE the spec)
- ✅ CRM Lite — live demo (mmccune22.github.io/bkfl-crm-lite) + repo with full commit history
- ✅ AI Intake — live mockups (mccune-legal-intake-mockups) + repo
- ✅ Full CRM (bkfl-dashboard) — future-vision reference only; mark clearly as NOT in v1 scope
- ✅ Brand assets — logo, palette, typography (in the repos)

## 2. Specifications
- 🔶 Intake Flow Spec v1 (docs/bkfl-intake-flow-spec-v1.docx) — predates most of the system; needs a v2 pass covering: 3-stage model, five-task rulebook, document pipeline states, AI Intake Summary vs. Intake Package, Removed-task history, firm-voice communications
- ⬜ Architecture one-pager — four layers (data / orchestrator / agents / presentation), event list, agent roster with inputs & outputs (parked as task; write when both windows feel done)
- 🔶 Task rulebook — fully written, but it lives in the app (Settings → Tasks + Lead Stages); extract into the spec verbatim
- 🔶 Document pipeline rules — same: Settings → Documents (32-type catalog, requirement tiers, reason codes, follow-up rules, category lifecycle) is the source of truth; extract
- ⬜ Data model reference — entities and fields: Lead, Contact, Task (statuses incl. Removed), docChecklist item states, intakePackage schema, communications, timeline
- ⬜ Agent output contracts — the frozen JSON memo shape for each agent (pay stub analyzer, document classifier, intake analyst, follow-up drafter)
- ⬜ API surface between the two windows — what the intake posts to the CRM backend on submit (packet shape, tokenized-link rules)

## 3. Content Assets
- ✅ Email templates ×4, firm-voiced with merge fields (Settings → Communications)
- ✅ 32-document catalog with requirement tiers, "requested when" conditions, spouse rules
- ✅ Reason codes + chase/close/review semantics
- ✅ 27 SOFA questions (mirrored intake ↔ CRM)
- ✅ Intake form question set (the mockups)
- ⬜ AI prompts / skills per agent — Matt authors; English text files, versioned in the repo
- ⬜ Golden test sets + answer keys (pay stubs first) — Matt authors; the eval gate for every prompt change
- ⬜ Reference material bundles — exemption tables, median income by household size, district-practice notes

## 4. Decisions Already Made (make sure they're written down, not just built)
- ✅ Email = identity key; dedupe rules; one contact ↔ one lead at intake
- ✅ Joint filers: one intake, primary contact only; spouse doc rules
- ✅ Stages are system-driven, never hand-edited; 3 stages in v1
- ✅ Every AI email requires law-firm approval; every document requires firm approval (AI is a recommendation)
- ✅ Task doorway model: click shows the work; doing the work completes the task; NO mark-complete shortcut (removed by design — review is mandatory); Removed keeps history
- 🔶 5:00 a.m. single batch window — stated in-app; put the one-clock rule in the spec explicitly
- ✅ Communications come from the firm, not an individual; no custom template builder in v1

## 5. Enterprise / Non-Functional (mostly undiscussed — decide before handoff)
- ⬜ Security & compliance: encryption at rest/in transit, privilege considerations, data retention/deletion policy, audit logging
- ✅ Multi-tenancy: DECIDED — the existing v1 platform is multi-tenant and that model carries forward; this project changes the windows (intake + CRM), adds document upload, and new rules on top of it
- ⬜ Auth & roles: login, password reset, Attorney vs. Staff permissions (UI exists; behavior spec doesn't)
- ⬜ Integrations & accounts: hosted on Azure (existing) — email delivery service, file storage (Azure Blob), Anthropic API; document who owns which accounts and keys
- ⬜ Environments: dev / staging / production, and where the demo data lives vs. real data

## 6. Working Process with India
- ⬜ Repo access + branch workflow (the Jimmy rule: work on branches, pull before starting, main is protected)
- ⬜ Definition of done per feature: matches prototype behavior + passes acceptance criteria you write per milestone
- ⬜ Who answers questions (Matt for product/legal, Jimmy for ?) and the communication cadence
- ⬜ Eval expectations: no prompt ships without passing the golden set; critical flags require 100%

---
*Backcheck rule of thumb: if a rule exists only inside the prototype's Settings pages, it's real but fragile — extract it into the spec before handoff so it survives a rewrite.*
