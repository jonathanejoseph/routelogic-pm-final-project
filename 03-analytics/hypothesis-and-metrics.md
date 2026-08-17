# Module 3: Hypothesis & Success Metrics

**Product:** RouteLogic Velocity
**Builds on:** M2 — Persona (Fleet Dispatcher), Workaround (WhatsApp shadow dispatch), Future-State Journey Map

---

## Hypothesis

> **If** we replace the dispatch reassignment flow with instant push notification and live driver acknowledgment (collapsing today's 8–15 minute silent gap), **then** dispatchers will stop relying on WhatsApp as the real system for reassignments, **because** the core blocker — not knowing whether the driver has received and seen the change — will be resolved inside RouteLogic itself.

**Grounding evidence:**
- Friction: *"I reassign a route and the driver doesn't see it for ten, fifteen minutes... We keep a WhatsApp group as the real system."* (UXR-02)
- Root cause: reassignments take 8–15 min to propagate with no push notification (BUG-2044, Sev: Critical)
- Behavioral proxy for "workaround abandonment": we cannot directly measure WhatsApp usage (it's outside our product), so we test the underlying cause — propagation and confirmation speed — as the leading indicator that the workaround's reason to exist is going away.

---

## Success Metrics

### Primary Success Metric
**Reassignment-to-acknowledgment time** — the elapsed time from a dispatcher confirming a reassignment in RouteLogic to the driver acknowledging receipt in-app.

- **Current baseline:** 8–15 minutes, with no confirmation signal at all today (BUG-2044).
- **Target:** Under 30 seconds for push delivery; under 2 minutes for driver acknowledgment under normal connectivity.
- **Why this metric:** It's the most direct, instrumentable proxy for the exact moment of misery in UXR-02 — the gap between "I sent it" and "they got it" is precisely what pushes the dispatcher to WhatsApp.

### Guardrail Metric
**Mis-delivered / failed-stop rate**, tracked before and after rollout.

- **Why this guardrail:** Speeding up reassignment delivery must not come at the cost of accuracy — e.g., drivers acting on notifications too hastily, or duplicate/conflicting reassignments landing in quick succession. If this metric moves in the wrong direction, the fix is net-negative even if propagation time improves.

### Secondary / Diagnostic Metrics (supporting, not primary)
- **Push notification delivery success rate** (technical health of the fix itself — distinct from user-facing acknowledgment time)
- **% of reassignments with no acknowledgment within 5 minutes** (surfaces edge cases — poor connectivity, device issues — that the primary metric alone could mask)
- **Qualitative signal:** follow-up UXR check-in with dispatchers (e.g., re-interview UXR-02's persona type) on whether WhatsApp is still used as a backup — since workaround abandonment itself isn't directly instrumentable in-app.

---

## What This Hypothesis Does Not Claim

- It does not claim WhatsApp usage will disappear entirely — habits and trust take longer to shift than propagation time. The metric tests the *mechanism* (speed + confirmation), not the *behavior change* directly, which is why the qualitative check-in is included as a secondary signal.
- It does not address the second half of the M2 sync problem — driver-to-dispatcher status lag (BUG-2072, UXR-09). That is a distinct hypothesis and out of scope here; flagged for a future test, not folded in to avoid diluting this one.

---

*Source data: RouteLogic Velocity UXR-02, BUG-2044; hypothesis builds directly on M2 persona and journey map.*
