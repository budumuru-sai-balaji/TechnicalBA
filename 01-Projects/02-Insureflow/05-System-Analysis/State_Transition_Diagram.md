# Claim State Transition Diagram
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

---

## All Claim States

| State ID | State Name | Description |
|---|---|---|
| S01 | FNOL Registered | FNOL submitted; CRN issued; awaiting policy validation |
| S02 | Duplicate Hold | Duplicate claim detected; awaiting Manager review |
| S03 | Pending Validation | PAS unavailable; awaiting manual validation by Agent |
| S04 | Auto-Rejected | Policy expired/cancelled/suspended on incident date |
| S05 | Policy Validated | Policy confirmed active; document checklist sent |
| S06 | Pending Document Submission | Awaiting customer to upload mandatory documents |
| S07 | Documents Overdue | 5 BD elapsed without full document upload |
| S08 | Pending Agent Verification | Documents uploaded; Agent reviewing each document |
| S09 | Documents Verified | All mandatory documents verified by Agent |
| S10 | Assigned to Assessor | Claim in assessor queue; not yet opened |
| S11 | Under Assessment | Assessor actively reviewing the claim |
| S12 | Fraud Review | 3+ fraud indicators; locked for Manager review |
| S13 | Referred to SIU | Fraud confirmed; sent to Special Investigation Unit |
| S14 | Escalated | Assessor escalated; awaiting Manager decision |
| S15 | Approved | Claim approved; proceeding to settlement |
| S16 | Rejected | Claim rejected; customer notified |
| S17 | Pending Settlement | Settlement calculation in progress |
| S18 | Settlement Approved | Finance approved disbursement amount |
| S19 | Settled | Disbursement complete; claim closed |
| S20 | Re-opened | Customer disputed rejection; re-entered assessment |
| S21 | Closed — No Response | Documents Overdue and customer unresponsive > 30 days |

---

## State Transition Table

| From State | Trigger / Event | Guard Condition | To State | Actor |
|---|---|---|---|---|
| (Initial) | Customer submits FNOL | All mandatory fields valid | S01 — FNOL Registered | System |
| (Initial) | Customer submits FNOL | Fields invalid | Error returned; no state created | System |
| S01 | Duplicate detected | Same policy + same incident date | S02 — Duplicate Hold | System |
| S01 | PAS unavailable | API timeout / error | S03 — Pending Validation | System |
| S01 | PAS returns expired/cancelled/suspended | Policy not active on incident date | S04 — Auto-Rejected | System |
| S01 | PAS returns active policy | Policy active on incident date | S05 — Policy Validated | System |
| S02 | Manager clears duplicate | Manager confirms not a duplicate | S05 — Policy Validated | Manager |
| S02 | Manager confirms duplicate | True duplicate confirmed | S16 — Rejected | Manager |
| S03 | Agent validates manually | Agent confirms policy active | S05 — Policy Validated | Agent |
| S03 | Agent rejects manually | Agent confirms policy inactive | S04 — Auto-Rejected | Agent |
| S04 | (terminal for auto-reject) | — | No further transitions | — |
| S05 | Checklist sent to customer | — | S06 — Pending Document Submission | System |
| S06 | Customer uploads all mandatory docs | All checklist items uploaded | S08 — Pending Agent Verification | System |
| S06 | 5 business days elapsed without full upload | Mandatory docs still missing | S07 — Documents Overdue | System |
| S07 | Customer uploads remaining docs | All checklist items now uploaded | S08 — Pending Agent Verification | System |
| S07 | 30 days elapsed with no customer response | — | S21 — Closed — No Response | System |
| S08 | Agent marks all documents "Verified" | All docs pass readability check | S09 — Documents Verified | Agent |
| S08 | Agent marks a document "Illegible/Incorrect" | At least one doc fails | S06 — Pending Document Submission | Agent |
| S09 | Auto-assignment succeeds | Eligible assessor found | S10 — Assigned to Assessor | System |
| S09 | No eligible assessor within 2 hrs | — | S10 — Assigned to Assessor (Manager manual) | Manager |
| S10 | Assessor opens claim | — | S11 — Under Assessment | Assessor |
| S11 | 3+ fraud indicators triggered | Fraud counter ≥ 3 | S12 — Fraud Review | System |
| S11 | Assessor approves | Rationale ≥ 100 chars; amount ≤ IDV | S15 — Approved | Assessor |
| S11 | Assessor rejects | Rationale ≥ 100 chars | S16 — Rejected | Assessor |
| S11 | Assessor escalates | Rationale ≥ 100 chars | S14 — Escalated | Assessor |
| S12 | Manager reviews and clears fraud | Indicators not confirmed | S11 — Under Assessment (re-assign Senior) | Manager |
| S12 | Manager confirms fraud | Indicators confirmed | S13 — Referred to SIU | Manager |
| S12 | Manager approves (fraud cleared) | Fully reviewed | S15 — Approved | Manager |
| S12 | Manager rejects (fraud cleared) | Fully reviewed | S16 — Rejected | Manager |
| S13 | (terminal for SIU referral) | Outcome managed by SIU externally | No further InsureFlow transitions | SIU |
| S14 | Manager approves | — | S15 — Approved | Manager |
| S14 | Manager rejects | — | S16 — Rejected | Manager |
| S14 | Manager returns to assessor with guidance | Coverage clarified | S11 — Under Assessment | Manager |
| S15 | Settlement calculation complete | — | S17 — Pending Settlement | System |
| S16 | Customer disputes within 15 days | New evidence submitted | S20 — Re-opened | Customer |
| S16 | 15-day dispute window lapses | No customer action | S16 — Rejected (final) | System |
| S17 | Finance approves amount | Settlement reviewed | S18 — Settlement Approved | Finance |
| S17 | Finance overrides amount | Override with justification | S18 — Settlement Approved | Finance |
| S18 | Disbursement initiated and confirmed | PayGW returns success | S19 — Settled | System/PayGW |
| S18 | Disbursement fails | PayGW returns failure | S18 — Settlement Approved (retry) | Finance |
| S19 | (terminal — claim complete) | — | No further transitions | — |
| S20 | Re-assigned to Senior Assessor | — | S10 — Assigned to Assessor | System |
| S21 | (terminal — administratively closed) | — | No further transitions | — |

