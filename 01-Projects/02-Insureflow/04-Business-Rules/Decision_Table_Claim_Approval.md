# Decision Table — Claim Eligibility and Approval Routing
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

> **Disclaimer:** All rules are hypothetical and for portfolio demonstration purposes only.

---

## Table 1: Claim Eligibility Decision Table

This table determines whether a claim proceeds, is auto-rejected, or is held following FNOL and policy validation.

### Conditions

| Condition | Values |
|---|---|
| C1: Policy status on incident date | Active (A) / Expired (E) / Cancelled (C) / Suspended (S) |
| C2: Incident date within 30-day window | Yes (Y) / No (N) |
| C3: Incident type covered under policy | Yes (Y) / No (N) |
| C4: Duplicate claim detected | Yes (Y) / No (N) |
| C5: Valid driving licence confirmed | Yes (Y) / No (N) / Not applicable (NA) |

### Actions

| Action | Description |
|---|---|
| A1: Advance to Document Submission | Claim eligible; proceed |
| A2: Auto-Reject — Expired/Cancelled Policy | Reject with reason code EXP-01 |
| A3: Reject — Incident Outside Reporting Window | Reject with reason code WIN-01 |
| A4: Reject — Coverage Exclusion | Reject with reason code COV-01 |
| A5: Hold — Duplicate Claim | Set status Duplicate Hold; alert Manager |
| A6: Reject — No Valid Licence | Reject with reason code LIC-01 |

### Decision Table

| Rule | C1 | C2 | C3 | C4 | C5 | Action |
|---|---|---|---|---|---|---|
| R01 | A | Y | Y | N | Y / NA | **A1 — Advance** |
| R02 | E | - | - | - | - | **A2 — Auto-Reject (Expired)** |
| R03 | C | - | - | - | - | **A2 — Auto-Reject (Cancelled)** |
| R04 | S | - | - | - | - | **A2 — Auto-Reject (Suspended)** |
| R05 | A | N | - | - | - | **A3 — Reject (Window)** |
| R06 | A | Y | N | - | - | **A4 — Reject (Coverage)** |
| R07 | A | Y | Y | Y | - | **A5 — Hold (Duplicate)** |
| R08 | A | Y | Y | N | N | **A6 — Reject (No Licence)** |

*Notes:*
- `-` = condition is irrelevant (don't care).
- C5 = "Not Applicable" for Theft claims where vehicle was parked/not driven.
- Rules are evaluated top-down; first match wins.

---

## Table 2: Assessment Decision Routing Table

This table determines how a claim is routed at the assessment stage.

### Conditions

| Condition | Values |
|---|---|
| C1: IDV of vehicle | ≤ ₹5,00,000 (L) / > ₹5,00,000 (H) |
| C2: Fraud indicator count | 0–2 (Low) / 3+ (High) |
| C3: Claim is a re-opened dispute | Yes (Y) / No (N) |
| C4: Policy type | Comprehensive (COMP) / Third-Party Only (TPO) |

### Actions

| Action | Description |
|---|---|
| B1: Assign to Standard Assessor | Queue for available standard assessor |
| B2: Assign to Senior Assessor | Queue for Senior Assessor only |
| B3: Set Fraud Review; alert Manager | Lock assessor controls; Manager takes over |
| B4: Assign to Senior Assessor (re-open) | Mandatory Senior Assessor for disputed claims |
| B5: Check coverage type before assignment | Validate incident type against TPO policy |

### Decision Table

| Rule | C1 | C2 | C3 | C4 | Action |
|---|---|---|---|---|---|
| R01 | L | Low | N | COMP | **B1** |
| R02 | H | Low | N | COMP | **B2** |
| R03 | L | High | - | - | **B3** |
| R04 | H | High | - | - | **B3** |
| R05 | - | Low | Y | COMP | **B4** |
| R06 | - | Low | Y | TPO | **B4 + B5** |
| R07 | L | Low | N | TPO | **B1 + B5** |
| R08 | H | Low | N | TPO | **B2 + B5** |

---

## Table 3: Claim Approval/Rejection Decision Table

Used by Assessor at the point of decision capture.

### Conditions

| Condition | Values |
|---|---|
| C1: Incident type covered under policy | Yes / No |
| C2: Exclusion clause applies | Yes / No |
| C3: Valid driving licence at incident time | Yes / No / Not Applicable |
| C4: Damage consistent with reported incident | Yes / Partially / No |
| C5: All mandatory documents verified | Yes / No |
| C6: Settlement amount ≤ IDV | Yes / No |

### Actions

| Action | Description |
|---|---|
| D1: Approve — Full Settlement | Proceed to settlement calculation |
| D2: Approve — Partial Settlement | Partial coverage; assessor notes required |
| D3: Reject — Coverage Exclusion | Rejection reason: exclusion clause |
| D4: Reject — Licence Issue | Rejection reason: no valid licence |
| D5: Reject — Inconsistent Damage | Rejection reason: damage inconsistency |
| D6: Escalate — Ambiguous Coverage | Manager review required |
| D7: Hold — Document Incomplete | Return to document submission |
| D8: Reject — Settlement Exceeds IDV | Finance must recalculate |

### Decision Table

| Rule | C1 | C2 | C3 | C4 | C5 | C6 | Action |
|---|---|---|---|---|---|---|---|
| R01 | Y | N | Y/NA | Yes | Y | Y | **D1 — Approve Full** |
| R02 | Y | N | Y/NA | Partially | Y | Y | **D2 — Approve Partial** |
| R03 | Y | Y | - | - | - | - | **D3 — Reject Exclusion** |
| R04 | Y | N | No | - | - | - | **D4 — Reject Licence** |
| R05 | Y | N | Y/NA | No | - | - | **D5 — Reject Inconsistency** |
| R06 | Y | N | Y/NA | Yes | N | - | **D7 — Hold Documents** |
| R07 | Y | N | Y/NA | Yes | Y | N | **D8 — Reject IDV Breach** |
| R08 | N | - | - | - | - | - | **D3 — Reject Coverage** |
| R09 | Y | N | Y/NA | Partially | Y | Y | **D6 — Escalate (ambiguous)** |

---

## Table 4: Escalation Decision Table

Determines Manager's action on escalated or fraud-flagged claims.

### Conditions

| Condition | Values |
|---|---|
| C1: Fraud indicators confirmed by investigation | Yes / No |
| C2: Coverage question resolved | Yes / No |
| C3: Assessor rationale sufficient | Yes / No |

### Actions

| Action | Description |
|---|---|
| E1: Refer to Special Investigation Unit | Fraud confirmed; claim locked |
| E2: Approve — Manager decision | Manager approves after review |
| E3: Reject — Manager decision | Manager rejects with rationale |
| E4: Return to Assessor with guidance | Coverage clarified; Assessor to re-evaluate |

| Rule | C1 | C2 | C3 | Action |
|---|---|---|---|---|
| R01 | Yes | - | - | **E1 — Refer to SIU** |
| R02 | No | Yes | Yes | **E2 or E3 — Manager decides** |
| R03 | No | No | - | **E4 — Return to Assessor** |
| R04 | No | Yes | No | **E4 — Return with guidance** |
