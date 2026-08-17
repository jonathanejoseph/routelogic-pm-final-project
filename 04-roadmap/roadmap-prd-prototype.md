# Module 4: Roadmap, PRD & Prototype

**Product:** RouteLogic Velocity
**Builds on:** M2 (persona, workaround, journey map) and M3 (hypothesis, success metrics)

---

## Roadmap Placement

**Now (this quarter):** Real-Time Reassignment Push & Acknowledgment
This is the top roadmap priority because it directly targets the single highest-severity, best-evidenced friction point in the dataset (BUG-2044, Sev: Critical; UXR-02) and is the subject of the M3 hypothesis test.

**Next (following quarter, not scoped in this PRD):** Driver-to-dispatcher status sync fix (BUG-2072/UXR-09) — the mirror-image problem, deliberately sequenced after this one so the team isn't testing two sync hypotheses simultaneously.

**Later (not yet prioritized):** Core-action UI simplification (BUG-2079, UXR-11) — high strategic relevance to "operational simplicity" but a larger, more structural change; sequenced after we've proven the smaller sync fix moves behavior.

---

## PRD: Real-Time Reassignment Push & Acknowledgment

### Problem Statement
When a dispatcher reassigns a route, the driver does not see the change for 8–15 minutes and receives no push notification (BUG-2044). Drivers continue operating on stale routes during this window. Dispatchers have adapted by manually notifying drivers through an external WhatsApp group, which they describe as "the real system" (UXR-02) — meaning the product does not currently function as the source of truth for its own core coordination task.

### Goal
Close the gap between "dispatcher reassigns" and "driver has seen and can act on the change" to the point where a manual backup channel is no longer necessary for this specific action.

### In Scope
- Push notification to the driver's device the moment a reassignment is confirmed by the dispatcher.
- In-app acknowledgment action for the driver (single tap) confirming receipt.
- Real-time status update on the dispatcher's view reflecting acknowledgment (sent → delivered → acknowledged).
- Retry/fallback logic if push delivery fails (e.g., poor connectivity) — surfaced to the dispatcher, not silently dropped.

### Explicitly Out of Scope (for this PRD)
- Driver-to-dispatcher status lag on the dashboard (BUG-2072) — separate problem, separate hypothesis, sequenced in "Next."
- Any redesign of the core-action navigation/menu structure (BUG-2079, UXR-11) — related to the broader "operational simplicity" strategy but not required to fix this specific propagation failure.
- Offline caching of stop lists (BUG-2050) — a distinct reliability issue; a reassignment can't be pushed to a device with no connectivity regardless of this fix, so offline is a dependency risk (see below), not something this PRD solves.

### User Story
*As a fleet dispatcher, when I reassign a route, I want the driver to be notified instantly and to see confirmation that they've received it, so that I don't have to manually message them through a separate app to know the change landed.*

### Requirements
1. Reassignment action triggers a push notification within a target of <30 seconds under normal connectivity.
2. Driver sees an in-app prompt requiring a single tap to acknowledge.
3. Dispatcher's screen reflects live status: Sent → Delivered → Acknowledged, with timestamps.
4. If no acknowledgment within a defined threshold (e.g., 5 minutes), the dispatcher is flagged so they can fall back to manual contact — this is a safety net, not a redesign of the acknowledgment flow itself.
5. Notification and acknowledgment events are logged, creating an auditable record where today none exists (directly addressing the "unlogged WhatsApp thread" problem named in M2).

### Dependency / Risk Called Out
This feature assumes reasonable connectivity. Given BUG-2050 (offline mode fails to cache stop lists, blocking rural routes), a reassignment push could still fail silently for the rural-driver segment (UXR-06). This PRD does not fix that — it is flagged as a known limitation, not solved by omission.

### Success Criteria
Tied directly to M3: reassignment-to-acknowledgment time under 30 seconds (push) / 2 minutes (acknowledgment), with no regression in mis-delivered/failed-stop rate (guardrail).

---

## Prototype (Description)

*(Textual description in lieu of a visual mockup — a low-fidelity wireframe would show three states.)*

**Dispatcher view — Reassignment card:**
Shows the reassigned stop, and a status line that updates live: "Sent · 0:04" → "Delivered · 0:11" → "Acknowledged by [Driver] · 0:34." No manual refresh required.

**Driver view — Reassignment alert:**
A single, high-contrast push notification with the new stop/route summary and one button: "Got it." Tapping it is the entire required action — consistent with the M2 journey map's principle of collapsing high-stakes actions into their fastest possible path.

**Dispatcher view — Fallback flag:**
If no acknowledgment after 5 minutes, the card visually flags (e.g., amber border) with a "Call driver" shortcut — acknowledging that a manual fallback may still sometimes be needed, without requiring it by default.

---

*This PRD scopes the feature that will be tested against the M3 hypothesis and metrics. No roadmap items beyond this "Now" priority are being committed to in this document.*
