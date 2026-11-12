# Exception Matrix
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

---

## Purpose

This matrix defines how InsureFlow handles exceptions — abnormal scenarios that deviate from the standard happy path. Each entry includes the exception trigger, system response, actor responsibilities and resolution path.

---

## Exception Categories

| Category | Code Prefix | Description |
|---|---|---|
| Policy Exceptions | EX-POL | Issues relating to policy status or coverage |
| Document Exceptions | EX-DOC | Issues with document submission or verification |
| Assessment Exceptions | EX-ASS | Issues during claim assessment |
| Fraud Exceptions | EX-FRD | Suspected or confirmed fraudulent activity |
| Technical Exceptions | EX-TEC | System/integration failures |
| Settlement Exceptions | EX-SET | Issues during settlement calculation or disbursement |
| Customer Exceptions | EX-CUS | Customer behaviour exceptions |

---

## 1. Policy Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-POL-01 | Expired Policy | PAS returns policy end date before incident date | Auto-set claim to "Auto-Rejected"; trigger rejection notification | None required (automated) | Claim closed. Customer may contact insurer for policy renewal dispute (outside system scope) |
| EX-POL-02 | Cancelled Policy | PAS returns policy status = "Cancelled" | Auto-set claim to "Auto-Rejected"; trigger rejection notification | None required | Claim closed. If customer disputes cancellation, handled offline by policy team |
| EX-POL-03 | Suspended Policy | PAS returns policy status = "Suspended" | Auto-set claim to "Auto-Rejected" | None required | Claim closed. Suspension reason communicated by policy team |
| EX-POL-04 | Policy Not Found | PAS returns no record for submitted policy number | FNOL blocked; error message: "Policy number not found. Please verify and resubmit." | Agent notified to assist customer | Customer corrects policy number; FNOL re-submitted |
| EX-POL-05 | Vehicle Mismatch | Vehicle registration on FNOL does not match PAS policy | FNOL blocked; error: "Vehicle registration does not match policy records." | Agent verifies and assists | Customer corrects VRN; if genuine mismatch → escalate to policy team |
| EX-POL-06 | Incident Type Not Covered | Policy type does not include submitted incident type (e.g., theft on TPO policy) | Claim auto-rejected; reason code COV-01 | None required | Claim closed. Rejection notice sent |
| EX-POL-07 | PAS API Unavailable | PAS API returns timeout or 5xx error | Claim set to "Pending Validation"; Agent alerted for manual validation | Agent to validate policy via offline PAS access | Agent validates manually; updates claim to Policy Validated or rejects |

---

## 2. Document Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-DOC-01 | Illegible Document | Agent marks document as "Illegible" | Re-upload request sent to customer; 5 BD window | Agent monitors re-upload; re-reviews after upload | Customer re-uploads; Agent re-verifies |
| EX-DOC-02 | Incorrect Document | Agent marks document as "Incorrect Document" (wrong file type) | Re-upload request sent; correct document specified | Agent sends instructions on required document | Customer submits correct document; re-verification |
| EX-DOC-03 | Documents Overdue | No complete document set after 5 BD from request | Claim set to "Documents Overdue"; Manager alerted; reminder sent to customer | Manager reviews and decides: extend window or close claim | Manager extends deadline (once only) or closes claim with 30-day re-open option |
| EX-DOC-04 | Missing FIR for Theft | Theft claim submitted without FIR copy | Checklist item flagged; claim cannot advance without FIR | Agent notifies customer of FIR requirement | Customer uploads FIR; Agent verifies |
| EX-DOC-05 | File Too Large | Customer uploads file > 5 MB | Upload blocked; error: "File exceeds 5 MB limit. Please compress and re-upload." | None required (automated) | Customer compresses file and re-uploads |
| EX-DOC-06 | Unsupported File Format | Customer uploads file not in PDF/JPG/PNG | Upload blocked; error listing accepted formats | None required (automated) | Customer converts and re-uploads |
| EX-DOC-07 | Repair Estimate from Unregistered Workshop | Assessor flags estimate source as unregistered | Assessor flags document; notifies Manager | Manager decides: accept or request replacement estimate | Customer submits estimate from registered workshop, or Manager accepts with justification |

---

## 3. Assessment Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-ASS-01 | No Eligible Assessor Available | Auto-assignment finds no assessor meeting criteria within 2 hrs | Manager alerted; "Unassigned" flag on claim | Manager manually assigns assessor or adjusts workload | Manager assigns; claim enters assessor queue |
| EX-ASS-02 | Assessor Conflict of Interest | Assessor is a named party in the claim or related to claimant | Conflict flag raised (manual or assessor-declared) | Assessor must declare; Manager re-assigns | Claim reassigned to different assessor |
| EX-ASS-03 | Assessor Inactivity | Assigned assessor has not opened claim within 1 BD (SLA-07 breach) | Manager alerted; Level 2 SLA breach | Manager contacts assessor; reassigns if no response | Claim reassigned or assessor confirms action |
| EX-ASS-04 | Ambiguous Coverage Decision | Assessor cannot determine coverage (escalates) | Claim set to "Escalated"; removed from assessor queue | Manager reviews and provides decision or guidance | Manager decides or returns to assessor with guidance |
| EX-ASS-05 | Assessment SLA Breach | Assessment not completed within 5 BD (SLA-08) | Level 2 alert to Manager; Level 3 at 7.5 BD | Manager investigates delay; escalates if needed | Manager actions or escalates to Senior Manager |
| EX-ASS-06 | Settlement Exceeds IDV | Assessor enters recommended settlement > IDV | System blocks submission; error: "Settlement cannot exceed IDV of ₹X." | Assessor corrects amount | Assessor revises settlement to ≤ IDV |

