# FortiGate Perimeter Migration — 500E to 700G (HA)

## Overview
Enterprise perimeter firewall migration driven by hardware end-of-life (EOL) and security platform standardization.
The project focuses on risk-controlled cutover, high availability validation, and rollback assurance.

## Role
Cyber Security Lead / Network Security Architect

Responsibilities:
- Architecture definition
- Risk analysis
- Migration strategy
- Technical validation

## Environment
- Vendor: Fortinet
- Platform: FortiGate
- Migration: 500E → 700G
- FortiOS: 7.2.x
- Topology: Active / Passive HA
- Scope: Perimeter firewalls

## Objectives
- Ensure business continuity during migration
- Achieve rollback capability under 15 minutes
- Maintain security posture and policy integrity
- Align with regional firewall standards

## Architecture
The target design implements an Active/Passive HA cluster with:
- Dedicated heartbeat interfaces
- Full configuration synchronization
- Centralized management readiness

See `docs/architecture.md` for details.

## Key Technical Decisions
- Controlled cutover instead of parallel deployment
- HA mode selection based on operational simplicity
- FortiOS version alignment to avoid object incompatibilities

See `docs/decisions.md`.

## Risks & Mitigations
| Risk | Impact | Mitigation |
|------|--------|------------|
| Object incompatibility | Traffic loss | Pre-migration validation |
| Cutover downtime | Business disruption | Tested rollback plan |
| HA sync failure | Network instability | Dedicated heartbeat |

See `docs/risks.md`.

## Migration Execution
High-level flow:
1. Configuration backup and validation
2. FortiOS preparation
3. Configuration import and HA build
4. Controlled cutover
5. Post-change validation

See `docs/migration-plan.md`.

## Outcome
- Downtime: ~7 minutes
- No post-migration incidents
- HA cluster stable
- Security policies preserved

## Lessons Learned
- Pre-validation automation significantly reduces risk
- Version alignment is critical before configuration import
- Migration planning is more important than raw execution speed

See `docs/lessons-learned.md`.

## Security Notice
All examples and documentation are sanitized.
No production IPs, credentials, or sensitive data are included.
