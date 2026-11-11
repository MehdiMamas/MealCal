# MealCal

Planning repository for a personal nutrition and health tracker meant to replace Cal AI: an iPhone app plus a web app, with Apple Health as a destination for the data.

There is no application code yet. The repo holds the specification, the roadmap and snapshots of the planned issues. Live status of each piece of work is on the [issues](https://github.com/MehdiMamas/MealCal/issues) page.

## What is planned

- **Web app**: logging, corrections, imports, dashboards, photo upload and exports. It is the only place with Today, Diary and Add screens.
- **iPhone sync app**: a small native app that is the only thing that reads and writes Apple Health (HealthKit). It pulls pending changes, writes them, and shows sync status.
- **Backend**: owns the meal ledger, photos, history and exports. The hosting and database choice is still open and is decided in [issue #6](https://github.com/MehdiMamas/MealCal/issues/6).
- **Data ownership**: versioned records, JSON and CSV export, and a correction history.

## Read this first

- [`docs/SPEC.md`](docs/SPEC.md): product specification and architecture, including what Apple Health can and cannot do.
- [`docs/ROADMAP.md`](docs/ROADMAP.md): implementation order and gates.
- [`docs/CAL-AI-PARITY.md`](docs/CAL-AI-PARITY.md) and [`docs/CAL-AI-CLAIMS.md`](docs/CAL-AI-CLAIMS.md): the Cal AI feature scope and the claims behind it.
- [`docs/issues/`](docs/issues): snapshots of the planned issue bodies.

## Running it

Nothing to run yet. The first step on the roadmap is proving, on a real iPhone, that adding, editing and deleting past entries in Apple Health works, before any backend is built.

## Tech (planned)

Web app in TypeScript, a native SwiftUI app with HealthKit, and a hosted database. The exact stack is provisional until the hosting decision above.
