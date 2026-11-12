# Business Requirements Document (BRD)
## InsureFlow — Motor Insurance Claims Management Platform

**Document Version:** 1.0  
**Date:** November 2026  
**Author:** Sai Balaji Budumuru (Self-Directed BA)  
**Status:** Baseline

> **Disclaimer:** All rules, SLAs and business logic are hypothetical, created for portfolio demonstration. They do not represent actual insurer policy, IRDAI regulations or operational standards.

---

## 1. Executive Summary

InsureFlow is proposed to replace the current fragmented, manual motor insurance claims process with a digitised, workflow-driven platform. The platform aims to reduce average claim cycle time from 18+ business days to ≤ 10 business days, improve customer transparency, and strengthen audit-readiness.

---

## 2. Business Problem Statement

### 2.1 Current State Pain Points

| Pain Point | Business Impact |
|---|---|
| No structured FNOL intake; claims reported by phone or email | High data quality issues; missing incident details at intake |
| Manual policy validation by front-office staff | Average 2–3 day delay before assessment begins |
| No document checklist enforcement | Assessors receive incomplete files; rework and delays |
| Claim assignment via email to assessors | No workload visibility; uneven distribution |
| No customer-facing status tracker | High inbound call volume; estimated 40% of calls are status enquiries |
| Escalation and rejection not documented consistently | Audit findings; regulatory risk |
| Settlement calculated in spreadsheets | Calculation errors; no approval workflow |

### 2.2 Problem Statement

> The motor insurance claims team cannot process claims within acceptable timeframes or provide customers with real-time visibility because the end-to-end claims lifecycle is managed through disconnected manual activities across phone, email and spreadsheets, with no integrated system enforcing process compliance, document completeness or SLA adherence.

---

## 3. Business Objectives

| ID | Objective | KPI | Target |
|---|---|---|---|
| BO-01 | Reduce average claim cycle time | Average days from FNOL to settlement | ≤ 10 business days |
| BO-02 | Improve customer satisfaction | CSAT score (post-claim survey) | ≥ 4.0 / 5.0 |
| BO-03 | Reduce status-enquiry call volume | % of inbound calls related to status | < 15% |
| BO-04 | Improve document completeness at submission | % of claims with complete documents at FNOL | > 85% |
| BO-05 | Increase first-contact resolution | % of claims resolved without re-open | > 80% |
| BO-06 | Strengthen audit-readiness | Audit findings related to claims process | Zero critical findings |

---

## 4. Scope

### 4.1 In Scope
- Motor insurance claims only (private car and two-wheeler).
- Full claim lifecycle: FNOL → Policy Validation → Document Submission → Assessment → Decision → Settlement.
- Customer-facing web portal for FNOL submission and status tracking.
- Assessor dashboard for claim queue management and decision capture.
- Claims Manager dashboard for escalations, SLA monitoring and approvals.
- Integration with Policy Administration System (PAS) for policy validation.
- Integration with Payment Gateway for settlement disbursement.
- Automated notifications (SMS/email) at key lifecycle events.
- Audit log for all state transitions.

### 4.2 Out of Scope
- Health, life or commercial vehicle insurance claims.
- Third-party garage or surveyor portal.
- Policy issuance or renewal.
- Agent commission management.
- Mobile application (Phase 2).

---

## 5. Stakeholders

| Stakeholder | Type | Role |
|---|---|---|
| Claimant (Customer) | External | Submits FNOL, uploads documents, receives decisions |
| Front-Office Agent | Internal | Assists FNOL registration, validates customer identity |
| Claims Assessor | Internal | Reviews documents, assesses coverage, makes decision |
| Claims Manager | Internal | Assigns assessors, approves escalations, monitors SLAs |
| Finance / Settlement Team | Internal | Reviews settlement calculation, authorises disbursement |
| IT / Integration Team | Internal | Maintains PAS and payment integrations |
| Compliance / Audit | Internal | Reviews process adherence, accesses audit logs |
| Policy Administration System | External System | Provides policy data for validation |
| Payment Gateway | External System | Processes settlement disbursements |

