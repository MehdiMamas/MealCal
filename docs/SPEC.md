# Personal Nutrition & Health Platform
## Product Specification, Technical Architecture, and Implementation Roadmap

**Status:** Build-ready planning specification (v1.0)  
**Prepared:** 2026-10-12  
**Audience:** One-person, private-use iPhone + desktop-web product  
**Working name:** Nourish Ledger (placeholder; rename at any time)  
**Goal:** Replace Cal AI as the personal nutrition system of record, while using ChatGPT as the preferred conversational/vision interface and Apple Health as an interoperable health-data destination and activity source.

> **Non-negotiable architectural rule:** The application owns its meal ledger, photos, provenance, historical revisions, and export data. ChatGPT proposes changes. The backend records approved changes. **Only the native iPhone app reads and writes HealthKit.** A successful database edit is *not* a successful HealthKit edit until the iPhone confirms it. Health samples created by other apps, including Cal AI, are not deletable by this app.

---

## 1. Executive decision and feasibility boundaries

Build a **single-user, privacy-first app** with:

- **iOS:** Native SwiftUI client, with a native HealthKit adapter; camera/photo library, before-and-after meal images, offline queue, local notification and share extension later.
- **Web:** Next.js/TypeScript application hosted on Vercel, usable from a Windows PC and iPhone browser for logs, corrections, imports, dashboards, photo uploads, and exports.
- **Backend:** Server endpoints + Supabase PostgreSQL/Auth (free tier for personal use), plus private image object storage (Cloudflare R2 recommended for more free capacity; Supabase Storage as simpler limited alternative).
- **ChatGPT-first AI:** An MCP-compatible remote server with strongly typed nutrition tools, built now; connect it when the actual ChatGPT account supports the required writable custom app actions. Until then, structured JSON handoff/import works without subscribing to a second AI service.
- **Health synchronization:** Persistent desired-state reconciliation jobs, executed by the authorized iOS app at every launch/foreground, with best-effort background triggers. Edits can target historical dates; there is no midnight lock.
- **Data ownership:** Versioned records, exportable structured JSON/CSV, beautifully formatted PDF reports, explicit import provenance, a correction history, reversible deletions where feasible, and zero silent double-counting.

### What is possible and what is not

| Requirement | Reality / implementation |
|---|---|
| Add food dated today, yesterday, last year | **Yes.** Preserve original local date, time zone, and sample timestamps. |
| Correct/delete a past meal, reflect it in Apple Health | **Yes, for samples written by this app**, subject to current permissions and device connectivity. Delete previously written immutable samples and write their replacements. |
| Correct/delete Cal AI-created Apple Health samples | **No programmatic cross-app deletion.** Edit the app's imported ledger; Apple Health's legacy Cal AI samples remain until the user removes them manually in Health or Cal AI provides its own supported edit mechanism. |
| Edit Apple Health directly from Vercel/ChatGPT | **No.** Remote calls change desired data; the iPhone performs HealthKit operations later. |
| Read workouts, steps, activity calories and write potassium/sodium | **Yes, with permission** for the supported HealthKit types. |
| Store a photo provided only in ChatGPT automatically | **Not guaranteed.** ChatGPT may analyze an attached photo, but direct image-byte forwarding to an MCP write tool is not a generally reliable contract. Provide a native/web upload and optionally a ChatGPT-linked media upload workflow. |
| Connect a custom MCP with automatic writes from a consumer ChatGPT subscription | **Do not assume so.** As checked on 2026-10-12, full custom MCP write capability is limited to eligible Business/Enterprise/Edu configurations. Check plan access when integrating. |
| Use ChatGPT Plus subscription as free OpenAI API usage | **No.** Any later in-app OpenAI API integration has separately billed API usage. |
| Build iPhone app without App Store publication | **Yes**, using an Apple Developer account and development/Ad Hoc installation or TestFlight; native build/signing normally requires Xcode/macOS. TestFlight builds expire after 90 days. |
| Make background sync instantaneous without opening iPhone app | **Not guaranteed.** iOS schedules background execution opportunistically; surface 'Pending iPhone sync' until confirmed. |
| Store HealthKit-derived private health records in CloudKit/iCloud | **Do not use for this product.** Apple's App Review guideline 5.1.3(ii) prohibits apps from storing personal health information in iCloud. |

### Success condition

> “I tell ChatGPT: ‘I didn't actually eat the pasta I logged on April 7.’ The meal disappears from my app ledger for April 7 immediately. A pending Health sync job is created. My iPhone deletes only this app's April 7 HealthKit food nutrient samples and confirms the resulting daily totals. If it was originally Cal AI-imported, it is changed in the app only and an explicit legacy-source notice explains why its original Cal AI HealthKit entries are unchanged.”

---

## 2. Requirements catalog and acceptance contract

**P0 = required for a useful private app; P1 = next; P2 = optional enhancement.**

| ID | Priority | User-facing requirement | Acceptance criterion |
|---|---|---|---|
| R-001 | P0 | Private, single-user project, not published publicly | iOS installs on enrolled iPhone; private authenticated web app works on PC. |
| R-002 | P0 | Native iOS + responsive web application | Same authoritative meal records and dates appear on both. |
| R-003 | P0 | ChatGPT-first usage, optional built-in AI only later | All core data operations have AI-agnostic API and importable JSON. |
| R-004 | P0 | Remote MCP/plugin read/create/edit/delete tools | Stable authenticated tool definitions with explicit write capability flags. |
| R-005 | P0 | Give ChatGPT a meal description/photo, document findings | The structured extraction can be reviewed/committed; raw photo preservation has a separate upload path. |
| R-006 | P0 | Take before and after meal photos | Both photos belong to one meal, with capture times; estimate consumed portion, not just served portion. |
| R-007 | P0 | Log all known nutrients, especially potassium and sodium | Nutrients are extensible, unit-aware and distinguish measured/estimated/unknown. |
| R-008 | P0 | Read Apple Health activity data and write supported dietary samples | Real-device permissions, reading, source tracking and write acknowledgments tested. |
| R-009 | P0 | Correct or delete any historical app-owned meal in Apple Health | Own samples replaced/deleted regardless of day boundary. |
| R-010 | P0 | Late additions to old days never get ignored | Day/date is immutable in intent, but its content/totals recalculate whenever changed. |
| R-011 | P0 | Import Cal AI PDF via ChatGPT-created JSON | Validator previews full report, records source, imported dates, counts, warnings, and commits idempotently. |
| R-012 | P0 | Import Cal AI history without duplicate Health writing | Imported rows marked `health_write_policy = never_legacy`; no HealthKit write jobs created for them. |
| R-013 | P0 | Include walking and strength exercise expenditure or exclude entirely | Activity mode `off` vs Health-based modes with transparent provenance and no duplicate calorie summation. |
| R-014 | P0 | Meal calendar, daily totals, analytical charts | Past dates show corrected totals without stale caches. |
| R-015 | P0 | Export report PDF and structured data | Selectable time ranges, charts, meal tables, exercise and nutrient summaries, JSON/CSV download. |
| R-016 | P1 | Email compiled PDF like Cal AI | Share via iOS Mail initially; optional server mail delivery with free-tier provider. |
| R-017 | P0 | Minimal recurring cost, affordable photo retention | Image compression, private storage, usage caps and a documented export/delete policy. |
| R-018 | P0 | Source/audit trail and correction safety | Every import/edit/deletion stores provenance, previous revision and health-sync state. |
| R-019 | P1 | Flexible portion/leftovers model | Eat all / half / percentage / leftovers / second helping / incremental consumption. |
| R-020 | P1 | Show source-specific expenditure (walking vs workouts) | Explain which data is displayed as subsets, never add overlapping sources as if independent. |
| R-021 | P0 | Accurate date, time, timezone and DST handling | 23/25-hour days, historical late entries and travel time zones tested. |
| R-022 | P1 | Undo, merge and duplicate detection | Undo reversible app-owned edits; suspected duplicates require review. |
| R-023 | P0 | Privacy/security for food and Health data | Private buckets, row-level security, encrypted transport, revocable access and minimum data transfer. |
| R-024 | P1 | Offline iOS capture and eventually consistent Health sync | Draft saved without internet; operations safely retry without duplicate writes. |
| R-025 | P1 | 'Sync now' and visible HealthKit reconciliation log | Shows confirmed, queued, blocked, conflicting and legacy-unmanaged entries per date. |
| R-026 | P2 | Optional in-app ChatGPT-like AI | Switchable provider behind same structured API; disabled by default to avoid extra fees. |

