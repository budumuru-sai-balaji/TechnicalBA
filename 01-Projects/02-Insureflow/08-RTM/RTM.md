# Requirements Traceability Matrix (RTM)
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

---

## Purpose

The RTM links every business requirement (from the BRD) through to functional requirements (FRD), business rules (BR), test cases (UAT), and artefacts. This ensures full coverage and no orphaned requirements.

---

## Column Definitions

| Column | Definition |
|---|---|
| BRD Ref | Business requirement from BRD |
| FR Ref | Functional requirement(s) from FRD |
| Business Rule Ref | Applicable business rules |
| API / System Ref | Relevant API or system component |
| UAT Test Case | Corresponding UAT scenario |
| Artefact | Diagram or design artefact |
| Coverage Status | Fully Covered / Partially Covered / Not Covered |

---

## RTM Table

| BRD Ref | Business Requirement | FR Ref | Business Rules | API / System | UAT Test Case | Artefact | Status |
|---|---|---|---|---|---|---|---|
| BR-01 | 24×7 digital FNOL channel | FR-FNOL-01 | BR-ELIG-02 | POST /claims | UAT-01, UAT-02 | FNOL BPMN, Wireframe-01 | Fully Covered |
| BR-02 | FNOL must capture all required fields | FR-FNOL-01 | BR-ELIG-01, BR-ELIG-02, BR-ELIG-03 | POST /claims (validation) | UAT-02, UAT-07 | FNOL BPMN | Fully Covered |
| BR-03 | Real-time CRN confirmation | FR-FNOL-01 | — | POST /claims (201 response) | UAT-01 | API Spec 2 | Fully Covered |
| BR-04 | Automated policy status validation | FR-PV-01 | BR-ELIG-01, BR-POL-01–05 (EX-POL) | POST /policies/verify | UAT-03, UAT-04 | FNOL BPMN, API Spec 1 | Fully Covered |
| BR-05 | Claim proceeds only if policy active on incident date | FR-PV-01 | BR-ELIG-01, BR-COV-01 | POST /policies/verify (eligibility block) | UAT-03 | Decision Table 1, State Transition | Fully Covered |
| BR-06 | Duplicate claim detection | FR-FNOL-03 | BR-FRAU-05 | POST /claims (409 response) | UAT-08 | FNOL BPMN, Exception Matrix EX-FRD-03 | Fully Covered |
| BR-07 | Document checklist per claim type | FR-DOC-01 | BR-DOC-01 | POST /claims (nextSteps.documentChecklist) | UAT-04 | FRD Table, Wireframe-01 | Fully Covered |
| BR-08 | Assessment cannot start without all verified docs | FR-DOC-01, FR-DOC-02 | BR-DOC-01 | System: status gate at DOC_VER | UAT-04 | State Transition, BPMN Assessment | Fully Covered |
| BR-09 | Document upload status visible to customer and team | FR-DOC-01 | BR-DOC-01 | GET /claims/{ref} (documents block) | UAT-04 | API Spec 3, Wireframe-01 | Fully Covered |
| BR-10 | Rule-based assessor assignment | FR-ASS-01 | BR-ASS-01, BR-ASS-02 | System: auto-assignment engine | UAT-05 | Assessment BPMN, Decision Table 2 | Fully Covered |
| BR-11 | Assessors record findings and rationale | FR-ASS-02 | BR-ASS-04 | System: assessment workspace | UAT-05, UAT-06 | Wireframe-02, FRD FR-ASS-02 | Fully Covered |
| BR-12 | High-value claims (IDV > ₹5L) → Senior Assessor | FR-ASS-01 | BR-ASS-01 | System: assignment filter | UAT-05 | Decision Table 2, Assessment BPMN | Fully Covered |
| BR-13 | Decisions recorded with mandatory rationale | FR-DEC-01, FR-DEC-02, FR-DEC-03 | BR-ASS-04 | System: decision capture form | UAT-05, UAT-06 | FRD, Wireframe-02 | Fully Covered |
| BR-14 | Settlement = IDV − Excess − Depreciation | FR-SET-01 | BR-SET-01, BR-SET-02, BR-SET-03 | System: settlement engine | UAT-09 | FRD FR-SET-01, Data Dictionary | Fully Covered |
| BR-15 | Disbursement within 2 BD of Finance approval | FR-SET-02 | BR-SET-05 | PayGW integration | UAT-09 | SLA Matrix SLA-15, Assessment BPMN | Fully Covered |
| BR-16 | Customer receives formal decision notification | FR-DEC-01, FR-DEC-02 | BR-SLA-12 | Notification engine | UAT-05, UAT-06 | Notifications table in FRD | Fully Covered |
| BR-17 | All state transitions logged with user/timestamp | NFR-03 | — | System: audit log (INSERT-only) | (All UAT cases validate status) | ERD: AUDIT_LOG, Data Dictionary | Fully Covered |
| BR-18 | SLA breaches auto-flagged and escalated | NFR-01 (via SLA matrix) | BR-SLA-01–09 | System: SLA monitoring engine | UAT-10 | SLA Matrix, Exception Matrix | Fully Covered |
| BR-19 | Fraud-indicator claims → Manager; not auto-decided | FR-ASS-03 | BR-FRAU-01–05, BR-ASS-05 | System: fraud flag + lock | UAT-10 | Decision Table 4, Exception Matrix EX-FRD | Fully Covered |