---

## 6. Business Requirements

### 6.1 Claims Intake
| ID | Requirement |
|---|---|
| BR-01 | The business requires a structured, digital FNOL registration channel available to customers 24×7. |
| BR-02 | FNOL must capture: claimant identity, policy number, incident date, incident description, incident location and vehicle registration number. |
| BR-03 | The business requires real-time confirmation of FNOL registration with a unique Claim Reference Number. |

### 6.2 Policy Validation
| ID | Requirement |
|---|---|
| BR-04 | The business requires automated validation of policy status (active/expired/cancelled) at FNOL. |
| BR-05 | A claim must only proceed if the policy was active on the incident date. |
| BR-06 | The business requires that duplicate claims (same policy, same incident date) are automatically detected and held for review. |

### 6.3 Document Management
| ID | Requirement |
|---|---|
| BR-07 | The business requires a document checklist enforced per claim type (own damage, theft, third-party). |
| BR-08 | Assessment must not commence until all mandatory documents are verified as received and readable. |
| BR-09 | The business requires that document upload status is visible to both the customer and the claims team. |

### 6.4 Assessment
| ID | Requirement |
|---|---|
| BR-10 | The business requires rule-based assignment of claims to assessors based on claim type and vehicle IDV. |
| BR-11 | Assessors must be able to record their findings, coverage decision and supporting rationale in the system. |
| BR-12 | High-value claims (IDV > ₹5,00,000) must be assigned to a Senior Assessor. |

### 6.5 Decision and Settlement
| ID | Requirement |
|---|---|
| BR-13 | The business requires that approved, rejected and escalated decisions are recorded with mandatory rationale. |
| BR-14 | Settlement amount must be calculated based on IDV, applicable depreciation and policy excess. |
| BR-15 | Settlement disbursement must be initiated via the payment integration within 2 business days of approval. |
| BR-16 | Customers must receive formal written notification of the claims decision. |

### 6.6 Compliance and Audit
| ID | Requirement |
|---|---|
| BR-17 | All claim state transitions must be logged with user ID, role, timestamp and action rationale. |
| BR-18 | The business requires that SLA breaches are automatically flagged and escalated to the Claims Manager. |
| BR-19 | Fraud-indicator claims must be escalated to the Claims Manager and must not be auto-approved or auto-rejected. |

---

## 7. Constraints

| ID | Constraint |
|---|---|
| C-01 | The solution must integrate with the existing PAS using its published REST API — no changes to PAS are in scope. |
| C-02 | The platform must be browser-based; no desktop client installation. |
| C-03 | All data must be stored within India (data residency). |
| C-04 | Go-live budget and timeline are not defined in this document (portfolio scope). |

---

## 8. Assumptions

1. The PAS API provides reliable, real-time policy data with < 2-second response time.
2. All customers have internet access or can be assisted by front-office agents for FNOL.
3. Assessors are employees of the insurance company (not external contractors) for Phase 1.
4. SMS and email notification infrastructure exists and is available for integration.
5. IDV and depreciation calculation logic is already codified and owned by the Finance team.

---

## 9. High-Level Process Flow

```
Customer reports incident
        ↓
FNOL Registered (Claim Reference issued)
        ↓
Policy Validated (automated, via PAS)
        ↓
Coverage & Eligibility Checked
        ↓
Document Checklist Enforced
        ↓
Claim Assigned to Assessor
        ↓
Assessment Completed
        ↓
Decision: Approved / Rejected / Escalated
        ↓
Settlement Calculated (if Approved)
        ↓
Customer Notified of Final Decision
```

---

## 10. Success Criteria

1. All 10 lifecycle stages are supported within InsureFlow with no manual handoffs via email.
2. Policy validation completes in < 2 seconds in 95% of cases.
3. Customer portal enables FNOL submission without front-office agent involvement.
4. Audit log captures 100% of state transitions.
5. SLA breach escalation triggers within 30 minutes of threshold being crossed.
