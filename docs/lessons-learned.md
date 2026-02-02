# Lessons Learned

This document captures key lessons identified during the FortiGate 500E → 700G perimeter migration.
The intent is to improve predictability, reduce risk, and standardize future migrations.

---

## 1. Preparation Beats Speed
Time invested in pre-checks and baselining reduced execution risk more than accelerating the cutover itself.
Future migrations should protect preparation time as a non-negotiable activity.

## 2. Version Alignment Is Critical
Aligning FortiOS versions **before** importing configurations significantly reduced object and behavior inconsistencies.
Version drift must be treated as a primary risk factor, not a minor detail.

## 3. Rollback Must Be a Requirement, Not a Fallback
Defining rollback time (<15 minutes) as a hard requirement enabled clear Go/No-Go decisions.
This prevented “push-through” behavior under time pressure.

## 4. Like-for-Like Reduces Variables
Avoiding functional redesign during the cutover window simplified validation and troubleshooting.
Design improvements should be handled in separate, explicitly approved changes.

## 5. HA Stability Is a Gate, Not a Checkmark
HA must reach a fully stable and synchronized state before declaring success.
Partial or “eventual” HA convergence introduces unacceptable operational risk.

## 6. Business-Defined Tests Matter More Than Technical Pings
Critical traffic validation defined by business owners provided stronger confidence than generic connectivity checks.
Future plans should always include business-approved test cases.

## 7. Observability Must Be Restored Early
Logging and monitoring validation was essential to confirm both security posture and operational health.
Visibility gaps should trigger a pause or rollback, not be deferred.

## 8. Clear Stop Criteria Prevent Bad Decisions
Explicit Go/No-Go and stop-the-line criteria reduced ambiguity during execution.
This structure supported calm, defensible decisions under time constraints.

## 9. Documentation Accelerates Teams
Well-structured documentation allowed other teams to understand the plan without ad-hoc explanations.
This reduced execution friction and dependency on specific individuals.

## 10. Automation Opportunities Identified
Pre-checks, configuration validation, and post-change evidence collection are strong candidates for automation.
Future iterations should integrate scripts to reduce manual effort and human error.

---

## Improvement Actions (Forward Looking)

- Standardize a pre-check automation toolkit for FortiGate migrations
- Create reusable validation checklists per environment type
- Maintain a shared migration baseline repository for regional deployments
