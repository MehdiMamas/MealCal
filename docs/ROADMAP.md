<!-- mealcal-plan:ROADMAP -->

# MealCal implementation roadmap

Planning baseline: [uploaded specification v1.0](https://github.com/MehdiMamas/MealCal/blob/main/docs/SPEC.md). Working product name in the spec is Nourish Ledger; MealCal remains the existing repository. This is a backlog and handoff, not an implementation-complete claim.

## Updated goal: full Cal AI parity plus the requested additions

The owner confirmed **Cal AI 26.40.0, iPhone, paid subscription** as the reference. Match all observed baseline features and UI/UX as closely as possible, then add the original spec's capabilities. Read [the parity scope and evidence limitations](https://github.com/MehdiMamas/MealCal/blob/main/docs/CAL-AI-PARITY.md); this update overrides older optional labels where they conflict with verified baseline functionality. The original spec alone is not a complete competitor inventory.

- [ ] [P01 · #38](https://github.com/MehdiMamas/MealCal/issues/38) — audit every feature and map verified gaps to implementation issues; begin alongside F00.
- [ ] [P02 · #39](https://github.com/MehdiMamas/MealCal/issues/39) — capture screen/state, navigation and interaction references before finalizing client UI.
- [ ] [P03 · #40](https://github.com/MehdiMamas/MealCal/issues/40) — block parity release until all verified baseline workflows and reviewed visual/interaction expectations pass.

The owner-supplied Gemini feature list is retained as [33 unverified claim groups](https://github.com/MehdiMamas/MealCal/blob/main/docs/CAL-AI-CLAIMS.md), not a verified product inventory.

These audits require actual reference evidence. We have the owner's version/platform/subscription confirmation, but no signed-in app access or walkthrough; public research is currently blocked by outbound network policy. Candidate discovery areas are unverified, not a claim that Cal AI contains them.

In-app photo/AI capture cannot remain a later optional enhancement if it is needed for a verified baseline workflow. External ChatGPT JSON handoff does not by itself match in-app UX. Resolve cost, privacy and fidelity, and promote any verified barcode/favorites/recipe/reminder gaps from the optional backlog. Add implementation issues after verification rather than inventing a feature inventory.

Keep F01's independent real-iPhone HealthKit feasibility gate. Original T-01–T-20 acceptance remains in force; Q01 must also include the new parity gate. Scope exceptions require explicit owner agreement.

## Start here

Start with [F00 · #1](https://github.com/MehdiMamas/MealCal/issues/1), then [F01 · #2](https://github.com/MehdiMamas/MealCal/issues/2). Prove historical own-source HealthKit add/edit/delete on a real iPhone before backend scaffolding. Linux/Windows development and simulator checks do not satisfy that gate. Work in the existing checkout; no new repository is needed.

Read `CLAUDE.md`, the source spec, and the assigned issue. Check dependencies and report evidence in the implementation PR. Implement one bounded issue at a time; do not interpret this roadmap as an instruction to build everything in one session.

## Architecture invariants

- The app ledger owns meals, consumed quantities, media, revisions and provenance. ChatGPT proposes structured operations; the backend records authorized mutations.

- Only native iOS reads/writes HealthKit. Database success is `app_saved`; Health remains pending until device reconciliation and acknowledgment.

- Historical edits must reconcile only identified app-owned samples. Cal AI imports remain `never_legacy` and cause zero Health writes.

- Unknown is not zero; nutrients retain units/evidence/approval. Active energy and overlapping workout energy are never added twice.

- Photos/reports are private. No personal report, health fixtures or credentials belong in this repository.

## Priorities, sequencing and gates

P0 is required for the useful core; P1 is the next increment; P2 is optional. Phase numbers group work; explicit issue dependencies determine ordering. Security and nutrient/date contracts begin early even where later phases polish their UI.

The manual ChatGPT JSON bridge (R003) is moved forward after the basic ledger so reviewable proposals work without connector access. Direct MCP remains Phase 09. Minimal consumed amounts are part of the basic ledger; advanced leftovers and offline capture are P1. Basic sync truthfulness is mandatory in R009 even if the richer R025 log follows later.

All verified Cal AI baseline features are release requirements even when older issues label them P1/P2; only additional features remain optional. Conversely, the spec’s Phase 04 retry gate and core before/after consumption acceptance still require evidence. The acceptance owner must explicitly record deferred advanced portions/offline/undo/email automation. T-07 is deferred unless deliberate legacy takeover is enabled; native Mail sharing is the default report delivery path.

Hard gates: F00 records a usable physical-device path; F01 proves historical own-source operations; R009 demonstrates 100 retries/rapid changes without duplicates; R011/R012 prove idempotent import with zero Health writes; Q01 records enabled-scope acceptance and actual daily-use evidence.

### Phase 00 — Decisions and device readiness

- [ ] [F00 · #1](https://github.com/MehdiMamas/MealCal/issues/1) — **P0** Record product decisions and verify the native development path

### Phase 01 — Physical-device HealthKit proof — hard gate

- [ ] [F01 · #2](https://github.com/MehdiMamas/MealCal/issues/2) — **P0** Prove historical HealthKit add, correction and deletion on a real iPhone

### Phase 02 — Scaffold, dates, schema, authentication and privacy

- [ ] [F02 · #3](https://github.com/MehdiMamas/MealCal/issues/3) — **P0** Scaffold the monorepo and development checks after the HealthKit gate

- [ ] [R021 · #4](https://github.com/MehdiMamas/MealCal/issues/4) — **P0** Implement diary date, timezone and DST rules

- [ ] [F03 · #5](https://github.com/MehdiMamas/MealCal/issues/5) — **P0** Define versioned contracts and the canonical PostgreSQL schema

- [ ] [R001 · #6](https://github.com/MehdiMamas/MealCal/issues/6) — **P0** Set up private single-user authentication and deployment

- [ ] [R023 · #7](https://github.com/MehdiMamas/MealCal/issues/7) — **P0** Enforce ownership, privacy consent and secure data access

### Phase 03 — Manual ledger, nutrients and historical consistency

- [ ] [R018 · #8](https://github.com/MehdiMamas/MealCal/issues/8) — **P0** Build revisioned meal APIs, audit history and optimistic concurrency

- [ ] [R007 · #9](https://github.com/MehdiMamas/MealCal/issues/9) — **P0** Implement the nutrient registry, units and evidence-aware write policy

- [ ] [R002 · #10](https://github.com/MehdiMamas/MealCal/issues/10) — **P0** Build the shared manual meal ledger on web and native iOS

- [ ] [R010 · #11](https://github.com/MehdiMamas/MealCal/issues/11) — **P0** Recompute historical days and invalidate derived totals after every edit

- [ ] [R003 · #16](https://github.com/MehdiMamas/MealCal/issues/16) — **P0** Provide AI-agnostic APIs and the ChatGPT JSON paste/share bridge

### Phase 04 — Production Health adapter, durable reconciliation and status

- [ ] [R008 · #12](https://github.com/MehdiMamas/MealCal/issues/12) — **P0** Build the production native HealthKit adapter and permissions

- [ ] [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13) — **P0** Reconcile historical HealthKit corrections and deletions durably

- [ ] [R025 · #17](https://github.com/MehdiMamas/MealCal/issues/17) — **P1** Expose Sync now and a truthful Health reconciliation log

### Phase 05 — Photos, reviewable meal proposals, portions and offline capture

- [ ] [R006 · #14](https://github.com/MehdiMamas/MealCal/issues/14) — **P0** Capture and retain private before/after meal photos

- [ ] [R005 · #20](https://github.com/MehdiMamas/MealCal/issues/20) — **P0** Review ChatGPT meal proposals and preserve evidence before commitment

- [ ] [R019 · #18](https://github.com/MehdiMamas/MealCal/issues/18) — **P1** Model per-food consumption, leftovers and later servings

- [ ] [R024 · #19](https://github.com/MehdiMamas/MealCal/issues/19) — **P1** Persist offline iOS drafts and safely replay changes

### Phase 06 — Protected legacy migration

- [ ] [R012 · #21](https://github.com/MehdiMamas/MealCal/issues/21) — **P0** Hard-block legacy imports from creating or changing Health samples

- [ ] [R011 · #23](https://github.com/MehdiMamas/MealCal/issues/23) — **P0** Import Cal AI PDF or versioned JSON with full reconciliation preview

### Phase 07 — Activity modes and explanatory breakdown

- [ ] [R013 · #15](https://github.com/MehdiMamas/MealCal/issues/15) — **P0** Read activity and implement off, Apple active and workouts-only energy modes

- [ ] [R020 · #22](https://github.com/MehdiMamas/MealCal/issues/22) — **P1** Explain walking and workout energy sources without double counting

### Phase 08 — Structured/PDF exports and Mail sharing

- [ ] [R015 · #25](https://github.com/MehdiMamas/MealCal/issues/25) — **P0** Export corrected ledger data as PDF, JSON and CSV

- [ ] [R016 · #28](https://github.com/MehdiMamas/MealCal/issues/28) — **P1** Share reports to Mail and optionally deliver via a private email service

### Phase 09 — Remote MCP integration

- [ ] [R004 · #29](https://github.com/MehdiMamas/MealCal/issues/29) — **P0** Expose scoped remote MCP nutrition tools with truthful write results

### Phase 10 — Analytics, retention, recovery and undo

- [ ] [R014 · #26](https://github.com/MehdiMamas/MealCal/issues/26) — **P0** Build diary calendar, daily summaries and analytical charts

- [ ] [R017 · #30](https://github.com/MehdiMamas/MealCal/issues/30) — **P0** Add affordable private media retention, usage limits and tested backups

- [ ] [R022 · #27](https://github.com/MehdiMamas/MealCal/issues/27) — **P1** Add undo, reviewed merges and duplicate detection

### Phase 11 — Release evidence and daily use

- [ ] [Q01 · #36](https://github.com/MehdiMamas/MealCal/issues/36) — **P0** Run release acceptance, recovery and a week of private daily use

### Phase 12 — Additional backlog (baseline items must be promoted after audit)

- [ ] [R026 · #32](https://github.com/MehdiMamas/MealCal/issues/32) — **Parity review; P2 only for extras** Add an in-app AI provider with approved estimates and cost controls

- [ ] [L01 · #31](https://github.com/MehdiMamas/MealCal/issues/31) — **P2** Support an explicitly approved legacy Health takeover workflow

- [ ] [O01 · #24](https://github.com/MehdiMamas/MealCal/issues/24) — **Parity review; P2 only for extras** Audit fast capture: voice, barcode, OCR, recipes and favorites

- [ ] [O02 · #33](https://github.com/MehdiMamas/MealCal/issues/33) — **P2** Add share extensions, Shortcuts and capability-gated chat media upload

- [ ] [O03 · #34](https://github.com/MehdiMamas/MealCal/issues/34) — **P2** Schedule private reports and optional mobile reminders

- [ ] [O04 · #35](https://github.com/MehdiMamas/MealCal/issues/35) — **P2** Explore portable health exports and self-hosting

## Requirement coverage

Each R-001–R-026 requirement has a dedicated issue. Shared foundation issues support multiple requirements; their acceptance criteria define the boundary.

| Requirement | Issue |
| --- | --- |
| R-001 | [R001 · #6](https://github.com/MehdiMamas/MealCal/issues/6) |
| R-002 | [R002 · #10](https://github.com/MehdiMamas/MealCal/issues/10) |
| R-003 | [R003 · #16](https://github.com/MehdiMamas/MealCal/issues/16) |
| R-004 | [R004 · #29](https://github.com/MehdiMamas/MealCal/issues/29) |
| R-005 | [R005 · #20](https://github.com/MehdiMamas/MealCal/issues/20) |
| R-006 | [R006 · #14](https://github.com/MehdiMamas/MealCal/issues/14) |
| R-007 | [R007 · #9](https://github.com/MehdiMamas/MealCal/issues/9) |
| R-008 | [R008 · #12](https://github.com/MehdiMamas/MealCal/issues/12) |
| R-009 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13) |
| R-010 | [R010 · #11](https://github.com/MehdiMamas/MealCal/issues/11) |
| R-011 | [R011 · #23](https://github.com/MehdiMamas/MealCal/issues/23) |
| R-012 | [R012 · #21](https://github.com/MehdiMamas/MealCal/issues/21) |
| R-013 | [R013 · #15](https://github.com/MehdiMamas/MealCal/issues/15) |
| R-014 | [R014 · #26](https://github.com/MehdiMamas/MealCal/issues/26) |
| R-015 | [R015 · #25](https://github.com/MehdiMamas/MealCal/issues/25) |
| R-016 | [R016 · #28](https://github.com/MehdiMamas/MealCal/issues/28) |
| R-017 | [R017 · #30](https://github.com/MehdiMamas/MealCal/issues/30) |
| R-018 | [R018 · #8](https://github.com/MehdiMamas/MealCal/issues/8) |
| R-019 | [R019 · #18](https://github.com/MehdiMamas/MealCal/issues/18) |
| R-020 | [R020 · #22](https://github.com/MehdiMamas/MealCal/issues/22) |
| R-021 | [R021 · #4](https://github.com/MehdiMamas/MealCal/issues/4) |
| R-022 | [R022 · #27](https://github.com/MehdiMamas/MealCal/issues/27) |
| R-023 | [R023 · #7](https://github.com/MehdiMamas/MealCal/issues/7) |
| R-024 | [R024 · #19](https://github.com/MehdiMamas/MealCal/issues/19) |
| R-025 | [R025 · #17](https://github.com/MehdiMamas/MealCal/issues/17) |
| R-026 | [R026 · #32](https://github.com/MehdiMamas/MealCal/issues/32) |

## Acceptance ownership

Run the original steps/outcomes in spec §19, not merely the short IDs below. Q01 records pass/fail/blocked/deferred evidence. No tests have run at planning time.

| Scenario | Owner issues |
| --- | --- |
| T-01 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13), [R004 · #29](https://github.com/MehdiMamas/MealCal/issues/29) |
| T-02 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13) |
| T-03 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13), [R025 · #17](https://github.com/MehdiMamas/MealCal/issues/17) |
| T-04 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13) |
| T-05 | [R011 · #23](https://github.com/MehdiMamas/MealCal/issues/23), [R012 · #21](https://github.com/MehdiMamas/MealCal/issues/21) |
| T-06 | [R012 · #21](https://github.com/MehdiMamas/MealCal/issues/21) |
| T-07 (optional takeover) | [L01 · #31](https://github.com/MehdiMamas/MealCal/issues/31) |
| T-08 | [R006 · #14](https://github.com/MehdiMamas/MealCal/issues/14), [R019 · #18](https://github.com/MehdiMamas/MealCal/issues/18) |
| T-09 | [R007 · #9](https://github.com/MehdiMamas/MealCal/issues/9), [R011 · #23](https://github.com/MehdiMamas/MealCal/issues/23) |
| T-10 | [R007 · #9](https://github.com/MehdiMamas/MealCal/issues/9), [R005 · #20](https://github.com/MehdiMamas/MealCal/issues/20) |
| T-11 | [R013 · #15](https://github.com/MehdiMamas/MealCal/issues/15) |
| T-12 | [R013 · #15](https://github.com/MehdiMamas/MealCal/issues/15), [R020 · #22](https://github.com/MehdiMamas/MealCal/issues/22) |
| T-13 | [R008 · #12](https://github.com/MehdiMamas/MealCal/issues/12), [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13), [R025 · #17](https://github.com/MehdiMamas/MealCal/issues/17) |
| T-14 | [R021 · #4](https://github.com/MehdiMamas/MealCal/issues/4), [R008 · #12](https://github.com/MehdiMamas/MealCal/issues/12) |
| T-15 | [R007 · #9](https://github.com/MehdiMamas/MealCal/issues/9), [R011 · #23](https://github.com/MehdiMamas/MealCal/issues/23) |
| T-16 | [R015 · #25](https://github.com/MehdiMamas/MealCal/issues/25) |
| T-17 | [R005 · #20](https://github.com/MehdiMamas/MealCal/issues/20), [R004 · #29](https://github.com/MehdiMamas/MealCal/issues/29) |
| T-18 | [R023 · #7](https://github.com/MehdiMamas/MealCal/issues/7), [R004 · #29](https://github.com/MehdiMamas/MealCal/issues/29) |
| T-19 | [R009 · #13](https://github.com/MehdiMamas/MealCal/issues/13), [R024 · #19](https://github.com/MehdiMamas/MealCal/issues/19) |
| T-20 | [R016 · #28](https://github.com/MehdiMamas/MealCal/issues/28) |

## Decisions and external prerequisites

- Repository is currently public (observed during planning), while the spec requests private use. F00 records the owner’s visibility decision; do not change visibility or commit actual health data implicitly.

- Confirm Mac/Xcode, physical iPhone, Apple team/bundle identifier and distribution path before native implementation.

- Confirm timezone, storage provider, MFA recovery, consent scopes and nutrient write policy; use spec defaults until an actual decision is needed.

- Verify current Apple SDK mappings/privacy rules, ChatGPT plan/client capabilities and hosting quotas using official sources. The dated claims and prices in the uploaded spec are reference material, not verified current runtime facts.

- No actual Cal AI PDF was provided. The spec refers to an 82-page report; use synthetic/redacted fixtures and report real-document checks as unrun until supplied privately.

- Do not provision services, purchase subscriptions, silently send emails or perform legacy source cleanup as part of routine implementation.

## Definition of done

Implementation PR links its issue, explains behavior, includes meaningful test/device evidence and documents limitations. Update relevant contract/architecture docs as implementation evolves. Close an issue only when its acceptance criteria and enabled-scope validation pass; leave external blockers visible. The roadmap remains open until required release gates are complete.