---

## State Transition Diagram (ASCII)

```
                        ┌─────────────────────────────┐
                        │          (START)             │
                        └──────────────┬──────────────┘
                                       │ Customer submits FNOL
                                       ▼
                              ┌────────────────┐
                              │  S01: FNOL     │
                              │  Registered    │
                              └───┬────┬───┬───┘
                   Duplicate ◄────┘    │    └────► PAS unavailable
                  detected             │
                     ▼                 │ PAS: active         ▼
              ┌──────────┐             │            ┌──────────────────┐
              │ S02:     │             │            │ S03: Pending     │
              │ Duplicate│             │            │ Validation       │
              │ Hold     │             ▼            └────┬─────────────┘
              └──┬────┬──┘   ┌─────────────────┐        │ Agent validates
        clear ◄──┘    │      │  S04: Auto-     │        ▼
                   confirm   │  Rejected ■      │   (back to S05)
                   duplicate └─────────────────┘
                      │
                      ▼                │ PAS: policy active
                   S16 Rejected    ────┘
                                       ▼
                              ┌─────────────────┐
                              │  S05: Policy    │
                              │  Validated      │
                              └────────┬────────┘
                                       │ Checklist sent
                                       ▼
                              ┌─────────────────┐    5 BD, no upload
                              │  S06: Pending   │───────────────────►
                              │  Doc Submission │◄──── re-upload     S07: Docs
                              └────────┬────────┘                    Overdue
                                       │ All docs uploaded           │
                                       │                     30 days │
                                       ▼                             ▼
                              ┌─────────────────┐             S21: Closed ■
                              │  S08: Pending   │◄── doc rejected
                              │  Agent Verif.   │
                              └────────┬────────┘
                                       │ All docs verified
                                       ▼
                              ┌─────────────────┐
                              │  S09: Documents │
                              │  Verified       │
                              └────────┬────────┘
                                       │ Auto-assignment
                                       ▼
                              ┌─────────────────┐
                              │  S10: Assigned  │
                              │  to Assessor    │
                              └────────┬────────┘
                                       │ Assessor opens
                                       ▼
                              ┌─────────────────┐
                              │  S11: Under     │◄─────────────────────┐
                              │  Assessment     │                       │
                              └──┬──┬──┬────────┘                      │
           3+ fraud indicators ◄─┘  │  └──► Escalate                   │
                                    │             ▼                     │
                                    │      ┌──────────────┐    return   │
                                    │      │  S14:        │────────────►┘
                                    │      │  Escalated   │
                                    │      └──┬────────┬──┘
                             Approve│    Approve│    Reject│
                                    │          ▼          ▼
              ┌──────────┐          │   ┌──────────┐  ┌──────────┐
              │ S12:     │          │   │ S15:     │  │ S16:     │
              │ Fraud    │          │   │ Approved │  │ Rejected │◄─── Assessor rejects
              │ Review   │          │   └────┬─────┘  └────┬─────┘
              └──┬──┬────┘          │        │              │
     confirm ◄──┘  │ clear          │ Approve│         dispute (15d)
     fraud         │                │        ▼              ▼
        ▼          ▼ (re-assess)    │  ┌──────────┐   ┌──────────┐
     S13:      S11 (Senior)         │  │ S17:     │   │  S20:    │
     SIU ■         │                │  │ Pending  │   │ Re-opened│
                   └────────────────┘  │ Settlement│  └────┬─────┘
                                       └────┬──────┘       │ Re-assign Senior
                                   Finance  │               ▼
                                   approves │          S10 (Senior)
                                            ▼
                                   ┌──────────────┐
                                   │ S18:         │
                                   │ Settlement   │
                                   │ Approved     │
                                   └──────┬───────┘
                                   PayGW  │ success
                                          ▼
                                   ┌──────────────┐
                                   │  S19:        │
                                   │  Settled ■   │
                                   └──────────────┘

■ = Terminal state
```

---

## Role Permissions per State Transition

| Transition | Permitted Actors | Notes |
|---|---|---|
| S01 → S02 | System only | Automated duplicate check |
| S02 → S05 or S16 | Manager only | Manual review required |
| S04 (auto-reject) | System only | No human can override without re-FNOL |
| S08 → S09 | Agent only | Cannot be bypassed |
| S11 → S15 | Assessor, Senior Assessor | Not permitted if fraud-flagged |
| S11 → S16 | Assessor, Senior Assessor | Rationale mandatory |
| S11 → S14 | Assessor only | Cannot escalate own re-assigned claim |
| S12 → S13 | Manager only | Fraud confirmation |
| S14 → S15/S16 | Manager only | Escalation resolution |
| S16 → S20 | Customer only | Within 15-day window |
| S17 → S18 | Finance only | Cannot be self-approved by Assessor |
| S18 → S19 | System + PayGW | Automated on payment confirmation |
