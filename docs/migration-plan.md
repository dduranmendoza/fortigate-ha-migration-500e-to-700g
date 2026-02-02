# Migration Plan (Runbook)

This runbook describes the controlled cutover migration from FortiGate 500E to FortiGate 700G in an Active/Passive HA design.
It is structured to be executable, auditable, and rollback-driven.

---

## 0. Scope and Rules

### In Scope
- Like-for-like migration (same security and routing intent)
- HA build and validation
- Controlled cutover within an approved change window
- Post-change verification and stabilization

### Out of Scope
- Security policy redesign
- Network segmentation redesign
- Major routing redesign (unless approved as a separate change)

### Non-Negotiables
- No production credentials or sensitive data in documentation
- No “temporary allow-any” rules without time-box + documentation + removal
- Rollback must remain feasible throughout the window

---

## 1. Preconditions (Before the Window)

### 1.1 Change Readiness Checklist
- [ ] Approved change ticket and communication sent
- [ ] Stakeholders aligned (operations / apps / SOC)
- [ ] Defined critical services and test cases (business-approved)
- [ ] Confirm rack/power status (parallel racking optional, not required)
- [ ] Confirm console/remote access method for both old and new units
- [ ] Confirm management reachability (NTP/DNS/AAA/logging endpoints)

### 1.2 Backups and Baselines
- [ ] Backup current configuration from source firewall
- [ ] Export current HA status (if applicable) and key system info
- [ ] Capture baseline evidence (screenshots or sanitized outputs):
  - [ ] Interface status
  - [ ] Routing table / BGP neighbors (if applicable)
  - [ ] NAT/VIP inventory (high level)
  - [ ] Critical policy references (high level)
  - [ ] Logging/monitoring status

### 1.3 Compatibility and Version Alignment
- [ ] Target FortiOS version confirmed for 700G platform
- [ ] Upgrade plan validated (if upgrade is required)
- [ ] Import strategy defined (like-for-like)

### 1.4 Pre-Check Gates (Go/No-Go before change)
**If any item fails, do NOT start the cutover**
- [ ] Config backup verified (readable and complete)
- [ ] Rollback plan reviewed and feasible
- [ ] Staff availability confirmed for entire window
- [ ] Monitoring/SOC ready to validate logs/alerts

---

## 2. Pre-Change Implementation (Staging the Target)

> Goal: Prepare the new FortiGate 700G and HA cluster without impacting production traffic.

### 2.1 Physical and L2 Preparation
- [ ] Rack and power up the new units (or stage as permitted)
- [ ] Cable interfaces according to the architecture:
  - [ ] WAN
  - [ ] LAN
  - [ ] HA heartbeat
  - [ ] MGMT (recommended)
- [ ] Validate switch VLANs/trunks match baseline design

### 2.2 System Setup
- [ ] Set management IP (sanitized and documented)
- [ ] Configure NTP, timezone, DNS (as required)
- [ ] Validate licensing and FortiGuard reachability (if applicable)

### 2.3 Build HA Cluster
- [ ] Configure HA settings on both members
- [ ] Validate HA forms correctly (primary/secondary)
- [ ] Confirm synchronization status is stable

### 2.4 Configuration Import / Apply Like-for-Like
- [ ] Import configuration artifacts (sanitized approach)
- [ ] Resolve any import warnings/issues (document decisions)
- [ ] Ensure interface mapping and VLAN intent matches baseline

### 2.5 Pre-Cutover Validation (Target Unit)
- [ ] HA stable and synced
- [ ] Interfaces show expected up/down state (given not in prod yet)
- [ ] Routing configuration present (static/BGP as applicable)
- [ ] NAT/VIPs present (high level check)
- [ ] Logging configuration present (FAZ/SIEM as applicable)

---

## 3. Change Window (Controlled Cutover)

> Goal: Move production traffic from 500E to 700G safely, with fast rollback capability.

### 3.1 Start-of-Window Actions
- [ ] Announce change start to stakeholders
- [ ] Confirm last-minute readiness:
  - [ ] Access to both platforms
  - [ ] Test owners on standby
  - [ ] Rollback path still feasible

### 3.2 Cutover Execution (Generic)
> The exact physical step depends on the environment. Use the approved cutover method.

- [ ] Move WAN link(s) from old to new (or switch upstream to new)
- [ ] Move LAN link(s) from old to new (or switch downstream to new)
- [ ] Confirm link status on new HA active member

### 3.3 Immediate Post-Cutover Gates (First 5–10 minutes)
**If any critical gate fails, trigger rollback**
- [ ] HA stable (no split-brain indicators)
- [ ] Interfaces up as expected
- [ ] Routing established (static reachability or BGP neighbors up)
- [ ] Management access stable
- [ ] Basic traffic validation passes (ping/health checks where applicable)

---

## 4. Post-Change Validation (Business + Security)

### 4.1 Critical Traffic Test Plan (Minimum)
Run business-approved tests (examples):
- [ ] Inbound critical services via VIPs
- [ ] Outbound internet for key segments
- [ ] Site-to-site dependencies (if applicable)
- [ ] Application-specific connectivity tests

### 4.2 Security Validation
- [ ] No unexpected “permit wide” policies introduced
- [ ] Logging visible in SOC/SIEM/FortiAnalyzer (as applicable)
- [ ] Confirm alerts/telemetry functioning

### 4.3 Operational Validation
- [ ] CPU/memory baseline within normal range
- [ ] No interface flaps or HA instability
- [ ] Policy hit counters behaving as expected (spot check)

### 4.4 Evidence Collection (Audit Pack)
Capture evidence (sanitized) for:
- [ ] HA status stable
- [ ] Routing status (neighbors/routes)
- [ ] Interface status
- [ ] Successful critical traffic tests
- [ ] Logging/monitoring confirmation

---

## 5. Go/No-Go Criteria (Stop-the-Line)

Trigger **rollback** if any of the following occurs:
- HA not stable or not synchronized within the agreed time
- Critical routing adjacency does not establish
- One or more business-critical tests fail
- Unexpected NAT/VIP behavior affects production services
- Monitoring/logging cannot be restored in the allowed window

---

## 6. Rollback Plan (Fast Revert)

> Rollback is a first-class requirement. Do not “push through” under time pressure.

### 6.1 Rollback Trigger
Rollback is executed when Go/No-Go criteria indicates unacceptable risk or impact.

### 6.2 Rollback Steps (Generic)
- [ ] Move WAN link(s) back to original firewall (or revert upstream selection)
- [ ] Move LAN link(s) back to original firewall (or revert downstream selection)
- [ ] Confirm original unit is passing traffic
- [ ] Validate the same critical traffic tests on the original platform
- [ ] Announce rollback completion and stabilization

### 6.3 Post-Rollback Actions
- [ ] Preserve logs and evidence from the failed attempt
- [ ] Document root cause hypothesis
- [ ] Schedule follow-up remediation and new change window

---

## 7. Change Closure

### Closure Checklist
- [ ] All business-critical tests passed
- [ ] HA stable for agreed stabilization period
- [ ] Monitoring and logging confirmed
- [ ] No temporary emergency rules remain
- [ ] Evidence package stored and linked to the change ticket
- [ ] Lessons learned captured in `docs/lessons-learned.md`

---

## 8. Notes
This runbook is designed to be reusable across multiple sites.
Adjust only site-specific items (interfaces, VLANs, critical tests) while keeping the control structure intact.
