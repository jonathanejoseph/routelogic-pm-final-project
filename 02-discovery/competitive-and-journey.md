# Module 2: Competitive Analysis & Journey Map

**Product:** RouteLogic Velocity
**Strategy:** Reclaim operational simplicity by stripping away legacy noise so the tool is the fastest for everyday work.

---

## Part A — Competitive Analysis

### Positioning signal from research

Our UXR data does not include a formal competitive teardown, so this section is scoped to what the research actually evidences — one direct signal, stated plainly by a buyer-side stakeholder:

> *"It does everything, which is the problem. My frontline people use maybe 5% of it and can't find that 5%. We're evaluating a leaner competitor that just does routing well."*
> — UXR-04, Ops Manager, Enterprise Account

**What this tells us:**
- The competitive threat is not named, but the *shape* of the threat is: a narrower, faster, single-purpose tool is being evaluated specifically because RouteLogic's breadth has become a liability at the frontline.
- This is corroborated by UXR-10 (Regional Manager): admin reporting is valued, but "the daily driver experience is dragging down adoption and now renewal is at risk" — meaning the competitive vulnerability lives specifically in the frontline layer, not the admin layer.
- Our own data (UXR-11, UXR-12, BUG-2079) shows *why* a leaner competitor would win this comparison: core actions are buried under accumulated features with no removal process.

**Positioning implication (not a named comparison, since we have no competitor data to cite):**
RouteLogic is not losing on capability — it's at risk of losing on *speed to the core task*. Our differentiated position, per the strategy above, is not "add more" but "prove the everyday actions are faster than any leaner alternative could make them," while retaining the admin/reporting depth that won the account in the first place.

**Gap to close before this section is complete:** actual named-competitor research (pricing, feature parity, driver-app reviews) is not in our dataset and should be gathered separately — this section should not be treated as a finished competitive teardown, only a positioning hypothesis grounded in one internal data point.

---

## Part B — Persona & Workaround

### Persona: Fleet Dispatcher (reassignment scenario)

- **Role:** A dispatcher at a mid-size 3PL who reroutes drivers in real time as conditions change on the road.
- **Goal:** Get a reassigned route in front of the right driver fast enough that they act on it before continuing down the wrong path.
- **Friction:** *"I reassign a route and the driver doesn't see it for ten, fifteen minutes. By then they've driven the wrong way. We keep a WhatsApp group as the real system."* (UXR-02, corroborated by BUG-2044 — no push notification on route change)

### The Workaround: Shadow Dispatch via WhatsApp

**External tool:** WhatsApp group chat, used as a parallel, unofficial coordination layer running alongside RouteLogic.

**Manual process:**
1. Reassigns the route inside RouteLogic — the official, system-of-record action.
2. Opens WhatsApp separately and messages the driver directly with the new instruction.
3. Waits for a reply in WhatsApp to confirm the driver has seen and acted on it.
4. Mentally tracks two conflicting states — what RouteLogic *shows* vs. what WhatsApp *confirms*.
5. Repeats this dual-channel process on every reassignment.

**Core frustration (the exact moment it breaks):** The moment the dispatcher realizes the in-app reassignment hasn't reached the driver — and won't for 8–15 minutes — while the driver is already committing to the wrong route. The failure is silent, not loud, which is what makes it dangerous: there's no error to react to, only a delay to guess at.

**Evidence:**
> *"I reassign a route and the driver doesn't see it for ten, fifteen minutes. By then they've driven the wrong way. We keep a WhatsApp group as the real system."* — UXR-02

Corroborated by **BUG-2044** (Sev: Critical): dispatch reassignments take 8–15 min to propagate, no push notification on route change.

**Why this matters to the business:** This workaround is the operational proof behind the churn risk named in UXR-04 and UXR-10. Every time WhatsApp functions as "the real system," RouteLogic is a tool the company pays for but doesn't actually run its critical path on — the exact gap a competitor's sales pitch would target.

---

## Part C — Future-State Journey Map

**Value Prop:** For frontline coordinators making time-critical routing calls, we will collapse the everyday, high-stakes actions into their fastest possible path, because the next renewal won't be decided by the buyer who saw the feature list — it'll be decided by the coordinator who already gave up on the tool.

### 1. Reassign
- **User Action:** Dispatcher taps reassign once → route change fires immediately, no double-entry needed.
- **Internal State:** Confident — trusts the single action is sufficient, not just the first step.
- **Pain Point Addressed:** Removes need to also open WhatsApp "just in case."

### 2. Instant push
- **User Action:** System pushes route to driver device instantly → dispatcher sees live delivery confirmation.
- **Internal State:** Reassured — sees proof the driver was actually reached, not hoping.
- **Pain Point Addressed:** Kills the 8–15 min propagation gap (BUG-2044).

### 3. Live acknowledgment
- **User Action:** Driver taps acknowledge → dashboard updates in real time, closing the loop.
- **Internal State:** Trusting — the board now reflects ground truth, not a guess.
- **Pain Point Addressed:** Ends the "I can't trust the board" gap driving manual confirmation.

### 4. Resolved
- **User Action:** Dispatcher closes the task inside RouteLogic → never opens a second app.
- **Internal State:** In control — one tool, one truth, full attention back on the road.
- **Pain Point Addressed:** Retires WhatsApp as "the real system" (UXR-02).

### Competitive Advantages Over the Manual Workaround
1. **Speed** — reroute-to-confirmation happens in seconds inside one tool, not minutes across two.
2. **Auditability** — every reassignment is logged and searchable, not buried in an unlogged chat thread.
3. **Trust** — the dispatcher board reflects real-time ground truth, eliminating manual double-checking entirely.

---

*Source data: RouteLogic Velocity UXR notes (UXR-01–12) and bug reports (BUG-2031–2090), Scenario B.*
