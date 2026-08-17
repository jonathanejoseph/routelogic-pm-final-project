# Experimentation Plan (Module 5)

## Get your documents ready
- **From M3, your hypothesis sentence:** If we replace the dispatch reassignment flow with instant push notification and live driver acknowledgment (collapsing today's 8–15 minute silent gap), then dispatchers will stop relying on WhatsApp as the real system for reassignments, because the core blocker — not knowing whether the driver has received and seen the change — will be resolved inside RouteLogic itself.
- **From M3, your primary success metric & guardrail metric:** Primary: Reassignment-to-acknowledgment time — time from dispatcher confirming a reassignment to driver acknowledging it in-app. Baseline: 8–15 min (BUG-2044). Target: <30 sec push delivery, <2 min acknowledgment.
Guardrail: Mis-delivered / failed-stop rate must not increase — ensures the speed fix doesn't come at the cost of accuracy.
- **From M4, the feature you scoped in your PRD this is what you're testing:** Real-Time Reassignment Push & Acknowledgment — instant push notification to the driver's device the moment a dispatcher confirms a reassignment, with a single-tap in-app acknowledgment, and a live status view (Sent → Delivered → Acknowledged) on the dispatcher's screen.

## Define your experiment parameters
- **Feature under test pull from your M4 PRD:** Real-time push notification sent to the driver's device the instant a dispatcher confirms a route reassignment, plus a single-tap in-app "Got it" acknowledgment, with a live Sent→Delivered→Acknowledged status shown on the dispatcher's screen.
- **Persona pull your M2 persona:** Fleet Dispatcher at a mid-size 3PL who reassigns routes in real time as road conditions change.
- **Expected outcome the behaviour change you expect, from your M3 hypothesis:** Dispatchers stop manually notifying drivers through the WhatsApp group for reassignments, because in-app confirmation now closes the "did they get it" gap that WhatsApp was covering for.
- **Primary success metric the one number that defines success, from M3:** % of reassignments acknowledged in-app within 2 minutes of dispatch.
- **Baseline rate today's rate of your primary metric, from your M3 data:** 0% — there is currently no acknowledgment mechanism at all (BUG-2044 confirms no push notification and no confirmation signal exists today).
- **Guardrail metric & boundary what must not break, and how far it can move before you investigate:** Mis-delivered/failed-stop rate must not increase by more than 1 percentage point above its pre-test baseline. (Placeholder boundary — no real baseline mis-delivery rate exists in our dataset; replace with actual ops number before locking.)
- **Minimum Detectable Effect (MDE) the smallest improvement worth shipping, your floor:** Treatment must reach ≥70% of reassignments acknowledged within 2 minutes to be considered a working mechanism (a reliability floor, not a lift percentage, since baseline is 0%).
- **Sample size per arm use the calculator in the builder, baseline + MDE:** ~3,800–4,000 reassignments per arm, driven by the guardrail (assumed 2% baseline mis-delivery rate — not in our dataset, flagged as an assumption), detecting a 1pp increase at α=0.05 / 80% power.
- **Traffic split & test duration 50/50 standard · cover ≥ 2 weekly cycles:** 50/50 split. Minimum 2 weekly cycles, extended if needed to reach ~3,800/arm based on actual reassignment volume.
- **Significance threshold p < 0.05 is standard, explain any deviation:** p < 0.05, two-tailed, standard — no deviation.

## Define your control and variant
- **Control (A) the current experience, reference your M2 moment of misery and M3 funnel/workflow data:** Today's reassignment flow, as documented: a dispatcher reassigns a route inside RouteLogic. The app gives no push notification and no confirmation signal to either party (BUG-2044). The driver may not see the change for 8–15 minutes, and can continue driving toward the old route in the meantime. The dispatcher has no way to know, in-app, whether or when the driver has seen it — which is why they separately message the driver through the WhatsApp group as "the real system" (UXR-02). In Control, this manual WhatsApp step continues exactly as it does today; nothing in the app or the workaround is touched.
- **Variant (B) your single change, copy the relevant screens & functional requirements from your M4 PRD:** The single change is the reassignment notification/acknowledgment mechanism, exactly as scoped in the M4 PRD:

Requirements (copied from PRD):

Reassignment action triggers a push notification within a target of <30 seconds under normal connectivity.
Driver sees an in-app prompt requiring a single tap to acknowledge.
Dispatcher's screen reflects live status: Sent → Delivered → Acknowledged, with timestamps.
If no acknowledgment within 5 minutes, the dispatcher is flagged so they can fall back to manual contact.
Notification and acknowledgment events are logged.

Screens (copied from PRD Prototype section):

Dispatcher view — Reassignment card: Shows the reassigned stop, with a status line that updates live: "Sent · 0:04" → "Delivered · 0:11" → "Acknowledged by [Driver] · 0:34." No manual refresh required.
Driver view — Reassignment alert: A single, high-contrast push notification with the new stop/route summary and one button: "Got it." Tapping it is the entire required action.
Dispatcher view — Fallback flag: If no acknowledgment after 5 minutes, the card visually flags (amber border) with a "Call driver" shortcut.
- **Isolation check, what has NOT changed? list everything identical between arms (app version, recommendation engine, notifications, onboarding). If something changed inadvertently, your test is compromised.:** App version / build — both arms run the same underlying release, differing only by the feature flag controlling this notification mechanism.
Route optimization / algorithmic routing logic (unrelated to reassignment notification).
Offline mode and stop-list caching behavior (BUG-2050) — unchanged in both arms; if a driver is offline, the push still won't land in Variant either, consistent with the PRD's stated dependency risk.
Proof-of-delivery photo upload flow (BUG-2061) — untouched.
Driver-to-dispatcher status lag on the dashboard (BUG-2072) — explicitly out of scope per the PRD; both arms retain the same 20–60 min lag on general status updates, only the reassignment-specific flow differs.
Onboarding, menu structure, and core-action navigation (BUG-2079) — unchanged in both arms.
Driver and dispatcher populations — same assignment logic, same routes, same regions in both arms (no confound from selecting different user segments).
The WhatsApp group itself still exists and is technically available to Control and Variant users alike — only the need to use it is expected to differ, not its availability. This matters because if Variant dispatchers were blocked from WhatsApp, that would be a second variable, not an isolated test of the in-app fix.

## Formalize your hypothesis & shipping criteria
- **Your hypothesis (filled in):** I believe that real-time push notification + single-tap in-app acknowledgment for route reassignments for the Fleet Dispatcher will result in dispatchers no longer needing to manually notify drivers via WhatsApp for reassignments, as measured by reaching ≥70% of reassignments acknowledged in-app within 2 minutes (baseline: 0%) within 2+ weekly cycles, or until ~3,800 reassignments per arm are reached, whichever is longer. We will protect mis-delivered/failed-stop rate throughout the test, requiring it stay within 1 percentage point of its pre-test baseline.

Note on deviation from the template: I didn't fill in "[X%] change in [PRIMARY METRIC]" literally, because our baseline is 0% — there's no existing rate to calculate a percentage change from (a move from 0% to any number is an undefined/infinite percentage change, not a real signal). I substituted an absolute threshold (≥70%) in its place. This is explained further in the debrief below, since it's a real complication, not a shortcut.
- **Your shipping criteria (filled in):** We will SHIP if reassignment acknowledgment rate reaches ≥70% within 2 minutes at p < 0.05, and mis-delivered/failed-stop rate does not exceed a 1 percentage point increase after the test duration.

We will ITERATE if the acknowledgment rate is positive and statistically significant but lands below 70% — e.g., the mechanism works but adoption or reliability (connectivity, notification delivery) is holding it back from the reliability floor needed to retire WhatsApp with confidence.

We will KILL if the acknowledgment rate shows no significant improvement over 0%, or if the guardrail is breached (mis-delivery rate moves beyond the 1pp boundary) — in which case we stop regardless of how the primary metric performs, since a guardrail breach ends the test on its own.

The read date is fixed at the end of the test duration (2+ weekly cycles or ~3,800/arm, whichever is longer). No results reviewed before this date.
- **Hardest parameter to define, and did it change your hypothesis? quick debrief:** The hardest parameter was the MDE, because the standard "lift %" framing assumes a nonzero baseline to lift from — ours is 0%, since the acknowledgment mechanism doesn't exist yet. A literal "% change" is mathematically meaningless at a 0% baseline (any result is technically an infinite increase), so the standard template would have made the shipping criteria unfalsifiable — almost anything would "pass." That forced a real decision, not just a fill-in-the-blank: I had to ask what number would actually justify killing the WhatsApp habit, not just what number would be statistically different from zero. That's why the MDE became an absolute reliability floor (≥70%) instead of a relative lift — it changed the hypothesis from "did this technically work at all" to "does this work reliably enough to earn the dispatcher's trust away from a backup they've relied on for a real safety reason." That's a meaningfully different, and harder, bar than the original template implied.