---

## 4. Fraud Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-FRD-01 | Fraud Indicators Triggered | Claim meets 3 or more fraud indicator criteria | Claim set to "Fraud Review"; Assessor decision controls locked; Manager alerted | Manager reviews each indicator | Manager clears (returns to assessment) or confirms (SIU referral) |
| EX-FRD-02 | Fraud Confirmed | Manager confirms fraud after review | Claim set to "Referred to SIU"; claim locked from further system processing | Manager records confirmation rationale | SIU takes over; legal proceedings outside system scope |
| EX-FRD-03 | Duplicate Claim | Same policy + same incident date as existing claim | Claim set to "Duplicate Hold"; Manager alerted | Manager reviews original and duplicate | Manager closes duplicate or merges if separate incidents |
| EX-FRD-04 | Staged Accident Suspicion | Incident description inconsistent with damage photos; multiple third-party claimants on same event | Assessor or Manager flags for fraud review manually | Assessor / Manager raises manual flag | Same as EX-FRD-01 resolution |
| EX-FRD-05 | Claimant Identity Mismatch | Name on FNOL does not match PAS policyholder name | Warning flag raised; Agent alerted to verify identity | Agent verifies via ID documents | If genuine → update; if fraudulent → SIU referral |

---

## 5. Technical Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-TEC-01 | PAS API Timeout | PAS response not received within 10 seconds | Retry up to 3 times; on 3rd failure → "Pending Validation" | IT team alerted via monitoring; Agent notified | IT resolves integration; Agent validates manually until resolved |
| EX-TEC-02 | Payment Gateway Failure | PayGW returns non-2xx on disbursement request | Disbursement status set to "Failed"; Finance and Manager alerted | Finance team verifies payment details; retries | Finance retries or uses alternate payment route after verification |
| EX-TEC-03 | Notification Delivery Failure | SMS/email notification fails to deliver | System logs failure; retry after 15 min; on 3rd failure → Agent alerted | Agent contacts customer via phone if notification critical | Agent confirms customer awareness; logs outcome |
| EX-TEC-04 | Document Upload Failure | File upload returns server error | Error message to customer: "Upload failed. Please try again." | None unless persistent | Customer retries; if persistent → IT investigates |
| EX-TEC-05 | Session Timeout During Submission | Customer session expires mid-FNOL | Partial data not submitted; customer returned to login | None | Customer re-logs in; system pre-fills any saved draft data if feasible |
| EX-TEC-06 | Audit Log Write Failure | Audit log entry fails to write for a state transition | Critical system alert to IT | IT investigates immediately | IT resolves; retroactive log entry created with incident note |

---

## 6. Settlement Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-SET-01 | Settlement Amount Disputed by Claimant | Customer disputes settlement amount post-notification | Customer submits dispute via portal (within 15 days) | Manager reviews dispute | Manager upholds or revises settlement with Finance; customer notified |
| EX-SET-02 | Bank Account Details Invalid | PayGW rejects account details (invalid IFSC / account number) | Disbursement fails; Finance alerted | Finance contacts customer for correct details | Customer provides updated details; Finance re-initiates disbursement |
| EX-SET-03 | Finance Override Without Justification | Finance submits override amount without justification text | System blocks submission; error: "Justification is mandatory for settlement overrides." | Finance provides justification | Finance adds rationale; resubmits |
| EX-SET-04 | Depreciation Calculation Error | System returns invalid depreciation (negative or null) | Settlement calculation set to error; Finance alerted | Finance manually calculates and enters | Finance overrides with manual amount and justification |

---

## 7. Customer Exceptions

| Exception ID | Exception | Trigger | System Response | Actor Responsibility | Resolution Path |
|---|---|---|---|---|---|
| EX-CUS-01 | Customer Unresponsive | No document upload or response within 30 days of "Documents Overdue" | Claim set to "Closed — No Response"; customer notified | None required | Customer may re-initiate FNOL (subject to 30-day incident reporting rule) |
| EX-CUS-02 | Dispute Window Lapsed | Customer submits dispute after 15-day window | System rejects dispute; error: "Dispute window has closed." | Agent notified | Agent informs customer; no re-open available via system |
| EX-CUS-03 | Incorrect Contact Details | Notifications bouncing due to wrong email/phone on FNOL | Delivery failures logged; Agent alerted | Agent contacts customer via alternate means | Agent updates contact details; resends notifications |
| EX-CUS-04 | Customer Withdraws Claim | Customer requests claim withdrawal at any stage pre-settlement | Claim set to "Withdrawn" by Agent | Agent confirms withdrawal intent in writing | Claim closed; audit log updated |