---

## 3. Data ownership and trust model

### Sources of truth

1. **App ledger:** Canonical meals, individual foods, quantities consumed, images, nutrition per item, user edits, import provenance, user-selected settings, daily summaries computed from underlying items.
2. **Apple Health:** Authoritative source *for activity, workouts, body measurements if imported, and the actual presence/absence of HealthKit samples.* It is **not** the primary meal edit UI or the ownership record.
3. **ChatGPT:** Conversational reasoning and structured draft generation. It is **not** the sole long-term storage system and never gets unlimited Health write authority.
4. **Legacy Cal AI:** Historical imported ledger entries plus read-only HealthKit source metadata. Import does not transfer ownership of its existing HealthKit samples.

### Never do these

- Never regard a successful API response from `PATCH /meals/...` as a confirmed Apple Health edit.
- Never write an imported Cal AI row back to Apple Health by default.
- Never delete a HealthKit sample solely because it shares the same date or calories; must verify the source and your own stored object identity.
- Never write a zero-valued nutrient sample to stand for missing or unknown data.
- Never fabricate a potassium measurement with unsupported precision or pretend an AI estimate came from a food label.
- Never upload all Apple Health data by default. Limit read permissions and server-synced aggregates to relevant, consented types.
- Never count the same exercise energy once from a workout and again from Health's active-energy total.
- Never publicly expose raw meal pictures or reports via permanent unauthenticated URLs.

---

## 4. System architecture

```text
                 ChatGPT (conversation, photo interpretation)
                     | custom MCP tools IF write-enabled
                     | otherwise validated JSON/clipboard handoff
                     v
                 Remote MCP server / HTTPS API
                     |
                Auth + input validation
                     |
    +----------------+------------------+-----------------+
    |                |                  |                 |
    v                v                  v                 v
 Postgres       Private media        PDF export       Health outbox
 meal ledger    object storage       + email          desired changes
    ^                ^                  ^                 |
    |                |                  |                 v
    +------- Web app: Next.js on Vercel ------+      iPhone sync client
    |        (PC/browser, no HealthKit)              |      |
    +------- iOS app: SwiftUI ----------------------+      v
       (photo camera, local drafts, notifications)     Apple HealthKit
                                                      read/write/delete
```

**Absolute rule:** The server maintains **desired state and job status**. The iPhone performs actual HealthKit operations. Use an outbox, not direct web-to-HealthKit fiction.

### Suggested monorepo

```text
nutrition-platform/
├─ apps/
│  ├─ web/                       # Next.js App Router, dashboard, API, PDF
│  └─ ios/                       # Xcode project / SwiftUI app
│     ├─ Features/Diary/
│     ├─ Features/Camera/
│     ├─ Features/Health/
│     ├─ Features/Import/
│     └─ HealthKitAdapter/
├─ packages/
│  ├─ shared-contracts/          # TypeScript/Zod & JSON schema
│  ├─ nutrition-core/            # calculation, rounding, targets
│  ├─ import-calai/              # PDF extraction/parser, validation
│  └─ mcp-server/                # remote MCP tools backed by API
├─ supabase/
│  ├─ migrations/
│  ├─ policies/
│  └─ seed/
├─ fixtures/
│  ├─ health-sync/
│  └─ calai-redacted/
├─ docs/
│  ├─ ARCHITECTURE.md
│  ├─ PRIVACY.md
│  ├─ HEALTHKIT-SYNC.md
│  ├─ MCP-CONTRACT.md
│  └─ IMPORT-FORMAT.md
├─ scripts/
│  ├─ verify-import.ts
│  └─ export-json.ts
└─ .github/workflows/
```

### Client design choice

Prefer native SwiftUI for iPhone instead of starting with a web wrapper or React Native HealthKit bridge. SwiftUI gives the simplest, clearest access to HealthKit, camera, secure local queue and iOS permissions. Share an API and design tokens with the Next.js web UI; **do not attempt to share the HealthKit layer**. Web remains fully useful independently.

### Development hardware

Build the Next.js web/backend on Windows. Native HealthKit work needs Xcode/macOS and must be tested on a **real iPhone**. A Mac or legitimate macOS build service is a genuine requirement to plan for; having an Apple Developer membership alone does not make Windows capable of compiling/signing a SwiftUI iOS app. For private use choose direct development/Ad Hoc distribution where practicable; TestFlight is an alternate with recurring 90-day build expiry.

---

## 5. Screens and interaction specification

### iOS tabs

1. **Today:** Eaten, targets, protein/carbs/fat, fiber, sodium, **potassium**, energy burned (toggle), net energy (toggle), exercise activity and pending Health sync count.
2. **Diary:** Date switcher (past/future navigation), meal timeline and multi-food cards. Edit/delete/duplicate/log later, no midnight restriction.
3. **Add meal:** Camera before, optional after, text notes, portion scale, exact eaten amount, quick-add barcode/label/manual food, review nutrient sources, commit.
4. **Insights:** 7/30/90/custom day charts; per-nutrient trends, estimated-vs-known coverage, goals, walking/strength active energy, weight trends if enabled.
5. **Settings/Data:** Health permissions and source control, sync status, import Cal AI, export PDF/JSON/CSV, image retention, backups, privacy, account, expense limits.

### Web routes

- `/` daily dashboard with date picker and status.
- `/diary/[yyyy-mm-dd]` log and historical revisions.
- `/meals/new`, `/meals/[id]`, `/meals/[id]/history`.
- `/nutrition` all supported nutrients and coverage.
- `/activity` walking, workouts and expenditure policy.
- `/insights` comparisons and charts.
- `/imports/cal-ai` PDF/JSON import + reconciliation preview.
- `/exports` date-range, JSON/CSV/PDF, email delivery.
- `/settings/health` per-nutrient settings and sync queue (iPhone required).
- `/settings/integrations` MCP connection state and scopes.
- `/settings/privacy` deletion/backup/consent.

### UI principles

- Clean, comfortable, restrained, non-cluttered design; daily totals first, details on expansion; avoid faux scientific precision.
- Visually distinguish **logged**, **estimated**, **imported**, **Health-synced**, **Health-pending**, and **legacy Health unmanaged** by short text/icons, not only color.
- Nutrients are fully searchable. Potassium and sodium can be pinned alongside macros on every day.
- Show meal photos as before/after pair, sliding comparison, and optional leftovers thumbnail.
- Daily summaries link back to every underlying entry. Users can ask why a number changed.

---

## 6. Before/after photos and consumed-portion logic

### Workflow

1. Create `meal` with `served_at` and optional `photo_before`; assign foods and tentative quantities.
2. After eating, attach `photo_after` (optional), and enter `consumed_fraction` or leftovers by item.
3. AI can *suggest* fractions from before/after images, using food-specific analysis; default to **review required** because camera angle, occlusion and hidden ingredients make estimates uncertain.
4. Calculate nutrients from **consumed grams/servings**, not the full plate, and save that revision.
5. If no after photo exists, ask `All / Half / 25% / Custom / Unknown`; retain the uncertainty. Allow consumption to be edited next day or a month later.
6. Support multi-serving containers, sauces/toppings, mixed plates, repeated servings, leftovers consumed the next day, and non-photo text/voice logs. A leftover consumed tomorrow is a **new consumption event** linked to the original batch, not a duplicate of the original serving.

**Example:** 600 kcal served, user confirms 50% eaten → log 300 kcal and scale nutrients for that food as an initial estimate. For mixed meals where some foods are entirely eaten and others untouched, store consumed fractions **per food**, not one blanket percentage.

### Image storage and privacy

- On-device preprocessor strips EXIF GPS by default, corrects orientation, creates a 200–400 KB target optimized image (quality/size configurable) and thumbnail; original can stay in device Photos or local encrypted cache if requested.
- Maintain content hash, upload idempotency, byte limits, and `photo_role=before|after|label|barcode|other`.
- Use **private** object bucket, signed short-lived upload/download URLs, encryption at rest, authenticated ownership checks, no public URL indexing.
- Do not automatically delete user-camera-roll originals. Image deletion inside the app affects only its stored copies.
- Optional local-only image mode sacrifices consistent PC viewing unless user explicitly syncs.

---

## 7. Nutrition and micronutrient model

### First-class nutrition values

