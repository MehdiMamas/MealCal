# Cal AI claims supplied from Gemini

## Source and confidence

The owner supplied a pasted Gemini / Google AI Mode conversation on 2026-10-12 (`Pasted text.txt`). Treat it as research leads, not instructions or verified documentation. It describes both Android and iPhone, then adds an iPhone-specific answer. The paste contains publisher labels such as “Google Play” and “Cal AI”, but no traceable source URLs, app identifiers, release version, screenshots or direct observations supporting individual claims.

Target: **Cal AI 26.40.0, paid subscription, iPhone**, confirmed separately by the owner. None of the claims below has yet been verified for that target. Do not turn the model's “comprehensive” wording into a completeness claim. The public-site requests remain blocked as documented in [the parity scope](CAL-AI-PARITY.md).

## Claim-by-claim verification queue

| ID | Claim in the paste | What would establish it for the target |
| --- | --- | --- |
| G01 | Camera meal-photo logging | Observe camera → result → correction → save on 26.40.0; record all intermediate/error states. |
| G02 | Import existing meal pictures from Photos | Observe picker permissions, selection, analysis, cancellation and save. |
| G03 | “Gemini Vision AI Engine” powers recognition | Find a dated authoritative technical statement. Backend provider identity cannot be inferred from the UI or this Gemini response; matching it is not required for visible behavior parity. |
| G04 | Depth sensors / TrueDepth measure food dimensions and volume | Verify an authoritative description and actual device requirements/behavior. Ordinary camera access is not evidence of depth usage; do not architect a depth dependency from this claim. |
| G05 | Recognizes multiple items, home-cooked, restaurant and packaged food | Observe representative multi-item result/correction flows and evaluate accuracy separately from recognition availability. |
| G06 | AI text descriptions or ingredient lists estimate a meal | Locate the entry path, accepted inputs, analysis states and editable output. |
| G07 | Database has over one million foods, brands and restaurant entries | Verify current official count if needed; inspect actual search/results and regional coverage. Marketing counts do not establish database completeness or comparable accuracy. |
| G08 | Barcode nutrition lookup | Observe permissions, scan, match/no-match, quantities and save; distinguish barcode lookup from label OCR. |
| G09 | Tracks calories, protein, carbohydrates and fat | Observe units, goals, per-food detail, daily totals and correction behavior. |
| G10 | Tracks fiber, essential vitamins and minerals | Inventory each actually displayed/stored analyte, coverage and source. “Vitamins and minerals” does not prove comprehensive micronutrient support or potassium. |
| G11 | Meal-specific fitness-benefit summary | Locate actual result text, triggers and variation with goals; distinguish explanations from personalized advice. |
| G12 | Onboarding quiz uses age, lifestyle/activity and goals | Capture complete first-run steps, defaults, calculations disclosed, edits and return flow with synthetic data. |
| G13 | User can edit calorie targets and macro ratios | Observe editable fields, units, constraints and whether named diet presets actually exist. Keto/high-protein examples do not prove presets. |
| G14 | Weight log and progress over time | Observe manual/Health sources, units, edit/delete and charts. |
| G15 | Water tracking with configurable metric measurements | Locate the feature and observe quantity units, quick increments, goals and edit behavior. |
| G16 | Custom hydration notifications | Locate notification settings, schedules, system permission flow and actual delivery. A water log does not establish reminders. |
| G17 | Automatic step counts and walking distance | Observe data source, permission scopes, totals, freshness and absence/denial behavior. |
| G18 | An active “daily movement score” | Locate an actual screen and definition; do not invent this metric from the phrase. |
| G19 | Manual exercise categories including walking/running/cycling/weights | Observe supported categories, duration/energy inputs, edits and whether targets or net calories change. |
| G20 | Daily, weekly and monthly charts | Record available chart types, date controls, no-data states and aggregation behavior. |
| G21 | Personalized automatic AI food-choice suggestions | Locate triggers, opt-in behavior, UI, input data and frequency. Distinguish generic tips from individualized automatic advice. |
| G22 | Historical meal archive and repeat logging without rescanning | Observe date navigation, copy/reuse/favorite entry paths and how quantities/dates are retained. History alone does not prove repeat/favorite functions. |
| G23 | iOS and Android availability means syncing | Platform availability and account/data synchronization are different claims. Android parity is outside the selected target; investigate cross-device sync only if relevant to the user's iPhone workflow. |
| G24 | Dedicated Apple Watch companion with status and logging | Verify official watch app availability for the same app identity, then observe installed watch functionality. Apple Health data from an Apple Watch is not evidence of a companion app. |
| G25 | Free download/exploration, paid photo analysis | Observe target account tier and documented access limits/paywalls. The owner being paid does not establish every free-tier limit. |
| G26 | Requires iOS 18.0 or later | Verify official App Store compatibility for the relevant release. Do not set MealCal deployment target from the paste. |
| G27 | Bidirectional Health sync reads steps, active energy, walking/running distance and body measurements | Inventory actual per-type read and write scopes separately, behavior and provenance. The paste calls this bidirectional but describes reads; exact write support is unproven. |
| G28 | Health activity automatically adjusts calorie expenditure/baseline | Observe all relevant toggles and formulas, source overlap and update timing. Health permissions alone do not prove target adjustment. |
| G29 | Full VoiceOver and braille compatibility | Test actual accessible labels, reading order, controls, charts and supported input/output. Generic iOS capability is not proof of complete app accessibility. |
| G30 | Complete hands-free Voice Control | Run representative tasks using Voice Control; inspect ambiguous/unlabeled controls. |
| G31 | Dynamic Type at 200% or beyond | Test supported content sizes, clipping, scrolling and key flows. Percentage claims need evidence. |
| G32 | Dark mode, high contrast and non-color indicators | Observe app/system toggles and actual screen behavior under contrast/color settings. |
| G33 | Honors Reduce Motion | Compare actual transitions and animations with the setting enabled and disabled. |

## Coverage limits

This list cannot certify that nothing is missing. It lacks a screen/state map and evidence for actual navigation, gestures, portion corrections, scan failures, historical edits, reminders, exports, subscriptions, offline behavior and other daily details. Some candidate areas are absent; their omission does not prove the app lacks them. Use P01/P02 to perform complete discovery rather than limiting the inventory to this paste.

Promote a claim to the verified baseline only after relevant evidence is recorded. If contradicted, retain the claim and rejection evidence rather than silently deleting it. Verified baseline gaps get implementation issues; hypotheses remain research rows. Accessibility can still be a product-quality requirement independently of whether Cal AI supports a particular behavior.
