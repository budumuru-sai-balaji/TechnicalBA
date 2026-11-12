# Functional Requirements Document (FRD)
## InsureFlow — Motor Insurance Claims Management Platform

**Document Version:** 1.0  
**Date:** November 2026  
**Author:** Sai Balaji Budumuru (Self-Directed BA)  
**Status:** Baseline

---

## 1. Introduction

This document defines the functional requirements for InsureFlow, derived from the Business Requirements Document (BRD v1.0). Requirements are organised by feature area and written in a testable format.

**Actors:**
- **Customer** — Claimant using the web portal.
- **Agent** — Front-office staff member.
- **Assessor** — Claims assessor reviewing and deciding on claims.
- **Senior Assessor** — Assessor with authority over high-value claims.
- **Manager** — Claims Manager with escalation and SLA oversight.
- **System** — InsureFlow automated processing.
- **PAS** — Policy Administration System (external).
- **PayGW** — Payment Gateway (external).

---

## 2. FNOL Module

### FR-FNOL-01 — FNOL Submission
**Actor:** Customer / Agent  
**Description:** The system shall provide a structured web form for FNOL submission.  
**Fields required:**
- Full name, contact number, email address.
- Policy number (10-character alphanumeric).
- Vehicle registration number.
- Incident date (not future; not more than 30 calendar days prior).
- Incident type: Own Damage / Theft / Third-Party Liability.
- Incident location (free text + pin code).
- Brief incident description (minimum 50 characters).
- Optionally: FIR number (mandatory for Theft claims).

**Acceptance Criteria:**
- AC1: Submission with all mandatory fields complete generates a unique Claim Reference Number (CRN) in format CL-YYYY-XXXXXXXX.
- AC2: Submission without mandatory fields returns inline field-level validation messages.
- AC3: Incident date more than 30 days prior returns error: "Incident date exceeds the 30-day reporting window."
- AC4: Customer receives SMS and email confirmation with CRN within 60 seconds of submission.

### FR-FNOL-02 — Agent-Assisted FNOL
**Actor:** Agent  
**Description:** Agents shall be able to register an FNOL on behalf of a customer from the agent portal, with the customer's verbal consent noted.

### FR-FNOL-03 — Duplicate Claim Detection
**Actor:** System  
**Description:** On FNOL submission, the system shall query existing claims for the same policy number and incident date.  
**Acceptance Criteria:**
- AC1: If a match is found, the new FNOL is placed in "Duplicate Hold" status.
- AC2: An alert is raised for the Claims Manager to review within 1 business day.
- AC3: Customer receives notification: "Your claim is under preliminary review for verification."

---

## 3. Policy Validation Module

### FR-PV-01 — Automated Policy Verification
**Actor:** System, PAS  
**Description:** Immediately after FNOL submission, the system shall call the PAS Policy Verification API to validate:
- Policy number exists.
- Policy is active on the incident date.
- Policy covers the vehicle registration number submitted.
- Policy type includes the incident type submitted.

**Acceptance Criteria:**
- AC1: Validation completes within 2 seconds (95th percentile).
- AC2: If policy is valid, claim advances to "Policy Validated" status automatically.
- AC3: If policy is expired on the incident date, claim status is set to "Auto-Rejected — Expired Policy" and customer is notified within 1 business day.
- AC4: If PAS API is unavailable, claim is placed in "Pending Validation" and an alert is raised for the Agent to validate manually.

### FR-PV-02 — Coverage and Eligibility Check
**Actor:** System  
**Description:** Following policy validation, the system shall check coverage and eligibility against the following rules:
- Incident type is covered under the policy (e.g., theft coverage flag).
- No existing open claim on the same vehicle under the same policy.
- Policy excess and IDV are retrieved and stored against the claim record.

---

## 4. Document Management Module

### FR-DOC-01 — Document Checklist
**Actor:** System, Customer  
**Description:** The system shall display a mandatory document checklist based on the claim's incident type.

| Incident Type | Mandatory Documents |
|---|---|
| Own Damage | Duly filled claim form, RC copy, driving licence, repair estimate, photographs of damage (min. 3) |
| Theft | Duly filled claim form, RC copy, driving licence, FIR copy, all keys submitted confirmation, NOC from financer (if applicable) |
| Third-Party Liability | Duly filled claim form, RC copy, driving licence, FIR/Police report, third-party details |

**Acceptance Criteria:**
- AC1: Checklist is pre-populated on the customer portal upon policy validation.
- AC2: Document upload accepts: PDF, JPG, PNG (max 5 MB per file).
- AC3: Claim cannot advance to "Pending Assessment" until all mandatory documents are marked "Received."

### FR-DOC-02 — Document Verification
**Actor:** Agent / System  
**Description:** Each uploaded document shall be reviewed for readability and completeness by the Agent.  
**Acceptance Criteria:**
- AC1: Agents can mark each document as "Verified," "Illegible" or "Incorrect Document."
- AC2: If a document is marked "Illegible" or "Incorrect," the customer is notified to re-upload within 5 business days.
- AC3: Failure to re-upload within 5 business days places the claim in "Documents Overdue" status and triggers a Manager alert.

---

## 5. Assessment Module

### FR-ASS-01 — Assessor Assignment
**Actor:** System  
**Description:** When all documents are verified, the system shall auto-assign the claim to an assessor.

