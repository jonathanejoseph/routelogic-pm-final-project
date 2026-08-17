# A/B Experiment Brief, RouteLogic (B2B)

## Parameters
| Parameter | Decision |
|---|---|
| Feature under test | Real-time push notification sent to the driver's device the instant a dispatcher confirms a route reassignment, plus a single-tap in-app "Got it" acknowledgment, with a live Sent→Delivered→Acknowledged status shown on the dispatcher's screen. |
| Persona | Fleet Dispatcher at a mid-size 3PL who reassigns routes in real time as road conditions change. |
| Expected outcome | Dispatchers stop manually notifying drivers through the WhatsApp group for reassignments, because in-app confirmation now closes the "did they get it" gap that WhatsApp was covering for. |
| Primary success metric | % of reassignments acknowledged in-app within 2 minutes of dispatch. |
| Baseline rate | 0% — no acknowledgment mechanism currently exists (BUG-2044: no push notification, no confirmation signal today). |
| Guardrail metric | Mis-delivered / failed-stop rate. |
| Guardrail boundary | Must not increase by more than 1 percentage point above pre-test baseline. (Placeholder — no real baseline mis-delivery rate exists in our dataset; needs the actual ops number before this is trustworthy.) |
| Second guardrail | Driver notification opt-out/mute rate for reassignment alerts, must not exceed 5%. This catches a different harm than the first guardrail — mis-delivery measures operational accuracy, this measures driver trust/fatigue. If drivers start muting reassignment pushes, the mechanism could look statistically successful on paper while quietly failing in practice, because a muted notification produces no acknowledgment signal and would just look like slow uptake rather than active rejection. |
| Minimum Detectable Effect | Treatment must reach ≥70% of reassignments acknowledged within 2 minutes to be considered a working mechanism (a reliability floor, not a relative lift, since baseline is 0%). |
| Sample size per arm | ~3,800–4,000 reassignments per arm, driven by the guardrail (assumed 2% baseline mis-delivery rate — not in our dataset, flagged as an assumption), detecting a 1pp shift at α=0.05 / 80% power. |
| Traffic split | 50/50 |
| Test duration | Minimum 2 weekly cycles, extended if needed to reach ~3,800 reassignments/arm based on actual volume. |
| Significance threshold | p < 0.05, two-tailed — standard, no deviation. |

## Control vs. Variant
- **Control (A):** A dispatcher reassigns a route inside RouteLogic. The app gives no push notification and no confirmation signal to either party (BUG-2044). The driver may not see the change for 8–15 minutes and can continue driving toward the old route in the meantime. The dispatcher has no in-app way to know whether or when the driver has seen it, which is why they separately message the driver through the WhatsApp group as "the real system" (UXR-02). In Control, this manual WhatsApp workaround continues exactly as it does today.
- **Variant (B):** Requirements (copied from M4 PRD):

Reassignment action triggers a push notification within a target of <30 seconds under normal connectivity.
Driver sees an in-app prompt requiring a single tap to acknowledge.
Dispatcher's screen reflects live status: Sent → Delivered → Acknowledged, with timestamps.
If no acknowledgment within 5 minutes, the dispatcher is flagged so they can fall back to manual contact.
Notification and acknowledgment events are logged.

Screens (copied from M4 PRD Prototype):

Dispatcher view — Reassignment card: Shows the reassigned stop, with a status line that updates live: "Sent · 0:04" → "Delivered · 0:11" → "Acknowledged by [Driver] · 0:34." No manual refresh required.
Driver view — Reassignment alert: A single, high-contrast push notification with the new stop/route summary and one button: "Got it." Tapping it is the entire required action.
Dispatcher view — Fallback flag: If no acknowledgment after 5 minutes, the card visually flags (amber border) with a "Call driver" shortcut.
- **Held constant (isolation check):** App version/build — both arms run the same release, differing only by the feature flag controlling this notification mechanism.
Route optimization / algorithmic routing logic — unrelated to reassignment notification, untouched.
Offline mode and stop-list caching behavior (BUG-2050) — unchanged in both arms; a driver offline still won't receive the push in Variant, consistent with the PRD's stated dependency risk.
Proof-of-delivery photo upload flow (BUG-2061) — untouched.
Driver-to-dispatcher dashboard status lag (BUG-2072) — explicitly out of scope; both arms retain the same 20–60 min lag on general status, only the reassignment-specific flow differs.
Onboarding, menu structure, and core-action navigation (BUG-2079) — unchanged in both arms.
Driver and dispatcher populations, route/region assignment logic — identical in both arms, no segment-selection confound.
Availability of the WhatsApp group itself — unchanged and still accessible to both Control and Variant users; only the need to use it is expected to differ, not its availability. If Variant users were blocked from WhatsApp, that would introduce a second variable and compromise attribution.

## Hypothesis
> I believe that Real-time push notification sent to the driver's device the instant a dispatcher confirms a route reassignment, plus a single-tap in-app "Got it" acknowledgment, with a live Sent→Delivered→Acknowledged status shown on the dispatcher's screen. for Fleet Dispatcher at a mid-size 3PL who reassigns routes in real time as road conditions change. will result in Dispatchers stop manually notifying drivers through the WhatsApp group for reassignments, because in-app confirmation now closes the "did they get it" gap that WhatsApp was covering for., as measured by a Treatment must reach ≥70% of reassignments acknowledged within 2 minutes to be considered a working mechanism (a reliability floor, not a relative lift, since baseline is 0%). change in % of reassignments acknowledged in-app within 2 minutes of dispatch. within Minimum 2 weekly cycles, extended if needed to reach ~3,800 reassignments/arm based on actual volume.. We will protect Mis-delivered / failed-stop rate. throughout the test.

## Shipping criteria
> We will **ship** if % of reassignments acknowledged in-app within 2 minutes of dispatch. improves by ≥ Treatment must reach ≥70% of reassignments acknowledged within 2 minutes to be considered a working mechanism (a reliability floor, not a relative lift, since baseline is 0%). at p < 0.05, two-tailed — standard, no deviation. and Mis-delivered / failed-stop rate. does not reach Must not increase by more than 1 percentage point above pre-test baseline. (Placeholder — no real baseline mis-delivery rate exists in our dataset; needs the actual ops number before this is trustworthy.) after Minimum 2 weekly cycles, extended if needed to reach ~3,800 reassignments/arm based on actual volume..
> We will **iterate** if direction is positive but lift is below the MDE.
> We will **kill** if the primary metric shows no improvement or moves negatively.
> The read date is fixed at the end of Minimum 2 weekly cycles, extended if needed to reach ~3,800 reassignments/arm based on actual volume., no results reviewed before this date.
