# InsureFlow — Insurance Claims Management Platform

> **Disclaimer:** This is a self-directed Business Analyst portfolio project. All business rules, SLAs, eligibility criteria and workflow logic are hypothetical and created for demonstration purposes. They do not represent actual insurer policy terms, regulatory requirements (IRDAI or otherwise), or the operational standards of any real insurance company.

---

## Business Problem

A mid-sized motor insurance company processes claims through a fragmented, largely manual workflow:

- Customers report incidents via phone or email with no structured intake.
- Policy validation is done manually by front-office staff, causing delays.
- Document verification lacks a checklist-driven process, leading to rework.
- Assessors receive claim files via email with no assignment tracking.
- Claim status is not visible to customers, generating high inbound call volumes.
- Escalation and rejection decisions are inconsistently documented.
- Settlement calculations are done in spreadsheets, introducing error risk.

**Impact:** Average claim cycle time exceeds 18 business days. Customer satisfaction scores are low, re-open rates are high, and regulatory audit readiness is poor.

---

## Proposed Solution

**InsureFlow** is a motor insurance claims management platform that digitises and automates the full claim lifecycle — from First Notice of Loss (FNOL) through settlement — with:

- Structured FNOL intake (web portal and API).
- Automated policy validation against the core policy administration system.
- Document checklist enforcement with upload tracking.
- Rule-based assessor assignment.
- Decision-support tooling for coverage and eligibility evaluation.
- Real-time claim status visibility for customers and staff.
- Audit-ready state machine with full transition history.
- SLA monitoring and escalation triggers.

**Target outcome:** Reduce average claim cycle time to ≤ 10 business days and increase first-contact resolution to > 80%.

---

## Role: Self-Directed Business Analyst

This project was designed and executed independently as a BA portfolio case study. Responsibilities simulated:

| Activity | Output |
|---|---|
| Stakeholder identification and needs analysis | BRD |
| Functional decomposition | FRD |
| Process modelling | BPMN diagrams |
| Business rule elicitation | Business Rules Document + Decision Tables |
| System context and integration analysis | API Specifications, System Context Diagram |
| Data modelling | ERD + Data Dictionary |
| Requirements traceability | RTM |
| Test scenario design | UAT Test Cases |
| Exception and risk analysis | Exception Matrix |
| Prototype design | Wireframe descriptions |

---

## Stakeholders

| Stakeholder | Role | Interest |
|---|---|---|
| Claimant (Customer) | External | Submit claims, track status, receive settlement |
| Front-Office Agent | Internal | Register FNOL, assist customers |
| Claims Assessor | Internal | Evaluate claim, make coverage decision |
| Claims Manager | Internal | Assign assessors, approve escalations, review SLA breaches |
| Finance / Settlement Team | Internal | Calculate and disburse settlement amounts |
| IT / Integration Team | Internal | Maintain policy admin and payment system integrations |
| Compliance / Audit | Internal | Ensure regulatory and process adherence |
| Policy Administration System | External System | Source of truth for policy data |
| Payment Gateway | External System | Settlement disbursement |

---

## Key Assumptions

1. The platform integrates with an existing Policy Administration System (PAS) via REST APIs.
2. Motor insurance only (two-wheeler and four-wheeler); health and life claims are out of scope.
3. All monetary values are in INR (Indian Rupees).
4. The fraud-detection module flags claims for review but does not auto-reject.
5. A claim may have multiple supporting documents, each with its own verification status.
6. Settlement calculation logic (IDV, depreciation) is owned by the Finance team and exposed via an internal API.
7. IRDAI regulations are referenced conceptually; this project does not constitute compliance documentation.
8. Hypothetical SLA values are used throughout.

---

## Key Requirements Summary

### Functional
- FR-01: System shall allow customers to register an FNOL via the web portal.
- FR-02: System shall validate policy number and coverage dates against the PAS in real time.
- FR-03: System shall enforce a document checklist per claim type before allowing assessment.
- FR-04: System shall auto-assign claims to available assessors based on workload and claim type.
- FR-05: System shall track claim status transitions and timestamp each state change.
- FR-06: System shall calculate settlement amount using IDV and applicable depreciation.
- FR-07: System shall notify the customer at each major status transition via SMS/email.
- FR-08: System shall flag claims meeting fraud-indicator criteria for manager review.
- FR-09: System shall escalate claims that breach SLA thresholds automatically.
- FR-10: System shall provide an assessor dashboard with claim queue, documents and decision capture.