**Assignment rules:**
- Incident type must match assessor's certified specialisation.
- Assessor must have < 10 open claims in queue.
- If IDV > ₹5,00,000, only Senior Assessors are eligible for assignment.
- If no eligible assessor is available within 2 business hours, the Claims Manager is notified.

### FR-ASS-02 — Assessment Workspace
**Actor:** Assessor  
**Description:** Assessors shall have a dedicated workspace displaying: claim summary, all documents (with preview), policy details, vehicle IDV, and a decision capture form.  
**Decision options:** Approve / Reject / Escalate.  
**Mandatory fields on decision:**
- Decision type.
- Coverage finding (covered / not covered / partially covered).
- Supporting rationale (minimum 100 characters).
- Recommended settlement amount (if Approve).

### FR-ASS-03 — Fraud Indicator Flagging
**Actor:** System  
**Description:** The system shall evaluate claims against fraud indicators during assessment and flag if 3 or more indicators are present.

**Fraud indicators (hypothetical):**
1. Incident reported more than 15 days after occurrence.
2. Policy purchased less than 30 days before incident.
3. Previous claim on same vehicle within 12 months.
4. Incident location does not match policy registration district.
5. Claimant has 2 or more claims in the past 24 months.

**Acceptance Criteria:**
- AC1: Flagged claims are placed in "Fraud Review" status.
- AC2: Flagged claims cannot be approved or rejected by the Assessor; decision authority is transferred to the Manager.
- AC3: Manager receives an alert with the specific indicators triggered.

---

## 6. Decision and Escalation Module

### FR-DEC-01 — Claim Approval
**Actor:** Assessor / Senior Assessor  
**Description:** An approved claim advances to "Pending Settlement." Settlement amount entered by the assessor is validated against IDV (cannot exceed IDV minus policy excess).

### FR-DEC-02 — Claim Rejection
**Actor:** Assessor / Senior Assessor / Manager  
**Description:** A rejected claim is set to "Rejected" status. Customer is notified with the rejection reason and the right to dispute within 15 days.

### FR-DEC-03 — Claim Escalation
**Actor:** Assessor → Manager  
**Description:** Assessor can escalate a claim if: coverage decision is ambiguous, additional expertise is needed, or fraud indicators are present.  
**Acceptance Criteria:**
- AC1: Escalated claim appears in Manager's escalation queue.
- AC2: Escalating assessor cannot re-take the claim for decision after escalation.
- AC3: Manager must action the escalation within 2 business days (SLA).

### FR-DEC-04 — Dispute / Re-open
**Actor:** Customer  
**Description:** Within 15 days of a Rejection decision, the customer may submit a dispute with supporting evidence. The claim returns to "Re-opened" status and is re-assigned to a Senior Assessor.

---

## 7. Settlement Module

### FR-SET-01 — Settlement Calculation
**Actor:** System / Finance  
**Description:** The system shall calculate the settlement amount using the following formula:  

```
Settlement = IDV − Policy Excess − Depreciation Amount
```

Depreciation schedule (hypothetical):

| Vehicle Age | Depreciation Rate |
|---|---|
| < 6 months | 5% |
| 6 months – 1 year | 15% |
| 1 – 2 years | 20% |
| 2 – 3 years | 30% |
| 3 – 5 years | 40% |
| > 5 years | 50% |

**Acceptance Criteria:**
- AC1: Calculated settlement is displayed for Assessor and Finance review before disbursement.
- AC2: Finance can override the calculated amount with mandatory justification.
- AC3: Settlement cannot exceed the IDV stored at claim registration.

### FR-SET-02 — Disbursement
**Actor:** System, PayGW  
**Description:** Approved settlement is disbursed via the payment integration.  
**Acceptance Criteria:**
- AC1: Disbursement is initiated within 2 business days of Finance approval.
- AC2: Customer receives bank transfer confirmation via SMS/email.
- AC3: Disbursement status is updated in the claim record.

---

## 8. Notifications Module

| Trigger | Recipient | Channel |
|---|---|---|
| FNOL registered | Customer | SMS + Email |
| Policy validation failed | Customer + Agent | SMS + Email |
| Documents requested | Customer | SMS + Email |
| Documents overdue (Day 3) | Customer | SMS + Email |
| Claim assigned to assessor | Customer | Email |
| Assessment decision made | Customer | SMS + Email |
| Settlement disbursed | Customer | SMS + Email |
| SLA breach | Manager | In-app alert + Email |
| Fraud flag raised | Manager | In-app alert + Email |

---

## 9. Non-Functional Requirements

| ID | Requirement | Measure |
|---|---|---|
| NFR-01 | System availability | ≥ 99.5% during 08:00–20:00 IST, Mon–Sat |
| NFR-02 | Policy validation API response time | < 2 seconds at 95th percentile |
| NFR-03 | Portal page load time | < 3 seconds on a 10 Mbps connection |
| NFR-04 | Concurrent users | Support 500 concurrent sessions |
| NFR-05 | Audit log retention | 7 years, non-deletable |
| NFR-06 | Document storage encryption | AES-256 at rest |
| NFR-07 | Role-based access control | Minimum 5 roles: Customer, Agent, Assessor, Senior Assessor, Manager |
| NFR-08 | Session timeout | 20 minutes of inactivity |
