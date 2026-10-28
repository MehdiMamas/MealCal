# MealCal

Planning repository for a personal nutrition and health platform meant to replace Cal AI (iPhone app plus web, with Apple Health as a destination). There is no application code, build or test suite yet; the repo holds specs and issue snapshots.

## Where things live
- `docs/SPEC.md` - product specification and architecture.
- `docs/CAL-AI-PARITY.md`, `docs/CAL-AI-CLAIMS.md` - the Cal AI feature-parity scope and the claims behind it.
- `docs/ROADMAP.md` - implementation roadmap.
- `docs/issues/` - snapshot of the planned issue bodies (F00, R001..., etc.). GitHub holds live status.
- `CLAUDE.md` - detailed working rules, ordering gates and invariants. Read it before coding.

## Workflow
- Read the parity scope, spec, roadmap and the assigned GitHub issue before coding.
- Implement one issue's scope per reviewable change; link the issue in the PR and say which checks ran.
- Intended stack (not scaffolded yet): SwiftUI/HealthKit, Next.js/TypeScript, Supabase. Do not invent toolchain versions; document real commands here once they exist.

## Gotchas
- Do not commit personal reports, real health fixtures, secrets or provider tokens.
- Only the native iPhone app touches HealthKit.