Support **calories; protein, carbohydrate, total fat; saturated, monounsaturated, polyunsaturated fat; fiber; total sugar, added sugar where known; cholesterol; sodium; potassium; calcium; iron; magnesium; phosphorus; zinc; chloride; selenium; iodine; copper; manganese; vitamins A/B1/B2/B3/B5/B6/B7/B9/B12/C/D/E/K; caffeine; water** and extensible other analytes.

**Units:** kcal for energy, g for macros, mg for sodium/potassium and common minerals, µg for some vitamins/trace elements, mL for water. A normalized `nutrient_definition` registry maps app unit to HealthKit quantity identifier/unit **only when supported**. Some fields may be app-only. Always validate range and unit; `2.4 g sodium` must equal `2400 mg sodium`, not `2.4 mg`. Salt/sodium conversion should be explicit, never silently equated.

Each nutrient amount includes:

```ts
type NutrientAmount = {
  key: string;                      // e.g., 'potassium'
  amount: number | null;            // null = unknown; 0 = explicitly reported zero
  unit: 'kcal' | 'g' | 'mg' | 'mcg' | 'mL';
  provenance: 'packaging' | 'food_database' | 'manual' |
              'ai_estimate' | 'calai_import';
  confidence: 'verified' | 'high' | 'medium' | 'low' | 'unknown';
  source_reference?: string;        // barcode/USDA ID/label/user note
  verified_by_user: boolean;
  lower_bound?: number | null;
  upper_bound?: number | null;
};
```

**Priority of evidence:** exact food packaging label → weighed portion + reliable food database (USDA FoodData Central as a source when available) → clear user-supplied portion/recipe → photo estimate. Use ChatGPT to normalize mixed descriptions, but never silently invent measurements. For potassium, estimate only where a defensible food match and portion exist, explicitly mark estimate, and do not invent nonexistent data in a Cal AI import. A missing micronutrient is **unknown**, not zero.

### HealthKit writing policy

- User chooses separately for each supported nutrient: `off`, `confirmed_only`, `confirmed_plus_approved_estimates`.
- Unknown and unreliable values stay app-only. A user explicitly approving an estimate may enable writing it with provenance; the app should make clear it is **not a lab measurement**.
- Food records in app remain detailed; HealthKit usually receives quantity samples per **meal item × supported nutrient**, with a consistent time window, or per meal if chosen and documented. Prefer per-item for isolated edits.
- Metadata includes stable app entry/nutrient ID, source version and appropriate time zone metadata; map and retain returned HealthKit UUIDs.
- Do not write net calorie expenditure as dietary intake, and never make activity energy into negative food calories.

### Example HealthKit mappings (validate with current SDK)

| App key | HealthKit quantity | Typical unit |
|---|---|---|
| calories | `dietaryEnergyConsumed` | kcal |
| protein | `dietaryProtein` | g |
| carbohydrates | `dietaryCarbohydrates` | g |
| fat | `dietaryFatTotal` | g |
| fiber | `dietaryFiber` | g |
| sugar | `dietarySugar` | g |
| sodium | `dietarySodium` | mg |
| potassium | `dietaryPotassium` | mg |
| calcium | `dietaryCalcium` | mg |
| magnesium | `dietaryMagnesium` | mg |
| water | `dietaryWater` | mL |
| caffeine | `dietaryCaffeine` | mg |

Other supported dietary identifiers should be enrolled from a registry, not guessed at runtime. Permissions are requested only for the data types actually used.

---

## 8. Apple Health integration: permissions, read, write, edit, delete

### Permissions and app setup

In Xcode: enable **HealthKit** capability, optionally HealthKit **Background Delivery**, and add `NSHealthShareUsageDescription` and `NSHealthUpdateUsageDescription`. Request separate read/write scopes by quantity type, workout type, body mass if enabled and step count/active energy. Offer a permissions explainer before the Apple system prompt. The app must tolerate denial and later revocation: HealthKit can make denied read data appear simply absent.

**Read scopes (initial):** step count, active energy burned, workouts, dietary quantities needed for overlap audits, body mass if user chooses. **Write scopes:** supported dietary nutrients chosen by the user; optional body mass only when manual entry is explicitly enabled. This product should **not** advertise unrestricted arbitrary Health record deletion/writes.

### Own-source identity and sample ledger

Track each written sample in `health_sample_links`:

```text
app_user_id
meal_item_id
nutrient_key
entry_revision
healthkit_uuid
healthkit_type
app_external_id
sample_start_utc
sample_end_utc
quantity_normalized
source_bundle_id
sync_generation
write_confirmed_at
health_deleted_at (nullable)
```

Use `HKMetadataKeyExternalUUID` to embed stable app identity. Consider the SDK's synchronization metadata keys where appropriate, but **do not assume metadata prevents every duplicate by itself**; rely on persisted links, own-source re-query and idempotent job keys. HealthKit samples are immutable: an edit means delete old **app-owned** sample(s), then save new values.

### Reliable desired-state reconciliation algorithm

1. Every app ledger mutation commits a new revision in the DB and an outbox job (`health_sync_jobs`) in the same database transaction.
2. Job captures exact meal/food IDs, old revision and new revision, historical local date/time, desired supported nutrient map and a deterministic idempotency key.
3. iPhone gets authenticated queued jobs on foreground/launch, optional background refresh, or user pressing **Sync now**. Jobs targeting old dates are not filtered out.
4. iPhone queries relevant HealthKit samples for **this app's source** and stored UUIDs/IDs; it must never use a broad date predicate without own-source verification.
5. Compare actual own-source samples with desired values. For removed/changed nutrients, delete existing **own** samples and **confirm successful deletion**; if delete fails, do not write a replacement that could double count.
6. Write missing desired samples using correctly backdated timestamps, units and metadata. Persist actual HealthKit UUIDs and mark result only after SDK completion.
7. Re-query/reconcile to verify requested source/type/date/quantity identity, then send an idempotent acknowledgment to API.
8. On transient failures retry with exponential backoff and capped frequency. On permission failures show actionable blocked state; on ambiguous partial state re-query before retrying; never blindly reinsert.
9. Server recalculates summaries immediately from the ledger; Health panel remains **pending**, **synced**, **blocked**, **needs attention**, or **legacy not managed** separately.

**Failure modes:** app killed, locked device, denied write, HealthKit error, offline queue, old UUID absent, other app deleting own samples, duplicate events, date time zone changes, two quick edits, import during queued update. Coalesce jobs by entry to newest desired revision **without losing removal of older owned samples**. Never delete all of a day's nutrition as a shortcut.

### Historical deletion scenarios

| Scenario | App ledger | Apple Health behavior |
|---|---|---|
| Add a meal for three weeks ago | Create on old day | iPhone writes app-owned historical samples after sync. |
| Edit potassium for meal last month | Replace nutrient quantity in current ledger revision | iPhone deletes old app-owned potassium sample, writes corrected one; other nutrients unchanged. |
| Delete today's meal | Soft-delete + audit entry | iPhone deletes all linked app-owned nutrient samples. |
| Delete meal from past year | Same as today | Same as today; there is no application midnight cutoff. |
| Correct imported Cal AI meal | Imported correction overlay in app, preserve original source | **No deletion of Cal AI HealthKit samples**, no automatic Health rewrite. Display legacy divergence notice. |
| Correct old Cal AI day after user manually removes its Health samples | App correction becomes canonical | Optional *explicitly initiated* migration writes replacement app-owned values only after source audit and user approval; never automatic. |

### Important distinction: no guaranteed instant remote sync

The remote server cannot initiate a HealthKit call directly. Use `HKObserverQuery`, anchored queries, background delivery if entitlement-enabled, background task scheduling, foreground refresh and a **Sync now** action. iOS may delay background launches, so **never promise that a web-only edit has already changed HealthKit**. A notification can ask the user to open the iPhone app, and the MCP `get_sync_status` tool can honestly report pending work.

### Day boundaries

Store `occurred_at_utc`, `original_tzid` (IANA zone such as `America/Chicago`), and `local_date` at creation. Determine day boundaries according to the user's selected diary time-zone policy (`event_local` or `home_timezone`). Default to a user-configured home time zone for coherent daily totals; do not use 24-hour arithmetic to compute local days across DST. On retroactive edits recompute affected daily aggregates and exported figures.

---

## 9. Activity: walking, strength training and calories burned

### Input data

- HealthKit `activeEnergyBurned` (Apple Watch / connected source as supported), `stepCount`, `HKWorkout` sessions and `bodyMass` only with consent.
- Classify workouts by HealthKit activity type: strength training, walking, running, cycling, etc. Display duration and workout-specific reported energy, when present.
- Walking measured by steps is **not automatically a standalone calorie number**. Steps and active calories may already be measuring the same effort.

