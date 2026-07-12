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
- 🔶 API surface between the two windows — what the intake posts to the CRM backend on submit (packet shape, tokenized-link rules), PLUS the Upload Center pair (decided, see section 4): GET outstanding-items by upload token, POST file/reason per item

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
  9. Tasks are assigned to the FIRM, not to individuals, in v1 — anyone logged in can complete any Task. Accountability is a paper trail, not routing: every completion is stamped Completed By (the logged-in user, or System with the trigger named). Per-user/role assignment rules are a future feature layer
- ✅ Submission locks the intake: the Intake Package is an immutable point-in-time record (the AI Summary and attorney review are anchored to it). Post-submission documents and corrections enter ONLY through the document-upload doorway or a communication to the firm — never by re-entering the intake form
- ✅ Document review model (mapped): four piles by requested? × uploaded? — Pile 1 Required-uploaded (review first), Pile 2 Requested-uploaded, Pile 3 Requested-not-uploaded (firm rules Excuse or Keep Chasing on debtor reasons; "will send later" chases automatically), Pile 4 never-requested (not shown on the case). Nothing starts in Home Base — every Document arrives there through exactly one firm Approve click; AI accept is a recommendation, never an approval. Reject is never a dead end: it always converts into a re-request on the chase list
- ✅ "Excused" is its own status and requires a firm ruling — a debtor's "doesn't apply" waits for the firm; only Approve or Excuse closes an item. Ready for Petition Prep = every requested Document Approved or Excused
- ✅ Review completion = every uploaded Document ruled AND every skipped Document ruled (Excuse/Keep Chasing). Both review Task types stay (initial intake / follow-up uploads); SIX task types total
- ✅ Chase Task is event-driven, both directions: born the moment review completes with a non-empty chase list (due immediately — the old 1-week timer is dead), re-arms weekly while the chase holds, and auto-completes the instant the chase list empties. Triggers hang on checklist STATE, never on Task completion, so the On/Off switches can't break the chain
- ✅ One Documents tab per Lead, no separate intake-documents section — at the Lead stage every document IS an intake requirement (this version ends at Ready for Petition Prep). Multi-section document management is a Matter concept (post-conversion, v2); the intake checklist becomes the Intake folder of that structure when conversion exists
- ✅ Chase is ONE Task with TWO templates — "Document Request — Initial" first send, "Document Request — Reminder" on repeats (first-vs-repeat is a parameter per rule 7)
- ✅ Upload Center (post-submission secure upload) — DECIDED:
  - Lives in the INTAKE app as a second token-gated route (`/upload/{token}`), reusing the intake's upload components, AI screening hookup, and hosting — not a separate app, not a hidden page: gated exactly like a password-reset page
  - The intake FORM stays locked at submission; the Upload Center is the only debtor doorway afterward. Debtor-facing wording: "your intake is submitted — your secure upload link stays open until we have everything"
  - Auth: magic link + lightweight identity gate (DOB or SSN last-4, data given at intake). No passwords — mortgage-industry lesson: passwords kill completion. Forwarded email alone is not enough
  - Tokens: random, signed, per-Lead, expiring; a fresh link rides every follow-up email; scope is upload-only (can read outstanding item names + firm notes, never intake answers); revoked at Ready for Petition Prep
  - The page shows the CHASE LIST served live from the CRM — one upload slot per requested item (files arrive pre-categorized; the no-miscellaneous-pile rule applies to debtors too) plus per-item "I can't provide this" with the reason picker
  - Safeguards: TLS, virus scan, type/size limits, private per-tenant Azure Blob, audit log, uploads stamp the Lead timeline
  - Pipeline on upload: item → AI screen → ai_accepted/ai_flagged → 5:00 a.m. batch → "Review Documents — follow-up uploads" Task
- ✅ Document viewing rule (View PDF): inline-first, never a download. Click expands an inline preview under the card (first page, fit-width, scrollable) keeping the decision buttons visible; the preview carries one "Open full size" affordance that opens the browser's native PDF viewer in a NEW TAB (Content-Disposition: inline). No pop-up windows, no modals, and NO automatic downloads — files stay in the app, served via short-lived signed URLs from private Blob storage, and every view is audit-logged (who, when). A deliberate Download button is a possible later feature, never the default
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
