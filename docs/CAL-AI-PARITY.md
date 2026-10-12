# Cal AI parity baseline and audit

## Updated product direction

The owner clarified on 2026-10-12: MealCal should reproduce the full everyday Cal AI experience, including its features, workflows, UI and UX as closely as possible, and then add the capabilities in `SPEC.md`. Success means the owner does not encounter a routine task and think, “Cal AI used to do this for me.” This clarification takes precedence over earlier optional-feature classifications where they conflict with verified Cal AI parity.

The uploaded spec remains unchanged as the original baseline. It is not a complete inventory of Cal AI. The existing R-001–R-026 issues cover that spec, not proven product parity.

## What is known and what is not

- The owner requests complete Cal AI feature and experience parity, not merely similar calorie totals.
- The owner confirmed Cal AI **26.40.0 on iPhone with a paid subscription** on 2026-10-12. This is the target baseline. iOS version, region, exact subscription offering and account-specific feature flags remain unverified; capture these if they affect observed behavior.
- There is no signed-in Cal AI session, current screen recording, screenshot set, or real-device walkthrough available in this workspace.
- Public research was attempted on 2026-10-12. Requests to `www.calai.app`, `calai.app`, `apps.apple.com` and `itunes.apple.com` were blocked by the environment's outbound proxy (HTTP 403). No page content or feature claims were verified from those attempts. Domain additions were saved to the environment draft; saving does not establish runtime access.
- Public listings and marketing screenshots can support specific advertised capabilities. They cannot prove hidden settings, paid flows, regional differences, live experiments or full coverage of an installed version.
- Exact matching of an undisclosed proprietary AI model or food database cannot be established from visual similarity. Evaluate user-visible outputs against representative fixtures, record gaps and retain honest uncertainty.
- The owner supplied a Gemini feature-list conversation. [Its 33 claim groups](CAL-AI-CLAIMS.md) are captured as unverified research leads; they do not establish current iPhone behavior or complete coverage.

## Evidence rules

Pin the target release and account context before declaring parity. Keep a source log with URL or private reference identifier, access/capture date, version/tier/region where known, and exactly what each source establishes. Separate direct observation, official public description, third-party demonstration, owner report and unverified hypothesis. If evidence conflicts, record the conflict rather than merging versions into an invented current app.

Use owner-provided redacted walkthroughs for paid or hidden paths. Do not request passwords, session tokens or production health data. This repository is public; private reference recordings and actual meal/weight screens must remain outside it. Commit only sanitized flow descriptions, synthetic examples and references suitable for public use.

## Discovery checklist — candidates to inspect, not claims of existing Cal AI features

| Area | What to inspect | Evidence status |
| --- | --- | --- |
| Onboarding and goals | Questions, units, body/goal/activity inputs, target calculation, permissions, subscription gates and returning-user flows | Unverified |
| Home and diary | Tab structure, date navigation, calorie/macronutrient cards, meal ordering, day boundaries, quick actions and progress display | Unverified |
| Food capture | In-app camera/photo scan, library upload, barcode, label scan, text/voice/manual entry and food search | Unverified |
| Scan result and correction | Loading states, recognized foods, portion editing, uncertainty, rescans, correction flow and save behavior | Unverified |
| Reuse and meal management | Favorites, recents, saved meals, recipes, repeat/copy entries, multi-item meals, deletion, undo and historical edits | Unverified |
| Nutrition and activity | Nutrient detail, goals, exercise entry, steps, Health integration and whether activity changes intake targets | Unverified |
| Progress | Weight logging, measurements/photos, charts, date ranges, trends, reports and sharing | Unverified |
| Daily convenience | Water logging, reminders, streaks, widgets, shortcuts, watch support, notifications and share actions where present | Unverified |
| Account and settings | Units, appearance, language, accessibility, exports, recovery, subscription, privacy, deletion and integrations | Unverified |
| Edge states | Empty data, denied permissions, no network, failed scans, retry, slow loading, dismissed sheets, keyboard and app relaunch | Unverified |

For every discovered feature, record: stable parity ID, evidence, trigger/preconditions, steps, result, screens/states, paid/access constraints, current MealCal issue or uncovered gap, priority, acceptance test, and pass/fail/blocked status. Add implementation issues for confirmed gaps and split the broad optional backlog into reviewable tasks when evidence warrants it. Do not invent feature issues from this candidate checklist alone.

## UI and interaction reference

Create a complete screen/state inventory and navigation map. Capture layout, typography, spacing, colors, icons, cards, charts, tab order, sheets, gestures, animations, haptics, keyboard behavior, loading/empty/error states, accessibility and device sizes. Record entry/exit behavior and tap sequences as well as static screenshots.

Recreate the interface with original implementation and assets. Keep MealCal's identity distinct; copying a screenshot is not evidence that the underlying behavior works. Use side-by-side screenshots at matching device dimensions and repeat the same tasks on both apps. Record every intentional difference for owner review instead of silently substituting a different layout.

Native iPhone is the reference experience. Responsive desktop web should preserve capability and familiar information structure while adapting to desktop input and space. Do not treat a desktop layout as a pixel-for-pixel iPhone target.

## Priority changes and conflicts to resolve

- Every verified baseline feature is required for the parity release regardless of an older P1/P2 label. Truly additional features can remain optional. Explicit owner-approved omissions are the only exceptions; silence is not agreement to drop a baseline feature.
- In-app AI/photo logging cannot be made an optional later feature if it is required to reproduce Cal AI's observed core capture flow. Resolve provider, latency, accuracy, privacy and recurring-cost implications explicitly. A ChatGPT copy/paste bridge can remain useful, but it does not establish equivalent in-app capture UX.
- Barcode, favorites, recipes, reminders and other items previously grouped as optional must be individually audited. Promote verified baseline capabilities; do not assert that every candidate exists in Cal AI.
- Preserve the requested additions: ChatGPT interface, before/after consumption, historical correction, safe HealthKit reconciliation, legacy import protection, comprehensive nutrients, desktop access and owned exports. Add them without unnecessary friction or displacement of familiar baseline actions.
- Preserve truthful Health status, no legacy duplicate writes and uncertainty semantics even when adding new UI. Record any necessary difference from the observed app clearly.
- Feature parity does not mean a private personal tool must reproduce Cal AI's commercial subscription billing. Capture paywall/account flows as evidence and resolve their applicability with the owner rather than silently building a billing system.

## Release gate

The parity release needs a pinned target, complete evidence-backed feature and screen inventories, implementation issues for all confirmed gaps, and side-by-side daily-task validation. Cover setup, first meal, repeated logging, corrections, historical navigation, progress review, reporting and every other observed baseline workflow, including relevant edge states. Compare actions/taps, outcomes, loading behavior and visual references; document unexplained friction.

Keep all unknowns visible. Public-site research alone cannot close the gate. A week of daily-use testing must produce no unexplained missing baseline workflow. Obtain owner review of intentional differences and record unresolved external prerequisites. Product acceptance scenarios T-01–T-20 remain required according to enabled scope; they do not replace the parity matrix.

## Implementation order

Begin the evidence and UI audit alongside F00. Keep the F01 real-iPhone HealthKit proof as an independent early feasibility gate. The audit must inform user-facing client design and backlog priorities before those are locked. Build verified Cal AI baseline workflows and the requested extensions through the existing architecture; do not start feature implementation during this planning task.