### Energy inclusion settings

```ts
type ActivityMode =
  | 'off'                // show no burned calories in diet/net calculation
  | 'apple_active'       // use HealthKit's activeEnergyBurned total, workouts as breakdown
  | 'workouts_only';     // use selected, deduplicated workout energies only

type ActivitySettings = {
  mode: ActivityMode;
  includeWalkingWorkouts: boolean;
  includeStrengthWorkouts: boolean;
  includeOtherWorkouts: boolean;
  showStepCount: boolean;
  showNetCalories: boolean;
};
```

In `apple_active`, total burned = **one** source-of-truth HealthKit active energy aggregate; walking/strength sessions are explanatory subsets and must **not** be added to that total. `workouts_only` uses deduplicated selected workout energy where present. Overlapping workout energy records require source priority and deduplication, or an explicit uncertainty warning. Steps are a separate metric and must not be converted to extra calories on top of active energy. Where non-workout walking cannot be separated accurately, label it as “other active energy,” not precise “walking calories.”

### Calculations

```text
Eaten = Σ confirmed consumed calories for the selected day
BurnedForDisplay = 0 | deduplicated Apple active energy | selected workout energy
Net = Eaten - BurnedForDisplay        (if showNetCalories = true)
```

This **net** value is a simplified display metric, not total daily energy balance (resting metabolism is separate). Provide toggle **Exclude all activity calories** and allow the user to see workouts without subtracting them from nutrition.

### Import/Health duplication

Do not add calories from exported Cal AI day totals to HealthKit burned totals as though they were different events; use HealthKit as canonical for current exercise where available. Cal AI's exported burned values, if retained, are labeled *historical reported totals* only, not additional activity events. If Health data is unavailable, show unknown or legacy report values—not invented zero/expenditure.

---

## 10. Migration from Cal AI PDF and JSON handoff

### Observed report structure

The existing **82-page Cal AI summary PDF** has an overall date range, weight history and progress charts, weekly nutrition summaries, and dated daily meal rows. Daily rows include food name, calories, protein, carbohydrates, fat, fiber, sugar, sodium and time; day footer shows calories eaten, burned and net. Not all report data (especially exact photos, potassium, unique original object IDs) is provided. Some reported zero values may be missing-data placeholders. The parser must not assume otherwise.

### Import workflow

1. **Export:** Obtain the original PDF from Cal AI and retain an encrypted local copy/backup outside the app if desired.
2. **Extract:** Prefer deterministic local PDF text/table parsing in chunks; support “Upload PDF to ChatGPT → have it produce the versioned JSON schema below” as an alternate/cleanup flow. Large reports should be converted page ranges at a time to avoid truncation and hallucinated rows.
3. **Validate:** JSON Schema/Zod check; every date/time/number parsed into explicit units. Parse every daily section and row, record page and original row index. Distinguish summary/report totals from actual individual entries. Flag unknown foods, outlier weight measurements, unusual values and unreadable lines.
4. **Reconcile:** Show report day counts, per-day totals vs imported row sums, number of meals/foods, dates covered, duplicates, missing pages, no-photo warning, unparseable fields, nutrient gaps, and confidence.
5. **Preview:** User explicitly chooses import. **Default `health_write_policy=never_legacy` for all imported records**, with no HealthKit outbox events.
6. **Commit idempotently:** Generate `document_sha256`, source page/row coordinates and stable `import_fingerprint`. Same report imported twice updates an import record but does not create duplicate meals/foods. Reimporting an updated report detects changed rows and requires review, not blindly replacing corrected data.
7. **Read-only Health audit (optional):** If user allows dietary-read scope, examine HealthKit source metadata and show likely legacy Cal AI samples. Do not delete or claim exact one-to-one matching without identifiers.
8. **Use the imported history:** Web/iOS calendar now shows the complete available history. Missing image/potassium fields show `unknown`, not generated fake history.
9. **Future migration:** User may later choose a separate manual process to remove Cal AI source samples in Apple Health and deliberately re-publish corrected history from this app, with backup and preview; NEVER part of initial import.

### Imported record ownership policy

```text
origin = calai_pdf
imported_from = document_sha256 + page + row_index
app_record_editable = true
health_write_policy = never_legacy
health_owner = external_calai
health_sync_state = legacy_unmanaged
```

If the user says, “I lied; I didn't eat that on date X,” on a legacy day: remove that entry from the **app's daily totals** and mark it corrected. Apple Health still contains any Cal AI-created samples, and the app must clearly say **“Cal AI Health entries have not been changed.”** Do not try to write a negative offset, since HealthKit dietary quantities do not support universal subtraction/cancellation semantics.

### JSON shape for ChatGPT-produced migration

```json
{
  "schema_version": "1.0.0",
  "source": "calai_pdf",
  "document_sha256": "PUT_ACTUAL_SHA256_HERE",
  "date_range": {"start": "2026-01-01", "end": "2026-01-31"},
  "report_totals": {"weekly": [], "weight_history": []},
  "days": [
    {
      "local_date": "2026-01-10",
      "timezone": null,
      "reported_totals": {
        "energy_eaten_kcal": 1200,
        "energy_burned_kcal": null,
        "net_kcal": null
      },
      "foods": [
        {
          "source_page": 12,
          "source_row_index": 3,
          "name": "Example food - not from report",
          "time_local": "12:30",
          "time_precision": "minute",
          "quantity": null,
          "nutrients": {
            "energy_kcal": 400,
            "protein_g": 20,
            "carbohydrates_g": 40,
            "fat_g": 18,
            "fiber_g": null,
            "sugar_g": null,
            "sodium_mg": null,
            "potassium_mg": null
          },
          "quality": {"extracted": true, "verified": false}
        }
      ],
      "discrepancies": []
    }
  ],
  "import_policy": {
    "create_healthkit_samples": false,
    "default_health_write_policy": "never_legacy"
  }
}
```

The numbers above are **synthetic format examples**, not an extracted user meal. ChatGPT should produce exactly this contract or a versioned successor, and the validator must reject absent source IDs, malformed units or `create_healthkit_samples:true` for a Cal AI import.

### Large-PDF safety checks

- Work in page batches; record per-page success/failure, count every date heading, count all food rows, detect skipped pages or truncated ChatGPT responses.
- Compare computed day sums to reported calorie total with a rounding tolerance and list every mismatch. Do not overwrite original numbers to force an equality.
- Deduplicate by document fingerprint + page + row index; **not** only food name/time/calories (two matching foods in the same minute may be real).
- Report tables with 0mg sodium can mean genuine zero or missing historical data; preserve reported values and original-quality annotation.
- Preserve time without inventing missing seconds/time zone. Never re-create photographs from descriptions.
- Weight outlier detection should flag unusually large discontinuities for review; don't delete/import-correct without evidence.

---

## 11. Detailed persistent data model

**Database:** PostgreSQL with UUID primary keys, `user_id` ownership, soft deletes, transaction timestamps, and row-level security. Fields listed below are core, not exhaustive.

| Table | Key columns / purpose |
|---|---|
| `users` | auth ID, timezone, account metadata |
| `settings` | targets, pinned nutrients, energy modes, photo/export policies, Health consent version |
| `meals` | ID, user ID, day, name, occurred/served/completed times, tzid, notes, origin, current revision, status |
| `meal_items` | parent meal, food name, raw/served grams, consumed grams/fraction, food database refs, per-item revision |
| `nutrient_definitions` | key, canonical unit, HealthKit identifier if supported, grouping/display precision |
| `nutrient_values` | meal item, nutrient key, decimal amount, normalized unit, source and confidence, original evidence |
| `meal_media` | role (`before`,`after`,`label`...), object key, hash, capture time, secure ACL, dimensions |
| `meal_revisions` | full before/after snapshot, actor (`user`,`chatgpt`,`import`), reason, created time |
| `imports` | Cal AI PDF SHA256, schema version, imported range, extracted counts, warnings, status |
| `import_rows` | import ID, source page/row, fingerprint, target meal-item ID, parser confidence |
| `health_sample_links` | owning app entry/nutrient/revision, HealthKit UUID, source, written/removed times |
| `health_sync_jobs` | idempotency key, desired revision, action, status, attempt count, last error |
| `health_snapshots` | explicitly consented activity totals/sessions or provenance audits, with source and freshness |
| `workout_cache` | workouts and source references where user allows local/cloud persistence; avoid copies if unnecessary |
| `audit_events` | who, action, target, original vs new value, success status, timestamp |
| `exports` | report range, template version, checksum, short-lived document object key, delivery status |
| `mcp_authorizations` | OAuth grant metadata, scopes, revocation times; encrypted secret references, no raw tokens in logs |