### Non-Functional
- NFR-01: Policy validation API response < 2 seconds (95th percentile).
- NFR-02: System availability ≥ 99.5% during business hours.
- NFR-03: All claim state transitions shall be logged with user ID and timestamp (audit trail).
- NFR-04: Document storage shall be encrypted at rest.
- NFR-05: Role-based access control with at least four roles: Customer, Agent, Assessor, Manager.

---

## Important Business Rules

| Rule ID | Rule |
|---|---|
| BR-01 | A claim can only be registered against an active policy on the incident date. |
| BR-02 | Incident date must not be more than 30 days prior to FNOL registration date. |
| BR-03 | Claims with IDV > ₹5,00,000 require senior assessor assignment. |
| BR-04 | A claim with three or more fraud indicators must be escalated to the Claims Manager. |
| BR-05 | A rejected claim may be re-opened within 15 days if new evidence is submitted. |
| BR-06 | Settlement cannot exceed the declared Insured Declared Value (IDV). |
| BR-07 | Depreciation deductions are applied by vehicle age bracket. |
| BR-08 | Duplicate claims (same policy, same incident date) are automatically flagged and held. |
| BR-09 | An expired-policy claim is auto-rejected; customer receives rejection notice within 1 business day. |
| BR-10 | Assessor cannot approve their own re-assigned or escalated claim. |

---

## Process Diagrams

| Diagram | Location |
|---|---|
| FNOL Process BPMN | `03-BPMN-Process-Models/FNOL_Process_BPMN.md` |
| Claim Assessment BPMN | `03-BPMN-Process-Models/Claim_Assessment_BPMN.md` |
| Claim State Transition Diagram | `05-System-Analysis/State_Transition_Diagram.md` |

---

## Deliverables Index

| # | Folder | File | Artefact |
|---|---|---|---|
| 1 | 01-Business-Requirements | BRD.md | Business Requirements Document |
| 2 | 02-Functional-Requirements | FRD.md | Functional Requirements Document |
| 3 | 03-BPMN-Process-Models | FNOL_Process_BPMN.md | FNOL BPMN (text notation) |
| 4 | 03-BPMN-Process-Models | Claim_Assessment_BPMN.md | Assessment BPMN |
| 5 | 04-Business-Rules | Business_Rules.md | Business Rules Catalogue |
| 6 | 04-Business-Rules | Decision_Table_Claim_Approval.md | Claim Approval Decision Table |
| 7 | 05-System-Analysis | State_Transition_Diagram.md | Claim State Transition Diagram |
| 8 | 05-System-Analysis | SLA_Matrix.md | SLA Matrix |
| 9 | 05-System-Analysis | Exception_Matrix.md | Exception Matrix |
| 10 | 06-Data-Models | ERD.md | Entity Relationship Diagram |
| 11 | 06-Data-Models | Data_Dictionary.md | Data Dictionary |
| 12 | 07-API-Specifications | API_Spec_Policy_Verification.md | Policy Verification API |
| 13 | 07-API-Specifications | API_Spec_Claim_Submission.md | Claim Submission API |
| 14 | 07-API-Specifications | API_Spec_Claim_Status.md | Claim Status API |
| 15 | 08-RTM | RTM.md | Requirements Traceability Matrix |
| 16 | 09-UAT | UAT_Test_Cases.md | UAT Test Scenarios |
| 17 | 10-Prototype | Wireframes.md | Wireframe Descriptions |

---

## Key Analytical Decisions

1. **FNOL as a separate state from "Claim Registered"** — Separating FNOL from full claim registration allows the system to handle incomplete submissions gracefully and reduces drop-off.
2. **Fraud flagging, not auto-rejection** — Auto-rejection creates regulatory and customer-service risk; a manager review gate was modelled instead.
3. **Document checklist enforcement pre-assessment** — Moving document completeness checks before assessor assignment prevents wasted assessor time on incomplete files.
4. **IDV-based assignment routing** — Using IDV as the routing criterion for senior assessors reflects common industry practice and reduces decision risk on high-value claims.
5. **Re-open window of 15 days** — Balances customer dispute rights with operational finality of closed claims.

---

## Limitations and Future Enhancements

### Limitations
- Fraud detection rules are illustrative indicators, not ML-based scoring.
- Settlement calculation assumes simplified IDV/depreciation logic.
- No third-party garage / surveyor portal is modelled (out of scope).
- Regulatory compliance (IRDAI Circular references) is not validated.

### Future Enhancements
- ML-based fraud scoring engine integration.
- Mobile app for customer claim submission with photo upload.
- Surveyor portal for field assessment and geo-tagged photo submission.
- Real-time integration with IIB (Insurance Information Bureau) for policy history checks.
- Analytics dashboard for Claims Manager: cycle-time trends, rejection rates, assessor performance.
- WhatsApp / chatbot channel for FNOL intake.