---

## Functional Requirements Traceability

| FR Ref | Functional Requirement | BRD Ref | UAT Test Case | Status |
|---|---|---|---|---|
| FR-FNOL-01 | FNOL submission via web form | BR-01, BR-02, BR-03 | UAT-01, UAT-02 | Covered |
| FR-FNOL-02 | Agent-assisted FNOL | BR-01 | UAT-02 | Covered |
| FR-FNOL-03 | Duplicate claim detection | BR-06 | UAT-08 | Covered |
| FR-PV-01 | Automated policy verification | BR-04, BR-05 | UAT-03, UAT-04 | Covered |
| FR-PV-02 | Coverage and eligibility check | BR-05 | UAT-03 | Covered |
| FR-DOC-01 | Document checklist enforcement | BR-07, BR-08, BR-09 | UAT-04 | Covered |
| FR-DOC-02 | Document verification by Agent | BR-08, BR-09 | UAT-04 | Covered |
| FR-ASS-01 | Assessor auto-assignment | BR-10, BR-12 | UAT-05 | Covered |
| FR-ASS-02 | Assessment workspace and decision capture | BR-11, BR-13 | UAT-05, UAT-06 | Covered |
| FR-ASS-03 | Fraud indicator flagging | BR-19 | UAT-10 | Covered |
| FR-DEC-01 | Claim approval | BR-13, BR-16 | UAT-05 | Covered |
| FR-DEC-02 | Claim rejection | BR-13, BR-16 | UAT-06 | Covered |
| FR-DEC-03 | Claim escalation | BR-13 | UAT-10 | Covered |
| FR-DEC-04 | Dispute / Re-open | — | UAT-06 | Covered |
| FR-SET-01 | Settlement calculation | BR-14 | UAT-09 | Covered |
| FR-SET-02 | Disbursement via payment integration | BR-15 | UAT-09 | Covered |

---

## UAT Traceability Summary

| UAT ID | Test Scenario | BRD Refs Covered | FR Refs | Business Rules |
|---|---|---|---|---|
| UAT-01 | Successful FNOL submission | BR-01, BR-02, BR-03 | FR-FNOL-01 | BR-ELIG-02 |
| UAT-02 | FNOL with invalid policy number | BR-02, BR-04 | FR-FNOL-01, FR-PV-01 | BR-ELIG-01, BR-POL-04 |
| UAT-03 | FNOL with expired policy | BR-04, BR-05 | FR-PV-01 | BR-ELIG-01, BR-COV-01 |
| UAT-04 | Missing mandatory documents | BR-07, BR-08 | FR-DOC-01 | BR-DOC-01 |
| UAT-05 | Claim approval with valid documents | BR-10–16 | FR-ASS-01, FR-ASS-02, FR-DEC-01 | BR-ASS-01–04, BR-SET-01–03 |
| UAT-06 | Claim rejection with dispute raised | BR-13, BR-16 | FR-DEC-02, FR-DEC-04 | BR-ASS-04 |
| UAT-07 | Incident date outside 30-day window | BR-02 | FR-FNOL-01 | BR-ELIG-02 |
| UAT-08 | Duplicate claim detection | BR-06 | FR-FNOL-03 | BR-FRAU-05 |
| UAT-09 | Settlement calculation and disbursement | BR-14, BR-15 | FR-SET-01, FR-SET-02 | BR-SET-01–06 |
| UAT-10 | Fraud flag triggered and escalated | BR-19 | FR-ASS-03, FR-DEC-03 | BR-FRAU-01–04, BR-ASS-05 |

---

## Coverage Summary

| Category | Total Requirements | Fully Covered | Partially Covered | Not Covered |
|---|---|---|---|---|
| Business Requirements (BRD) | 19 | 19 | 0 | 0 |
| Functional Requirements (FRD) | 16 | 16 | 0 | 0 |
| UAT Test Cases | 10 | 10 | 0 | 0 |
| **Overall** | **45** | **45 (100%)** | **0** | **0** |