Use unique constraints on `(user_id, import_fingerprint)`, `(user_id, idempotency_key)`, and `(user_id, app_external_id, nutrient_key, active_revision)` as appropriate. A normal `DELETE /meals/{id}` should make a tombstone and queue Health deletion, **not** erase the data needed to identify existing HealthKit samples. Permanent account deletion later purges media/records after processing any pending own-source removal and explaining remaining health data.

### TypeScript canonical meal contract

```ts
type HealthWritePolicy = 'enabled' | 'disabled' | 'never_legacy';
type SyncStatus = 'not_requested' | 'queued' | 'in_progress' |
  'synced' | 'blocked_permission' | 'conflict' | 'legacy_unmanaged';

type Meal = {
  id: string;
  origin: 'manual' | 'chatgpt' | 'calai_pdf' | 'api';
  localDate: string;                 // YYYY-MM-DD
  timezone: string;                  // IANA timezone
  occurredAtUTC: string;
  revision: number;
  title: string;
  status: 'draft' | 'confirmed' | 'deleted';
  healthWritePolicy: HealthWritePolicy;
  healthSyncStatus: SyncStatus;
  items: Array<{
    id: string;
    foodName: string;
    servedGrams: number | null;
    consumedGrams: number | null;
    consumedFraction: number | null;
    nutrition: NutrientAmount[];
  }>;
  photos: Array<{
    id: string;
    role: 'before' | 'after' | 'label' | 'other';
    mediaKey: string;
  }>;
};
```

---

## 12. REST API and authentication

### Core endpoints (v1)

```http
GET    /api/v1/days/{date}
GET    /api/v1/meals?from=&to=&cursor=
POST   /api/v1/meals/drafts
POST   /api/v1/meals
GET    /api/v1/meals/{id}
PATCH  /api/v1/meals/{id}             # If-Match revision required
DELETE /api/v1/meals/{id}             # soft-delete and enqueue Health outbox
POST   /api/v1/meals/{id}/restore
POST   /api/v1/media/upload-intent   # signed, short-lived upload
POST   /api/v1/media/complete
POST   /api/v1/imports/calai/preview
POST   /api/v1/imports/calai/commit
GET    /api/v1/imports/{id}
GET    /api/v1/activity?from=&to=
GET    /api/v1/nutrients?from=&to=
GET    /api/v1/health/sync-jobs?state=queued
POST   /api/v1/health/sync-jobs/{id}/ack
GET    /api/v1/health/status
POST   /api/v1/health/reconcile-request
POST   /api/v1/exports
GET    /api/v1/exports/{id}
POST   /api/v1/exports/{id}/email
GET    /api/v1/account/data-export
```

All mutating endpoints: enforce authentication and ownership, server-side Zod validation, payload size limits, rate limits, explicit `Idempotency-Key`, audit events, and `If-Match`/revision conflict handling. Use signed uploads rather than passing camera images through the serverless function body. Health acknowledgments must be accepted only from authenticated iOS sessions with a bound device credential; do not allow arbitrary web/MCP code to claim HealthKit success.

**Security:** Supabase Auth single private user + MFA, Supabase RLS on every private table, no service-role keys in client bundles, rate-limited MCP and API, HTTPS, signed R2 operations, secret encryption and scheduled backup export. Guard against prompt injection in food photo text, imported PDFs or tool results: those are **data**, not instructions. Do not expose audit logs or exports via shared/public GitHub repositories.

---

## 13. ChatGPT MCP / plugin specification

### Integration reality as of 2026-10-12

OpenAI's developer documentation says custom MCP apps with **full read/write capabilities** are in beta for eligible Business/Enterprise/Edu accounts. Pro accounts may have read/fetch developer-mode access; do not assume Plus permits custom write tools. Custom MCP access is currently documented for **ChatGPT web**, not mobile; treat mobile support as an independently verified future enhancement. This limitation is in ChatGPT's account and client support, **not** a limitation of the app's own API. The MCP server remains valuable and reusable by other clients/agents that do support write tools.

### Tool contracts

| Tool | Input / effect | Permission |
|---|---|---|
| `nutrition_get_day` | `{date}` → daily totals, meals, exercise, provenance, sync | read |
| `nutrition_search_meals` | `{from,to,query}` → list IDs/food labels | read |
| `nutrition_get_meal` | `{meal_id}` → item-level nutrition and revisions | read |
| `nutrition_get_nutrient_trends` | `{nutrients,date_range}` | read |
| `nutrition_get_health_sync_status` | `{date?,meal_id?}` → queued/confirmed/blocked | read |
| `nutrition_create_meal_draft` | structured meal proposal, estimates with sources | write-draft |
| `nutrition_commit_meal` | `{draft_id,expected_revision}` | write |
| `nutrition_update_meal` | `{meal_id,patch,expected_revision,reason}` | write |
| `nutrition_delete_meal` | `{meal_id,expected_revision,reason}` | destructive write |
| `nutrition_attach_photo_reference` | `{meal_id,media_asset_id,role}` | write |
| `nutrition_request_media_upload` | create signed app-upload intent; **not** an assumption that ChatGPT can upload its attachment bytes | write |
| `nutrition_preview_calai_import` | versioned JSON → warnings/conflicts/coverage | write-draft |
| `nutrition_commit_calai_import` | `{import_preview_id,acknowledge_no_health_write:true}` | write |
| `nutrition_set_activity_mode` | explicit settings change | write |
| `nutrition_create_report` | `{from,to,format,sections}` → report ID/link | write |
| `nutrition_email_report` | `{report_id,destination}` → delivery queued | write with confirmation |

### Conversation contract

ChatGPT should ask for missing critical facts only, and may say:

> “I can log this as approximately 600 kcal with estimated potassium. Do you want to save it, or is this just an example?”

