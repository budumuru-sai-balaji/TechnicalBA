# Business Rules Catalogue
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

> **Disclaimer:** All rules are hypothetical and created for portfolio demonstration. They do not represent actual insurer terms or IRDAI regulations.

---

## Rule Naming Convention

`BR-[Category]-[Number]`

Categories: `ELIG` (Eligibility), `COV` (Coverage), `DOC` (Documents), `ASS` (Assessment), `SET` (Settlement), `FRAU` (Fraud), `SLA` (SLA/Escalation), `SYS` (System/Process)

---

## 1. Eligibility Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-ELIG-01 | A claim can only be registered against a policy that was active on the incident date. | Business | Critical | Auto-rejection |
| BR-ELIG-02 | The incident date must not be more than 30 calendar days prior to the FNOL registration date. | Business | Critical | FNOL rejected with error message |
| BR-ELIG-03 | The vehicle registration number on the FNOL must match the vehicle listed under the policy. | Business | Critical | FNOL blocked; customer notified |
| BR-ELIG-04 | Only the named policy holder or their authorised representative may register an FNOL. | Business | High | Agent must verify identity |
| BR-ELIG-05 | A policy in "Lapsed," "Cancelled" or "Suspended" status is not eligible for a claim. | Business | Critical | Auto-rejection with reason code |
| BR-ELIG-06 | A claim for theft requires an FIR (First Information Report) copy to be submitted. | Business | High | Claim cannot advance without FIR |

---

## 2. Coverage Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-COV-01 | The incident type submitted (Own Damage / Theft / Third-Party) must be covered under the policy type. | Business | Critical | Claim rejected — coverage exclusion |
| BR-COV-02 | A comprehensive policy covers Own Damage, Theft and Third-Party Liability. | Business | High | Coverage check in PAS |
| BR-COV-03 | A third-party-only policy does NOT cover Own Damage or Theft. | Business | Critical | Auto-rejection for ineligible incident type |
| BR-COV-04 | Damage caused by mechanical breakdown or wear and tear is excluded from Own Damage claims. | Business | High | Assessor rejection with exclusion code |
| BR-COV-05 | Damage caused while the vehicle was used for hire or reward (unless a commercial policy) is excluded. | Business | High | Assessor rejection |
| BR-COV-06 | Damage caused while the driver did not hold a valid driving licence is excluded. | Business | Critical | Claim rejected; licence verification required |
| BR-COV-07 | Loss or damage due to war, invasion or nuclear risk is excluded. | Business | Critical | Auto-exclusion rule |
| BR-COV-08 | Settlement shall not exceed the IDV declared at the time of policy issuance. | Business | Critical | System cap enforced |

---

## 3. Document Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-DOC-01 | A claim cannot advance to assessment until all mandatory documents are marked "Verified." | Business | Critical | Claim held in "Pending Document Submission" |
| BR-DOC-02 | Documents must be legible (not blurred, cropped or password-protected). | Business | High | Document marked "Illegible"; re-upload requested |
| BR-DOC-03 | Maximum document file size is 5 MB per file; accepted formats: PDF, JPG, PNG. | System | Medium | Upload blocked with error |
| BR-DOC-04 | If a re-upload is not completed within 5 business days of request, the claim is set to "Documents Overdue." | Business | High | Manager alert triggered |
| BR-DOC-05 | A repair estimate must be from a registered motor workshop. | Business | Medium | Assessor may flag for verification |

---

## 4. Assessment Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-ASS-01 | Claims with IDV > ₹5,00,000 must be assigned to a Senior Assessor. | Business | High | Assignment rule enforced; standard assessors excluded |
| BR-ASS-02 | An assessor cannot have more than 10 open claims assigned at any time. | Business | Medium | System skips assessor in auto-assignment |
| BR-ASS-03 | An assessor who escalated a claim cannot re-take it for final decision. | Business | High | Claim locked from original assessor after escalation |
| BR-ASS-04 | Assessor must provide a written rationale of at least 100 characters for any decision. | Business | High | Decision submission blocked |
| BR-ASS-05 | A claim flagged for fraud cannot be approved or rejected by an Assessor; it requires Manager decision. | Business | Critical | Approve/Reject controls hidden from Assessor |
| BR-ASS-06 | A previously rejected claim that has been re-opened must be assigned to a Senior Assessor. | Business | High | Assignment routing rule |

---

## 5. Settlement Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-SET-01 | Settlement = IDV − Policy Excess − Depreciation Amount. | Business | Critical | System enforces formula |
| BR-SET-02 | Depreciation applies by vehicle age bracket (see FRD for schedule). | Business | High | Applied automatically |
| BR-SET-03 | Settlement cannot exceed the IDV stored at claim registration. | Business | Critical | System cap; Finance override requires justification |
| BR-SET-04 | Finance may override the calculated settlement amount but must record justification. | Business | High | Override logged in audit trail |
| BR-SET-05 | Disbursement must be initiated within 2 business days of Finance approval. | SLA | High | SLA breach alert to Manager |
| BR-SET-06 | Settlement is paid to the bank account registered under the policy. | Business | High | Alternate payee requires Manager approval |

---

## 6. Fraud Rules

| Rule ID | Rule Statement | Source | Priority | Impact if Violated |
|---|---|---|---|---|
| BR-FRAU-01 | A claim with 3 or more fraud indicators is automatically set to "Fraud Review" status. | Business | Critical | Assessor decision locked |
| BR-FRAU-02 | Fraud indicators (hypothetical): (1) Incident reported > 15 days post-occurrence; (2) Policy < 30 days old on incident date; (3) Previous claim on same vehicle within 12 months; (4) Incident location outside policy registration district; (5) Claimant has 2+ claims in past 24 months. | Business | High | Each indicator increments a counter |
| BR-FRAU-03 | A fraud-confirmed claim is referred to the Special Investigation Unit (SIU) and set to "Referred to SIU." | Business | Critical | No settlement; claim locked |
| BR-FRAU-04 | A fraud-cleared claim is returned to the standard assessment workflow. | Business | High | Manager resets status and re-assigns |
| BR-FRAU-05 | Duplicate claims (same policy + same incident date) are auto-held and flagged to the Manager. | Business | Critical | Duplicate Hold status applied |

---

## 7. SLA and Escalation Rules

| Rule ID | Rule Statement | Threshold | Escalation Target |
|---|---|---|---|
| BR-SLA-01 | FNOL confirmation must be sent within 60 seconds of submission. | 60 seconds | System alert |
| BR-SLA-02 | Policy validation must complete within 2 business hours of FNOL. | 2 hours | Manager alert |
| BR-SLA-03 | Document verification must be completed within 2 business days of all docs received. | 2 BD | Manager alert |
| BR-SLA-04 | Assessor must begin review within 1 business day of assignment. | 1 BD | Manager alert |
| BR-SLA-05 | Assessment must be completed within 5 business days of assignment. | 5 BD | Manager alert |
| BR-SLA-06 | Manager must action escalated claims within 2 business days. | 2 BD | Director-level alert |
| BR-SLA-07 | Settlement disbursement must be initiated within 2 business days of Finance approval. | 2 BD | Manager alert |
| BR-SLA-08 | Auto-rejection notification to customer must be sent within 1 business day of trigger. | 1 BD | System alert |
| BR-SLA-09 | Re-opened claim must be re-assigned within 1 business day. | 1 BD | Manager alert |
