# Architecture

This document describes the target architecture for the FortiGate 500E → 700G perimeter migration.
It focuses on the HA design, network connectivity, and operational requirements needed for a controlled cutover.

---

## 1. Target State Summary

### Topology
- **Perimeter firewalls** migrated to **FortiGate 700G**
- **High Availability:** **Active/Passive** cluster
- **Heartbeat:** dedicated links between members
- **Objective:** predictable failover and fast rollback capability

### Design Principles
- **Stability over complexity**
- **Like-for-like intent** during migration (no redesign inside the cutover window)
- **Auditability:** decisions, risks, and validation steps documented

---

## 2. Logical Architecture

### Components
- **HA Cluster**
  - Member A (Primary)
  - Member B (Secondary)
- **Upstream / Internet**
  - WAN provider or upstream router(s)
- **Downstream / LAN**
  - Core / distribution / DC edge
- **Management Plane**
  - OOB or dedicated MGMT network (recommended)
  - NTP/DNS/AAA/Logging reachability

### Logical Planes (recommended separation)
- **Management plane:** administration, monitoring, logging
- **Control plane:** routing adjacencies (static/BGP as applicable)
- **Data plane:** production traffic flows (policies + NAT)

---

## 3. HA Design (Active/Passive)

### HA Objectives
- Single active dataplane at any time (simplifies operational troubleshooting)
- Predictable failover behavior
- Full config synchronization between members

### HA Key Requirements
- Dedicated heartbeat interfaces
- Reliable physical connectivity between members
- Consistent interface mapping between units (port roles and VLANs aligned)

### HA Operational Notes
- HA must reach a stable state before cutover is declared successful:
  - expected primary/secondary roles
  - synchronization completed
  - no split-brain indicators

---

## 4. Interfaces and Connectivity (High Level)

> All values below are conceptual and must be implemented using sanitized naming (no production IPs).

### Interface Roles
- **WAN:** uplink to provider / upstream routing domain
- **LAN/Inside:** downstream to core/distribution
- **HA:** heartbeat links (and optionally session sync depending on model/design)
- **MGMT:** administrative access and telemetry

### VLAN/Trunk Considerations
- VLANs and trunking must be aligned with the previous design to keep intent like-for-like.
- Any required switch port changes must be completed during pre-change activities and validated.

---

## 5. Routing and NAT (Architectural Constraints)

### Routing
- Maintain routing intent (static/BGP) to minimize variables during cutover.
- Verify adjacency formation (or static reachability) as a post-cutover gate.

### NAT / VIP / IP Pools
- Preserve NAT intent and ordering behavior.
- Define a minimum set of **critical flow tests** that validate:
  - inbound services via VIPs
  - outbound NAT for key applications
  - any site-to-site dependencies (if applicable)

---

## 6. Observability (Security + Operations)

### Mandatory Visibility Targets
- Firewall logging enabled and reachable (SOC/SIEM/FortiAnalyzer as applicable)
- Monitoring checks for:
  - HA state
  - interface/link status
  - CPU/memory baseline after go-live
  - critical policy hit counters (spot validation)

### Security Posture Guardrails
- No temporary “allow any” policies in production.
- If emergency troubleshooting requires temporary rules:
  - must be time-boxed
  - documented
  - removed before closure

---

## 7. Diagram Template (Sanitized)

You can capture a high-level diagram using placeholders:

- INTERNET / ISP
- WAN Router / Edge
- FortiGate HA Cluster (Active/Passive)
- CORE Switch / DC Edge
- MGMT Network (NTP/DNS/AAA/SIEM)

Example layout (conceptual):

INTERNET/ISP
   |
[UPSTREAM]
   |
(WAN)  FortiGate 700G HA  (LAN) ---- [CORE/DISTRIBUTION] ---- [DATA CENTER/LAN]
         |       |
        (HA)   (HA)
         |       |
      Heartbeat Links
   |
(MGMT) ---- [MGMT/OOB Network] ---- NTP/DNS/AAA/SIEM

Store the exported diagram image under:
`/diagrams/ha-topology.png`

---

## 8. Sanitization Rules (Do Not Break)

When documenting:
- Replace real IPs with `x.x.x.x` or `10.0.0.0/24`
- Replace site names with `SITE-A`, `SITE-B`
- Replace interface labels with `WAN1`, `LAN1`, `HA1`, `MGMT`
- Do not include:
  - credentials
  - certificates
  - full production configs

---

## 9. Acceptance Criteria (Architecture)

Architecture is considered implemented when:
- HA cluster stable and synchronized
- Interfaces operational and mapped correctly
- Routing intent validated (static/BGP)
- NAT/VIP critical flows verified
- Monitoring/logging restored and verified