A statement that is clearly an instruction (“Add yesterday's salmon and rice”) can create a confirmed entry according to the user's chosen automation level. A hypothetical example must never be committed as real food. Deleting or replacing old records should validate target ID/date and show affected nutrients and sync implications. In ambiguous cases, create draft and ask. The assistant may report **“Saved in the app; Apple Health sync pending”** and should never report **“Apple Health updated”** before phone acknowledgment. Legacy Cal AI corrections must return an explicit external-source note.

### Photo-input handling caveat

A user may attach an image to a ChatGPT conversation. ChatGPT can derive a structured meal interpretation, and the MCP tool can submit that **text/JSON**. The underlying attachment's raw image bytes are **not automatically guaranteed to be forwarded** to your remote MCP. To preserve original media:

1. Upload via iPhone/web app first, receive private media asset ID, then discuss it with ChatGPT; or
2. When a chat-side Apps SDK UI supports a user-driven media upload, have the UI upload directly to the app; or
3. Attach to ChatGPT for interpretation, then also upload/copy to app through a share-sheet or web screen.

The backend can provide short-lived signed access to images for an authorized AI tool when supported and consented. Avoid pretending that analysis in ChatGPT automatically means the photo has been permanently stored.

### Zero-additional-AI-cost bridge for individual ChatGPT plans

- In ChatGPT, attach image/describe meal → ask for JSON in the documented meal schema.
- Copy JSON or save `.json` file → open `/imports/chatgpt` web/iOS sheet → paste/share → validate → confirm.
- Commit creates outbox job; iPhone eventually syncs HealthKit.
- For an old meal: use app-generated stable meal ID in the ChatGPT conversation, ask for a **patch JSON** with `meal_id` and `expected_revision`; paste to app; verify preview; apply.

This is not the ideal one-step experience, but it works now with ordinary ChatGPT and no separate OpenAI API charges. Optional later: built-in AI using OpenAI API, separately billed and disabled by default, to deliver the exact same experience entirely inside iOS/web.

### MCP authentication

For supported ChatGPT connector deployment, implement production-quality remote Streamable HTTP MCP with OAuth 2.1/OIDC as applicable, scoped tokens (`nutrition:read`, `nutrition:write`, `nutrition:delete`, `reports:send`), rotating refresh tokens, revocation and tool-level action confirmations. The private single-user backend must still enforce the same authorization on raw REST endpoints. **Never expose a raw unrestricted API key inside a prompt or shareable ChatGPT conversation.**

---

## 14. Analytics and calorie math

### Daily overview

- Calories eaten + personal calorie target.
- Protein, carbs, fat, fiber, sugar.
- **Potassium and sodium pinned by default**, plus coverage indicator (e.g., “potassium known for 2/4 foods” rather than implying a perfect daily total).
- Active burned calories / workouts / steps only when permitted; configurable exclusion.
- Net calories separately, off by default if preferred.
- Meal image comparison, source/confidence badges and app-vs-HealthKit sync status.

### Analytics

- Daily and rolling 7/30/90-day trends for food energy, nutrients, exercise and weight (if enabled).
- Nutrient target attainment and % of foods with available data; weekly averages should account for no-log days separately from known zero-intake days.
- Photos with date and eaten fraction; meal categories/times; favorites.
- Highlight whether potassium value is label-based, databased or AI estimated.
- Before/after changes: show corrections as a revision diff and which HealthKit quantities changed.
- Graph aggregation can use cached summaries, but invalidate and recompute when historical records change. Exports must use **current corrected data**, not pre-import PDF totals.
- Cal AI imported data can feed trend charts without causing additional HealthKit writes; provenance filters let the user compare imported history with app-native history.

### Calculation precision

Use decimal-safe stored amounts for nutrients; convert into HealthKit units at the boundary. Keep raw per-food nutrient amounts; total is a **derived quantity**, never independently mutable. Define goal defaults but let user edit. Do not use calorie or potassium goals as medical guidance unless user explicitly chooses them; minimize health advice from estimated data.

---

## 15. Export to PDF, email and structured backups

### Report template

Generate a polished, print-styled **HTML report**, render to PDF, and optionally return the HTML as well. It should include:

1. Cover: user-selected display name (optional), date range, generated time, data coverage.
2. Overview: energy eaten, activity if enabled, net calories if enabled, nutrient targets and averages.
3. Day-by-day food tables: names, time, actual consumed portions, calories, macros, fiber, sugar, **sodium, potassium**, other available nutrients. Mark missing as `—`, not `0`.
4. Before/after photos when explicitly selected (adds file size; default compressed thumbnails).
5. Activity: steps/walking/strength sessions and energy source, with the selected inclusion policy and no double counting.
6. Weekly/monthly charts and weight history if opted in.
7. Corrections/import notes, estimated-data legend, last Apple Health reconciliation status and legacy source disclaimer.
8. Machine-readable export link/file: all raw ledger entries, revisions and source metadata (exclude sensitive secrets).

### Delivery options

- **P0:** Download PDF from web; share PDF via native iOS `ShareLink`/Mail composer. No server-side email provider needed. Export JSON and CSV alongside it.
- **P1:** Automatic “Email report to me” via Resend (free-tier transactional email quotas; domain verification and deliverability need configuration) or equivalent. Use a short-lived authenticated download link if the PDF is too large for a mail attachment; avoid indefinite public links.
- **P2:** Weekly/monthly scheduling, but only if explicit schedule is configured and service limits/privacy review pass.

Include `report_version` and `generated_at`. Regenerating a historical report after correction must reflect current app state. Exports of legacy data should not falsely claim Apple Health has been updated.

---

## 16. Hosting and cost strategy

The objective is **$0 incremental hosting per month under free-tier usage**, *not* a guarantee that every provider offers unlimited free storage forever. Paid Apple Developer membership is already assumed. Monitor terms and caps at deployment.

| Component | Choice | Indicative personal-use cost / limit | Notes |
|---|---|---|---|
| Website and server routes | Vercel Hobby | $0 while within noncommercial hobby limits | API and PDF rendering subject to function/time limits. |
| PostgreSQL + sign-in | Supabase Free | $0, 500 MB DB, 2 active free projects | Free projects may pause after 1 week inactivity. Enable export backups; no free managed PITR. |
| Photos (preferred) | Cloudflare R2 Standard | $0 within **10 GB-month**, plus 1M Class A/10M Class B ops monthly | Private bucket, signed URLs. Beyond free may charge; set billing alert/stop mechanisms. |
| Photos (simplest alternative) | Supabase Storage Free | $0 within **1 GB file storage** | Easier integration, earlier capacity pressure; usage over free can block. |
| PDF generation | HTML + headless browser or print renderer | $0 within runtime limits | Test Vercel function bundle/CPU/time; local client PDF fallback. |
| PDF email | Native Share Sheet first, optional Resend | $0; Resend free tier currently 3,000 emails/mo (100/day) | Never need mass emails for personal use. |
| Remote MCP | Vercel route (or alternate compatible host) | $0 if transport/runtime supports the client's MCP session requirements | Test streaming/timeouts; don't assume every serverless transport works unchanged. |
| AI | Existing ChatGPT usage | No extra API cost **for manual ChatGPT JSON workflow** | Direct custom MCP writes dependent on plan. Optional API usage billed separately. |
| iOS app deployment | Existing Apple Developer membership | No additional publishing fee for private install | Mac/Xcode access is separate practical prerequisite. |

**Photo capacity example:** Six retained images/day at 250 KB each ≈ 547.5 MB/year, before thumbnails and overhead. 1 GB storage may fill relatively quickly; 10 GB gives far more runway. Actual usage depends on photos, originals, copies and compression. Set an in-app storage gauge, maximum original size, configurable local retention, per-year archive export, and backup/export before deletion.

**Do not use CloudKit/iCloud as the Health-data backend.** While CloudKit technically exposes native and web databases, Apple's published App Review guidance says not to store personal health information in iCloud. The chosen cloud database must also follow Apple's separate requirements for informed consent, data security and restricted third-party sharing. Since this is personal use, keep the service private, but build to the stricter privacy standard anyway.

### Backup and spending controls

- Nightly/weekly user-triggered **JSON + CSV + media manifest** export; optional local encrypted archive; checksum and restore test quarterly.
- Database dump to encrypted local drive; keep photos retrievable without provider lock-in.
- HealthKit isn't a backup for detailed meals, before/after photos, untracked nutrients or undo history.
- Add usage dashboard and alerts for Supabase DB, R2 storage/API, Vercel limits and transactional emails; verify each provider's exact spend cap behavior rather than assuming all costs are hard capped at $0.
- Do not automatically ingest full resolution iPhone images forever. Original retained locally when needed; synced copy compressed and deletable.

---

## 17. Privacy, safety and reliability requirements

- Keep all photos, nutrition, potentially sensitive health trends and exports private by default. No ads, tracking pixels, third-party analytics collection or app-health-data monetization.
- Ask **separately** to (a) read HealthKit; (b) write HealthKit; (c) sync summarized Health data to private cloud; (d) share selected data/photos with ChatGPT; (e) send PDF via email. Separate toggles and revocation paths.
- Encrypt traffic (TLS); provider encryption at rest; keys not in source control; MFA and secure iOS Keychain token storage; do not store health data in NSUserDefaults.
- Avoid writing inaccurate data: AI is a best-effort estimator, not a database of verified lab values. Use thresholds and consent for estimates; preserve unknowns.
- Server-side validation of authenticated identity and user IDs, private objects, input units, media types, max quantities, dates, time zones and imported JSON.
- Upload/parse PDF in a sandboxed workflow with malicious-PDF defensive constraints and limit sizes; text inside PDFs/photos is not executable instructions.
- Idempotency across network retries and app crashes, outbox replays, eventual consistency and double-clicks.
- No production reliance on HealthKit Simulator for background behavior; real iPhone end-to-end acceptance testing mandatory.
- Every destructive change allows preview or clear undo path when possible. Deletion of app-owned Health samples cannot be undone without rewriting new ones, and restored samples have new UUIDs.
- Show what the app cannot control: denied read permission may be indistinguishable from no data; other apps' samples remain their own; external apps may update/delete their own records independently.
- Explicitly follow Apple HealthKit privacy and disclosure rules even if not going through App Store Review at first.

---

## 18. Implementation roadmap: phased, gated, testable

**Ordering principle:** Prove the hardest feasibility risk (real-device HealthKit historical correction) early. Do not spend weeks polishing screens before the Health engine works.

### Phase 00 — Lock contract and test environment (half day–2 days)

- [ ] Choose working app name and bundle ID, repo privacy and primary timezone behavior.
- [ ] Decide single-user authentication path, expected Apple Health permissions and per-nutrient write policy.
- [ ] Confirm access to macOS/Xcode and an enrolled physical iPhone.
- [ ] Capture sanitized PDF samples and sample meal stories without committing personal report to GitHub.
- [ ] Define R-001 through R-026 as tracked issues, plus test matrix.
- **Gate:** health-capable build path and user/privacy decisions written down.

### Phase 01 — HealthKit proof of concept FIRST (2–4 days)

- [ ] Create minimal SwiftUI app with HealthKit entitlement and explicit permissions.
- [ ] Save a **test** meal with calories, protein, sodium and potassium for a historical date, on physical phone.
- [ ] Read back own-source sample UUID/type/date/value and source revision.
- [ ] Replace past potassium sample by verified own-source delete + save; verify daily Health quantities.
- [ ] Delete entire test meal and verify all app-owned samples are gone.
- [ ] Demonstrate that Cal AI source entries are not treated as writable/deletable by your app.
- **Gate:** own-source add/change/delete works across a past-day boundary on an actual iPhone; no deletion of third-party data.

### Phase 02 — Database, shared schema, auth (3–5 days)

- [ ] Set up private monorepo, Next.js app, Supabase project and migrations.
- [ ] Create schema tables, constraints, source ownership policies, RLS and audit events.
- [ ] Implement canonical nutrients registry and Zod schemas with shared JSON Schema export.
- [ ] Set one private user, MFA, secure sessions, basic encrypted local backup.
- [ ] Add test fixtures for duplicate submissions, timezone and nutrient conversions.
- **Gate:** transaction/permission/schema tests pass; second anonymous user cannot read any private row.

### Phase 03 — Basic web and iOS meal ledger (4–7 days)

- [ ] Build diary calendar, add/edit/delete meals, food line items, servings and nutrition totals.
- [ ] Add revisions, tombstones, full history and optimistic concurrency.
- [ ] Build SwiftUI Today/Diary/Add screens against same backend.
- [ ] Confirm historical edits invalidate cache and update every chart/total.
- [ ] Implement manual entry before any AI integration.
- **Gate:** identical meal IDs and corrected totals in PC and iPhone web/native; historical data changes never stale.

### Phase 04 — Durable HealthKit sync engine (4–8 days)

- [ ] Add outbox jobs + HealthKit sample-link ledger + idempotent version processing.
- [ ] Implement iOS queued reconciler, per-nutrient unit mappings, and failed/blocked states.
- [ ] Add source verification, handling for missing UUIDs, coalescing and tombstone processing.
- [ ] Add `Sync now`, foreground worker and best-effort background features.
- [ ] Implement Health permissions/revocation UI and audit trace; test app kill/restart mid-sync.
- **Gate:** 100 repeated retries or rapid historical edits cause **zero duplicate app-owned samples**; all displayed sync statuses true.

### Phase 05 — Before/after photos and partial consumption (3–6 days)

- [ ] Add camera/library picker, before/after/timestamp and per-food fraction.
- [ ] Configure private storage bucket, signed upload URLs, metadata-stripping transforms and thumbnails.
- [ ] Add consumed-portion derivation/review screen and leftovers-linked consumption events.
- [ ] Test offline photo draft and upload retry; delete remote media securely.
- **Gate:** eat-half use case updates own-source Health nutrients to 50% (after approval) and both photos appear on PC.

### Phase 06 — Cal AI PDF import and migration protection (4–8 days)

- [ ] Build deterministic PDF extractor + JSON contract validator; support ChatGPT-created JSON upload.
- [ ] Parse date headings, meal rows, weekly summaries, weight series and missing fields.
- [ ] Import preview: counts, discrepancies, duplicates, source confidence and no-photo warning.
- [ ] Set `never_legacy`; hard-block imported rows from Health sync outbox.
- [ ] Test report re-import, malformed JSON and legacy meal correction; health source audit optional.
- **Gate:** repeated import is idempotent, every parsed row traceable, and **zero HealthKit writes** arise from initial Cal AI migration.

### Phase 07 — Comprehensive nutrients and activity policies (3–6 days)

- [ ] Complete nutrient registry, potassium/sodium UI, confidence and estimated-data policy.
- [ ] Read HealthKit `activeEnergyBurned`, workouts and steps with permissions.
- [ ] Add `off`, `apple_active`, `workouts_only`, selected activity types and net display switch.
- [ ] Implement non-duplication logic and explanatory breakdown.
- **Gate:** Apple active calories never added twice when the same workout also exists; exercise can be excluded entirely.

### Phase 08 — Exports and email (3–5 days)

- [ ] Build designed report HTML with print CSS and deterministic data aggregations.
- [ ] Generate downloadable PDF + JSON + CSV; include potassium/sodium and provenance.
- [ ] Implement iOS share-to-Mail path.
- [ ] Optional Resend verified sender and private downloadable PDF link/attachment.
- [ ] Compare exported historical totals to corrected app ledger; test long report pagination.
- **Gate:** chosen date range exports accurately and can reach user's email without exposing a public permanent file.

### Phase 09 — MCP server and ChatGPT interaction (3–7 days)

- [ ] Implement typed MCP read tools backed by same API and secured with scopes.
- [ ] Implement create-draft/commit/update/delete and report tools with validation and confirmations.
- [ ] Test current ChatGPT plan support; do not block shipping on unsupported custom-write actions.
- [ ] Implement `/imports/chatgpt` clipboard/JSON fallback for ChatGPT Plus/unsupported scenarios.
- [ ] Add status truthfulness tests (`pending` vs `Health updated`) and adversarial prompt injection tests.
- **Gate:** exact user commands create/patch the intended date in DB, Health queued and later confirmed on iPhone; no silent write from hypotheticals.

### Phase 10 — Analytics, polish and privacy hardening (4–8 days)

- [ ] Add polished Today, Insights, nutrient coverage and workout breakdown dashboards.
- [ ] Add revision diff, import origin, estimates, sync status and conflicts.
- [ ] Implement photo archive/storage meter, consent controls, sensitive logs scrubber, delete export.
- [ ] Accessibility, dark mode, local date formatting, performance and mobile web layout.
- **Gate:** 30/90-day report compares correctly, screens remain responsive, accessibility and privacy checks pass.

### Phase 11 — Production-style validation and daily use (3–7 days)

- [ ] Run all acceptance scenarios (below), including timezone boundaries and stale devices.
- [ ] Fresh install + account recovery + restore from backup test.
- [ ] Confirm Vercel deployment, one-user lockdown, image privacy, secrets and rate limits.
- [ ] Install privately on iPhone; run at least a week alongside Cal AI with **no duplicate Health writes**.
- [ ] Choose migration cutover date; keep legacy source read-only and disable Cal AI future Health writes in its settings if available/desired.
- **Gate:** daily driver usable without requiring manual database repairs; no inaccurate sync success messages.

### Phase 12 — Optional later enhancements (not launch blockers)

- [ ] In-app OpenAI API vision/chat provider (extra API cost), selectable/off by default.
- [ ] Voice logging, barcode lookup, recipe imports, on-device OCR, food favorites.
- [ ] Interactive ChatGPT app UI with user-authorized direct media uploads, where available.
- [ ] iOS share extension and Shortcuts intent for logs.
- [ ] Scheduled emailed weekly/monthly PDFs and mobile notifications.
- [ ] More accurate individual food portion estimation, AI uncertainty calibration.
- [ ] Export to other health/logging standards and self-hosting mode if privacy/cost needs change.

**Rough duration:** 5–10 focused developer weeks for a credible private-use release, highly dependent on Xcode/iPhone access and the Cal AI parser quality. Phases can overlap, but the Phase 01 and 04 gates should never be skipped. These are engineering estimates, not promises.

---

## 19. Critical end-to-end acceptance scenarios

| Test | Steps | Required outcome |
|---|---|---|
| T-01 Historical correction | Log meal on April 7; sync; change from 600 to 400 kcal weeks later | App April 7 becomes 400; iPhone deletes old **own** energy sample and saves new; no 1,000 kcal double count. |
| T-02 Historical deletion | Delete app-created meal months later | DB tombstone and own-source Health samples removed after phone sync; other meals untouched. |
| T-03 Offline correction | Change yesterday's food from PC while iPhone offline | App shows corrected day; Health is pending, not falsely synced; later iPhone catches up. |
| T-04 Rapid revisions | Modify meal three times before phone sync | iPhone ends with latest desired values; no orphan or duplicate older samples. |
| T-05 Cal AI import | Import same 82-page PDF twice | One logical record per source row; zero duplicate app records; zero new HealthKit samples. |
| T-06 Legacy correction | Delete imported Cal AI food | App historical day corrected; original Cal AI HealthKit data left untouched and clearly labeled. |
| T-07 Manual legacy takeover | After backing up and removing legacy samples manually, explicitly migrate a selected day | Verified selected replacement app-owned samples only; no silent bulk rewriting. |
| T-08 Before/after | Before photo of full plate, after photo of half left, approve 50% | Both images retained, food amounts scaled per item, Health receives consumed not served amount. |
| T-09 Missing potassium | Import row with no potassium | UI displays `unknown`; no Health potassium sample written. |
| T-10 Estimated potassium | AI estimates potassium with credible source; user approves | App shows estimate and source, writes only if user's estimate-write policy permits it. |
| T-11 Burned exclusion | Toggle mode off | Daily net does not subtract workout/active energy, but meal totals unchanged. |
| T-12 Apple active energy | Walking and strength workout overlap with active energy data | Display active energy once; workout breakdown informational, no double subtraction. |
| T-13 Permission revoked | Revoke sodium Health write permission | App record saved; outbox `blocked_permission`; no false synced report. |
| T-14 Date and DST | Record food around midnight and daylight saving switch | Correct chosen diary date and Apple sample timestamps. |
| T-15 Unknown vs zero | Report has unknown value and actual zero | Different UI/DB semantics; no false nutrient totals. |
| T-16 Export correctness | Correct last year's entry then generate PDF | PDF reflects revised item and totals; provenance shown. |
| T-17 ChatGPT image workflow | Attach image only in ChatGPT | Structured draft can be made; UI honestly requests app media upload if actual bytes unavailable. |
| T-18 Unauthorized access | Try unauthenticated API/private image URL/MCP | Denied; no leakage of food, activity, user ID or reports. |
| T-19 Idempotent retries | Kill app while old Health nutrient deletion succeeded but save not done | Reconciliation eventually completes without adding duplicates; failure visible pending. |
| T-20 Report by email | Generate selected date-range PDF, mail to self | Actual readable PDF delivered or link valid; no publicly permanent attachment URL. |

---

## 20. First implementation sprint (copy into your project tracker)

**Sprint A: prove the product is possible**

- [ ] Create a private GitHub repository named after the product.
- [ ] Add this spec to `docs/SPEC.md` and a `PROGRESS.md` file.
- [ ] Check available Mac/Xcode/iPhone build process and Apple team signing.
- [ ] Create SwiftUI sandbox app and HealthKit capability.
- [ ] Request read/write permissions for `dietaryEnergyConsumed`, `dietaryProtein`, `dietarySodium`, `dietaryPotassium`.
- [ ] Write a test meal to a **past date** (not today), read its Apple Health data, capture UUIDs.
- [ ] Edit the test meal, delete only its original app-owned quantities and save updated ones.
- [ ] Delete the test meal and verify Apple Health accurately reflects the deletion.
- [ ] Record all errors and caveats in `docs/HEALTHKIT-SYNC.md`.
- [ ] **Only after the health gate passes**, scaffold Next.js + Supabase database.

**Sprint B: get the first working end-to-end ledger**

- [ ] Create account and PostgreSQL meal/nutrient/revision tables.
- [ ] Implement web create/edit/delete by historical date.
- [ ] Implement iOS API client + Health outbox pull/ack.
- [ ] Test changing an old meal on PC and later seeing Apple Health corrected on iPhone.
- [ ] Add potassium/sodium cards and concise sync statuses.
- [ ] Commit functional milestone and run against T-01/T-02/T-03/T-13/T-19.

---

## 21. Decisions, known risks and open questions

### Defaults chosen for v1

- Native iOS SwiftUI rather than a PWA-only solution.
- Next.js on Vercel; PostgreSQL/Supabase; optional R2 private photo storage.
- Single-user system with MFA, no public sign-up.
- All historical dates editable; no midnight cutoff.
- Never import Cal AI food history into HealthKit automatically.
- One HealthKit quantity sample per meal item × supported nutrient, keyed to own-source IDs.
- No automatic write of unreviewed uncertain nutrient estimates.
- Energy inclusion defaults to `off` until user chooses a Health source, with separate net toggle.
- Preserve all supported nutrients and explicitly unknown values, not just macros.
- ChatGPT remains the preferred AI interface; API-costly in-app AI is optional, not a prerequisite.
- Existing ChatGPT write-connector access is **capability-gated**; fallback is validated JSON paste/share.
- Preserve both before/after images when supplied; after photo is optional; what was consumed is always user-correctable.

### Risks to track

- **High:** Consumer ChatGPT account lacks direct custom write MCP tool availability. Build connector in parallel, provide fallback.
- **High:** iOS background execution/authorization delays make instant HealthKit propagation impossible to guarantee. Honest pending status mandatory.
- **High:** Legacy Cal AI HealthKit samples cannot be removed by own app. User-initiated manual cleanup needed for exact cross-app history migration.
- **High:** Photo-only food identification, portion sizing and micronutrients are estimates. Explicit provenance and user approval required for reliable Health write behavior.
- **Medium:** Hidden original PDF fields/row extraction irregularities; import completeness audited by pages and totals.
- **Medium:** Free-tier quotas and temporary service pauses; build backup, compression, monitoring and restore procedures.
- **Medium:** Native Xcode/macOS requirement if primary development machine is Windows.
- **Medium:** Third-party health information sharing, Apple's HealthKit/iCloud rule and provider privacy policies require careful consent/data boundaries.

### Choices you can make after the HealthKit proof-of-concept

1. **Photo budget:** R2 (more free capacity, separate setup) vs Supabase Storage (simpler, much smaller limit).
2. **Daily expenditure default:** off, Apple active energy, or workouts only; independently enable net energy display.
3. **Estimated potassium policy:** app-only until manually confirmed, or Health-write after explicit approval.
4. **Personal report delivery:** Mail Share Sheet only, or automatic send via Resend.
5. **Private install:** direct Xcode/Ad Hoc or periodically refreshed TestFlight.
6. **AI connector:** wait for supported ChatGPT writable custom MCP, use JSON handoff now, or later pay separate OpenAI API costs for native in-app chat.

---

## 22. Official documentation / verification sources

Verified during spec preparation on 2026-10-12. These links are reference points; provider prices and ChatGPT plan features can change.

**Apple**

- HealthKit setup and permissions: https://developer.apple.com/documentation/healthkit/setting-up-healthkit
- HealthKit privacy, permissions and denial behavior: https://developer.apple.com/documentation/healthkit/protecting-user-privacy
- HealthKit store operations, including own-source object deletion: https://developer.apple.com/documentation/healthkit/hkhealthstore
- HealthKit object immutability: https://developer.apple.com/documentation/healthkit/hkobject
- Nutrition identifiers (including potassium and sodium): https://developer.apple.com/documentation/healthkit/nutrition-type-identifiers
- HealthKit source metadata: https://developer.apple.com/documentation/healthkit/hksource
- HealthKit external UUID metadata: https://developer.apple.com/documentation/healthkit/hkmetadatakeyexternaluuid
- Observer queries/background delivery: https://developer.apple.com/documentation/healthkit/executing-observer-queries
- Active energy definition: https://developer.apple.com/documentation/healthkit/hkquantitytypeidentifier/activeenergyburned
- Apple App Review privacy rules (5.1.3; iCloud prohibition): https://developer.apple.com/app-store/review/guidelines/
- Developer Ad Hoc devices: https://developer.apple.com/help/account/devices/devices-overview
- TestFlight lifecycle: https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview

**OpenAI**

- ChatGPT MCP developer mode capabilities and plan restrictions: https://help.openai.com/en/articles/12584461-developer-mode-and-mcp-apps-in-chatgpt
- Apps SDK preview: https://help.openai.com/en/articles/12515353-build-with-the-apps-sdk

**Hosting and email**

- Vercel Hobby: https://vercel.com/docs/plans/hobby
- Supabase pricing/free limits: https://supabase.com/pricing
- Cloudflare R2 Standard free tier: https://developers.cloudflare.com/r2/pricing/
- Resend email pricing: https://resend.com/pricing

---

## Final architecture invariant

**App ledger changed** → backend immediately records the corrected meal, revisions and desired Health state → **iPhone's authorized HealthKit adapter** reconciles and confirms its own samples → dashboards and ChatGPT tool results distinguish `app_saved` from `health_synced` → legacy Cal AI entries remain source-tagged and never create duplicate HealthKit writes.

This invariant is the foundation of the entire product; no user-facing feature should circumvent it.
