# Decisions

This document records the key architecture and migration decisions for the FortiGate 500E → 700G perimeter migration.
It is written to justify trade-offs, reduce ambiguity during execution, and support auditability.

## Decision Log (Summary)

| ID | Decision | Selected Option | Main Driver |
|----|----------|------------------|-------------|
| D-01 | Migration modality | Controlled cutover (replacement) | Rollback speed + operational control |
| D-02 | Deployment approach | No parallel racking assumed | Physical rack uncertainty |
| D-03 | HA topology | Active/Passive cluster | Simplicity + stability + recovery |
| D-04 | FortiOS strategy | Align target FortiOS before import | Compatibility + reduced risk |
| D-05 | Change scope | Like-for-like migration | Avoid functional changes during cutover |
| D-06 | Validation strategy | Pre-check + post-check gates | Predictability and audit evidence |
| D-07 | Rollback design | Fast rollback as a requirement | Business continuity |
| D-08 | Risk posture | “Stop-the-line” criteria defined | Prevent uncontrolled impact |

---

## D-01 — Migration modality: Controlled cutover (replacement)

### Options considered
- **Option A:** Parallel deployment and gradual migration
- **Option B:** Replacement via **controlled cutover** (selected)

### Selected
**Option B — Controlled cutover**

### Rationale
- Minimizes overall migration complexity.
- Supports a clear “point of change” with strong control and communication.
- Enables a rollback-first mindset: if validation gates fail, revert quickly.

### Trade-offs / Impact
- Short, planned downtime window is required.
- Strong preparation is mandatory (pre-checks and rollback rehearsal).

---

## D-02 — Deployment approach: No parallel racking assumed (rack uncertainty)

### Context
Physical rack availability may be unknown until site validation.
The strategy must succeed even if parallel racking is not possible.

### Selected
Assume **replacement in-place** as the baseline plan.
Parallel racking becomes an optimization only if space/power is confirmed.

### Why it matters
This avoids building a plan that depends on conditions not guaranteed at execution time.

---

## D-03 — HA topology: Active/Passive HA

### Options considered
- Active/Passive HA (selected)
- Active/Active (not selected)

### Selected
**Active/Passive**

### Rationale
- Operational simplicity for perimeter environments.
- Predictable failover behavior and easier troubleshooting.
- Lower risk during initial stabilization post-migration.

### Trade-offs
- Active/Active could provide higher throughput in some designs, but introduces additional operational complexity.
- For this migration, stability and predictability are prioritized over maximizing utilization.

---

## D-04 — FortiOS strategy: Version alignment before configuration import

### Selected
Align **target FortiOS version** before importing configuration artifacts.

### Rationale
- Reduces risk of object/schema incompatibilities.
- Avoids last-minute behavior changes caused by import/upgrade order.
- Supports consistent validation outcomes.

### Guardrails
- Target version must be validated against required features and known compatibility constraints.
- Any deviation from the target version requires re-running pre-check validation gates.

---

## D-05 — Change scope: Like-for-like migration (no redesign during cutover)

### Selected
Migrate configuration **like-for-like** (equivalent intent), avoiding redesign during the change window.

### Rationale
- Keeps variables under control.
- Simplifies troubleshooting: issues are easier to attribute to migration rather than new design decisions.
- Enables measurable validation against the “known good” baseline.

### What can change (allowed)
- Mandatory hardware-specific adjustments (interfaces, ports, HA links).
- Version-driven syntax changes required for compatibility.

### What must NOT change (during cutover)
- Security policy intent (rules, segmentation logic)
- NAT behavior intent
- Routing design intent (unless explicitly approved as a separate change)

---

## D-06 — Validation strategy: Pre-check and Post-check gates

### Selected
A gated approach:
- **Pre-check:** verify readiness before the change
- **Post-check:** verify outcomes before declaring success

### Rationale
- Prevents “hope-based” migrations.
- Produces audit evidence (what was checked, when, and results).

### Minimum validation areas
- HA health and sync
- Interface/link status
- Routing (static/BGP as applicable)
- NAT/VIPs/IP Pools behavior (spot checks)
- Critical traffic flows (business-defined)
- Logging/monitoring visibility

---

## D-07 — Rollback design: Rollback under 15 minutes (requirement)

### Selected
Rollback time objective is a **hard requirement**, not a “nice-to-have”.

### Rationale
- Business continuity and risk posture.
- Enables decisive go/no-go decisions during the window.

### Rollback principles
- Preserve original unit configuration and connectivity readiness.
- Rollback path must be tested (at least logically) before the change.

---

## D-08 — “Stop-the-line” criteria (Go/No-Go)

### Selected
Define clear stop criteria that trigger rollback or pause.

### Examples of stop criteria
- HA does not reach expected stable state (sync not complete / split brain risk)
- Critical routing adjacency does not establish in the defined time
- Defined critical traffic tests fail
- Unexpected NAT behavior impacting production flows

### Rationale
These criteria prevent “push-through” behavior under time pressure.

---

## Notes
This decision log is intentionally written to be:
- clear for non-authors to execute,
- defensible during audit/review,
- reusable as a baseline for future site migrations.
