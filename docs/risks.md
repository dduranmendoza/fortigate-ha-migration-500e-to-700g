# Risk Assessment

This document identifies, evaluates, and documents the primary risks associated with the FortiGate 500E → 700G perimeter firewall migration.
It is intended to support change approval, execution control, and post-change review.

---

## Risk Rating Criteria

| Level | Description |
|------|------------|
| Low | Limited impact, easily recoverable |
| Medium | Service degradation or limited scope impact |
| High | Business-impacting service disruption |
| Critical | Severe business disruption or security exposure |

---

## Risk Register

| ID | Risk Description | Impact | Likelihood | Risk Level | Control / Mitigation | Owner | Status |
|----|------------------|--------|------------|------------|----------------------|-------|--------|
| R-01 | Configuration object incompatibility during import | Traffic disruption | Medium | High | Pre-migration config review and validation | Security Lead | Mitigated |
| R-02 | FortiOS version mismatch between source and target | Unpredictable behavior | Medium | High | Align FortiOS version before import | Security Lead | Mitigated |
| R-03 | HA synchronization failure | Network instability | Low | High | Dedicated heartbeat and HA validation | Network Team | Mitigated |
| R-04 | Extended downtime during cutover | Business disruption | Low | High | Time-boxed cutover and rollback readiness | Change Manager | Mitigated |
| R-05 | Rollback failure | Prolonged outage | Low | Critical | Tested rollback path and preserved baseline | Security Lead | Mitigated |
| R-06 | Routing adjacency not established | Loss of connectivity | Low | High | Pre/post routing validation | Network Team | Mitigated |
| R-07 | NAT / VIP behavior deviation | Application outage | Medium | High | Critical flow testing | Security Lead | Mitigated |
| R-08 | Monitoring / logging not restored | Reduced visibility | Low | Medium | Post-change monitoring verification | SOC | Mitigated |
| R-09 | Human error during execution | Multiple service impact | Medium | High | Runbook + peer verification | Security Lead | Mitigated |
| R-10 | Change window overrun | Operational escalation | Low | Medium | Clear go/no-go criteria | Change Manager | Mitigated |

---

## High and Critical Risk Focus

### R-01 — Configuration object incompatibility
**Risk:** Objects or policies may not import cleanly due to version or platform differences.  
**Impact:** Partial or total traffic loss.  
**Mitigation:**  
- Pre-migration review of objects and policies  
- Version alignment prior to import  
- Validation before cutover  

---

### R-05 — Rollback failure
**Risk:** Inability to restore original service if the migration fails.  
**Impact:** Extended outage beyond approved window.  
**Mitigation:**  
- Preserve original configuration and physical readiness  
- Define rollback as a mandatory success criterion  
- Validate rollback logic prior to execution  

---

## Residual Risk Statement

After applying the defined controls, residual risk is considered **acceptable** for execution within an approved change window.

---

## Audit Notes

- Risk assessment completed prior to change execution.
- Controls mapped directly to execution steps.
- Risk register retained for post-change review and audit traceability.
