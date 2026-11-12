# SLA Matrix
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

> **Disclaimer:** All SLA values are hypothetical and for portfolio demonstration purposes only. They do not represent IRDAI mandated timelines or actual insurer commitments.

---

## SLA Definitions

- **Business Day (BD):** Monday–Saturday, 09:00–18:00 IST (excluding public holidays).
- **Clock Time:** Calendar minutes/hours regardless of business hours.
- **Breach:** SLA threshold exceeded without completion.
- **Escalation Target:** Role/person who receives alert on breach.

---

## 1. Process-Level SLA Matrix

| SLA ID | Process Stage | Metric | Standard SLA | Breach Threshold | Escalation Target | Monitoring Frequency |
|---|---|---|---|---|---|---|
| SLA-01 | FNOL Confirmation | Time from FNOL submission to CRN + notification sent | 60 seconds (clock) | > 60 seconds | System alert (auto-retry) | Real-time |
| SLA-02 | Policy Validation | Time from FNOL submission to policy validation complete | 2 hours (BD) | > 2 BD hours | Claims Manager | Every 30 min |
| SLA-03 | Document Checklist Dispatch | Time from policy validation to checklist sent to customer | 30 minutes (clock) | > 1 hour | Agent Supervisor | Every 15 min |
| SLA-04 | Customer Document Upload | Time allowed for customer to upload all mandatory documents | 5 BD | > 5 BD | Manager alert; Day 3 reminder sent | Daily |
| SLA-05 | Agent Document Verification | Time from documents received to all verified | 2 BD | > 2 BD | Claims Manager | Every 4 hours |
| SLA-06 | Assessor Assignment | Time from Documents Verified to claim assigned | 2 BD hours | > 2 BD hours | Claims Manager | Every 30 min |
| SLA-07 | Assessor First Review | Time from assignment to assessor opening the claim | 1 BD | > 1 BD | Claims Manager | Daily |
| SLA-08 | Assessment Completion | Time from assignment to decision submitted | 5 BD | > 5 BD | Claims Manager | Daily |
| SLA-09 | High-Value Assessment | Time from assignment to decision (IDV > ₹5,00,000) | 7 BD | > 7 BD | Senior Claims Manager | Daily |
| SLA-10 | Escalation Resolution | Time from escalation raised to Manager decision | 2 BD | > 2 BD | Director-level alert | Every 4 hours |
| SLA-11 | Fraud Review Resolution | Time from Fraud Review status to Manager decision | 3 BD | > 3 BD | Director-level alert | Daily |
| SLA-12 | Rejection Notification | Time from rejection decision to customer notification sent | 1 BD | > 1 BD | Agent Supervisor | Every 2 hours |
| SLA-13 | Settlement Calculation | Time from Approved status to settlement amount calculated | 1 BD | > 1 BD | Finance Manager | Every 4 hours |
| SLA-14 | Finance Approval | Time from Pending Settlement to Finance approval | 2 BD | > 2 BD | Finance Manager | Daily |
| SLA-15 | Disbursement Initiation | Time from Finance approval to payment initiated | 2 BD | > 2 BD | Finance Manager + Claims Manager | Every 4 hours |
| SLA-16 | Disbursement Completion | Time from payment initiated to customer receipt confirmed | 3 BD | > 3 BD | Finance Manager | Daily |
| SLA-17 | Re-open Assignment | Time from Re-opened status to Senior Assessor assigned | 1 BD | > 1 BD | Claims Manager | Every 2 hours |
| SLA-18 | Auto-rejection Notification | Time from auto-rejection trigger to customer notification | 1 BD | > 1 BD | Agent Supervisor | Every 2 hours |

---

## 2. End-to-End SLA Summary

| Claim Scenario | Target Cycle Time | Maximum Cycle Time | Measurement |
|---|---|---|---|
| Standard Own Damage claim (no complications) | ≤ 10 BD | 15 BD | FNOL to Settled |
| Theft claim (all documents available) | ≤ 12 BD | 18 BD | FNOL to Settled |
| High-value claim (IDV > ₹5,00,000) | ≤ 14 BD | 20 BD | FNOL to Settled |
| Escalated claim | ≤ 15 BD | 22 BD | FNOL to Settled |
| Fraud-reviewed claim (cleared) | ≤ 18 BD | 25 BD | FNOL to Settled |
| Disputed (re-opened) claim | ≤ 20 BD | 30 BD | Dispute submitted to Settled |

---

## 3. SLA Breach Escalation Matrix

| Breach Level | Trigger | Action | Recipient |
|---|---|---|---|
| Level 1 — Warning | SLA at 75% elapsed with no completion | In-app alert | Responsible actor (Assessor / Agent / Finance) |
| Level 2 — Breach | SLA threshold crossed | In-app alert + email | Claims Manager |
| Level 3 — Escalation | Breach > 50% beyond threshold (e.g., 7.5 BD on a 5 BD SLA) | Urgent email + call trigger | Senior Claims Manager / Director |
| Level 4 — Critical | Multiple SLA breaches on same claim | Escalation meeting triggered | Head of Claims |

---

## 4. SLA Reporting

| Report | Frequency | Audience | Key Metrics |
|---|---|---|---|
| Daily SLA Dashboard | Daily, 08:00 IST | Claims Manager | Open SLA breaches, aging claims, assessor queue depths |
| Weekly Claims Performance Report | Weekly, Monday | Head of Claims | Cycle time trends, breach rates by stage, resolution rates |
| Monthly SLA Compliance Report | Monthly | Compliance / Audit | SLA compliance %, breach root-cause analysis |
| Customer Communication Timeliness | Weekly | Agent Supervisor | Notification SLA adherence |
