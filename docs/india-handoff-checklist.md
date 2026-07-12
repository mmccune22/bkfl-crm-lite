# India Handoff Checklist — BK FastLane (CRM Lite + AI Intake)

Status key: ✅ have it · 🔶 exists, needs extraction or updating before handoff · ⬜ to be drafted when it's time

## 1. Working References (the prototypes ARE the spec)
- ✅ CRM Lite — live demo (mmccune22.github.io/bkfl-crm-lite) + repo with full commit history
- ✅ AI Intake — live mockups (mccune-legal-intake-mockups) + repo
- ✅ Full CRM (bkfl-dashboard) — future-vision reference only; mark clearly as NOT in v1 scope
- ✅ Brand assets — logo, palette, typography (in the repos)

## 2. Specifications
- 🔶 Intake Flow Spec v1 (docs/bkfl-intake-flow-spec-v1.docx) — predates most of the system; needs a v2 pass covering: 4-stage model (incl. Ready for Petition Prep + regression rule), six-task rulebook, document pipeline states, AI Intake Summary vs. Intake Package, Removed-task history, firm-voice communications
- ⬜ Architecture one-pager — four layers (data / orchestrator / agents / presentation), event list, agent roster with inputs & outputs (parked as task; write when both windows feel done)
- 🔶 Task rulebook — fully written, but it lives in the app (Settings → Tasks + Lead Stages); extract into the spec verbatim
- 🔶 Document pipeline rules — same: Settings → Documents (32-type catalog, requirement tiers, reason codes, follow-up rules, category lifecycle) is the source of truth; extract
- ⬜ Data model reference — entities and fields: Lead, Contact, Task (statuses incl. Removed), docChecklist item states, intakePackage schema, communications, timeline
- ⬜ Agent output contracts — the frozen JSON memo shape for each agent (pay stub analyzer, document classifier, intake analyst, follow-up drafter)
- ⬜ API surface between the two windows — what the intake posts to the CRM backend on submit (packet shape, tokenized-link rules)

## 3. Content Assets
- ✅ Email templates ×5, firm-voiced with merge fields (Settings → Communications)
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
- ✅ Stages are system-driven, never hand-edited; 4 stages in v1: New Lead → Intake Sent → Intake Submitted → Ready for Petition Prep
- ✅ Ready for Petition Prep = the v1 finish line. Auto-set when the Document checklist is complete (every applicable item Approved by Firm or Not Applicable; nothing awaiting upload, review, or chase). It REGRESSES to Intake Submitted if new Document activity reopens the checklist — the badge tells the truth about the file. prepReadyDate is stored and the UI shows days-in-stage everywhere the badge appears (dashboard pipeline "Days Ready" column, Leads table, Lead header)
- ✅ Every AI email requires law-firm approval; every document requires firm approval (AI is a recommendation)
- ✅ Universal task rules — apply to ALL six task types, and to every future one:
  1. Tasks are created by the system (events or the 5:00 a.m. batch), never by hand
  2. An outstanding task has exactly two exits: open it and do the work, or remove it — no mark-complete shortcut exists
  3. Opening a task always shows the actual work (email draft + attachment, or document review lanes)
  4. Doing the work completes the task automatically — approve = send + log to Communications + timeline + stage advance + recurrence re-arm, via one shared rulebook (buildApprovalPatch)
  5. Completed and Removed are permanent, read-only history; Removed can be restored to Outstanding
  6. Recurring tasks re-arm only while their trigger condition still holds
  7. Per-type differences are parameters only (trigger, cadence, doorway destination) — never rule exceptions
  8. Every Task type has a firm-level On/Off switch (Settings → Tasks). Off means off for everything: every open copy of that Task (all Leads) moves to Task history as Removed, and the system stops creating new ones — restorable individually per the history rule. Templates have no switch of their own — every email leaves the system through a Task, so the Task switch IS the email switch
- ✅ Submission locks the intake: the Intake Package is an immutable point-in-time record (the AI Summary and attorney review are anchored to it). Post-submission documents and corrections enter ONLY through the document-upload doorway or a communication to the firm — never by re-entering the intake form
- 🔶 PROVISIONAL (revisit once the document workflow is mapped): document review is TWO Task types, split for staff clarity about scope — "Review Documents — initial intake submission" (immediate on submission, never repeats, the big pile) and "Review Documents — follow-up uploads" (5:00 a.m. batch, the trickle; at most one open per Lead, count refreshed). SIX task types total now
- 🔶 PROVISIONAL (same caveat): the document chase is ONE Task ("Approve document follow-up request") with TWO templates — "Document Request — Initial" on its first run, "Document Request — Reminder" on every repeat (first-vs-repeat is a parameter per rule 7)
- 🔶 5:00 a.m. single batch window — stated in-app; put the one-clock rule in the spec explicitly
- ✅ Communications come from the firm, not an individual; no custom template builder in v1
- ✅ Templates own the words; Tasks own the timing — every schedule is stated in exactly one place (the Task card / task-creation rules); Communications templates have no schedule of their own and defer to their Task
- ✅ Design principle: every AI-driven feature ships with a firm-level Off switch — some lawyers will want any given AI step turned off, and that must always be possible without removing the feature for everyone. The per-Task switches are the v1 implementation; apply the same principle to every future AI feature
- ✅ AI disclaimer: the AI Intake Summary carries a preliminary-review disclaimer in BOTH the email body and the attached PDF (the PDF travels alone) — AI-assisted, preliminary only, not legal advice, attorney will personally review, figures may change

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
