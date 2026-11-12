# UAT Test Cases
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026  
**Environment:** UAT  
**Tester Role:** QA Analyst / Business Stakeholder

---

## Test Case Format

Each test case includes:
- **Preconditions:** State of system/data before test.
- **Test Steps:** Numbered, actionable steps.
- **Expected Result:** What the system should do.
- **Pass/Fail Criteria:** Objective measure.
- **Business Rule Ref:** Traceability.

---

## UAT-01: Successful FNOL Submission — Happy Path

**Test Objective:** Verify that a customer can submit a valid FNOL and receive a CRN confirmation.  
**Priority:** Critical  
**BRD Ref:** BR-01, BR-02, BR-03  
**FR Ref:** FR-FNOL-01  

**Preconditions:**
- Customer account exists with valid credentials.
- Policy MOT-2025-00123456 is ACTIVE in PAS (mock/test stub).
- Incident date: within last 10 days.
- PAS API test stub configured to return valid policy.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Log in to InsureFlow customer portal with valid credentials. | Dashboard displayed with "Report a Claim" button. |
| 2 | Click "Report a Claim." | FNOL form displayed with all mandatory fields. |
| 3 | Enter Policy Number: MOT-2025-00123456. | Field accepts input. |
| 4 | Enter Vehicle Reg. No.: TS09AB1234. | Field accepts input. |
| 5 | Select Incident Date: (10 days ago). | Date picker accepts date; no error. |
| 6 | Select Incident Type: Own Damage. | Type selected; document checklist preview updates. |
| 7 | Enter Incident Location: "Near PVNR Flyover, Hyderabad" and Pincode: 500082. | Fields accept input. |
| 8 | Enter Incident Description: 60-character valid description. | Character counter shows within limits. |
| 9 | Click Submit. | Loading indicator displayed. |
| 10 | Observe response. | CRN displayed on screen: format CL-2026-XXXXXXXX. |
| 11 | Check registered email. | Confirmation email received within 60 seconds with CRN, claim summary and document checklist link. |
| 12 | Check registered mobile. | SMS received within 60 seconds with CRN. |
| 13 | Note claim status on portal. | Status displayed: "FNOL Registered." |

**Pass Criteria:**
- CRN generated and displayed within 5 seconds of submission.
- Email and SMS received within 60 seconds.
- Claim status = FNOL Registered.

---

## UAT-02: FNOL with Missing Mandatory Fields

**Test Objective:** Verify that the system validates mandatory fields and prevents incomplete FNOL submission.  
**Priority:** Critical  
**BRD Ref:** BR-02  
**FR Ref:** FR-FNOL-01  

**Preconditions:** Customer logged in; FNOL form open.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Leave Policy Number field blank. | — |
| 2 | Enter all other fields correctly. | — |
| 3 | Click Submit. | Form does NOT submit. |
| 4 | Observe validation messages. | Inline error shown next to Policy Number: "Policy number is required." |
| 5 | Enter a description of only 20 characters (below minimum). | — |
| 6 | Click Submit. | Error shown: "Incident description must be at least 50 characters." |
| 7 | Enter all fields correctly. | — |
| 8 | Click Submit. | FNOL submits successfully; CRN generated. |

**Pass Criteria:**
- System prevents submission with any mandatory field missing.
- Error messages are field-specific and actionable.
- No CRN is generated for invalid submissions.

---

## UAT-03: FNOL with Expired Policy

**Test Objective:** Verify that the system auto-rejects a claim where the policy was expired on the incident date.  
**Priority:** Critical  
**BRD Ref:** BR-04, BR-05  
**Business Rule:** BR-ELIG-01, BR-POL-01  

**Preconditions:**
- PAS stub configured to return policy status = EXPIRED for test policy EXP-TEST-00000001.
- Policy end date in stub: 2025-01-01.
- Test incident date: 2026-10-15 (after policy end date).

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Log in and open FNOL form. | — |
| 2 | Enter Policy Number: EXP-TEST-00000001. | — |
| 3 | Enter a valid incident date (2026-10-15). | — |
| 4 | Complete all other fields and submit. | FNOL accepted; CRN generated. |
| 5 | Observe claim status after < 2 minutes. | Status updated to "Auto-Rejected — Expired Policy." |
| 6 | Check customer email. | Rejection email received within 1 BD: reason = "Policy was not active on the incident date." |
| 7 | Check whether claim can be disputed. | No dispute option displayed; claim is in terminal Auto-Rejected state. |

**Pass Criteria:**
- Claim status = Auto-Rejected within 2 minutes of FNOL.
- Customer notification sent within 1 business day.
- No path to assessment exists for auto-rejected claim.

---

## UAT-04: Claim with Missing Mandatory Documents

**Test Objective:** Verify that assessment cannot begin until all mandatory documents are verified.  
**Priority:** High  
**BRD Ref:** BR-07, BR-08  
**Business Rule:** BR-DOC-01  

**Preconditions:**
- Claim UAT-04-TEST-001 exists in "Policy Validated" status.
- Claim type: Own Damage (5 mandatory documents required).

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Log in as the customer for claim UAT-04-TEST-001. | — |
| 2 | View document checklist. | 5 items listed; all showing "Required." |
| 3 | Upload only 3 of 5 documents. | 3 items show "Uploaded"; 2 still show "Required." |
| 4 | Observe whether claim advances to Agent Verification. | Claim stays in "Pending Document Submission." No advance. |
| 5 | Log in as Agent. | — |
| 6 | Attempt to mark document review complete manually. | Option not available; portal shows "Documents incomplete." |
| 7 | Log in as customer; upload remaining 2 documents. | All 5 show "Uploaded." |
| 8 | Claim status updates to "Pending Agent Verification." | Confirmed. |
| 9 | Log in as Agent; mark all 5 as "Verified." | — |
| 10 | Claim status updates to "Documents Verified." | Confirmed. |
| 11 | Auto-assignment triggers. | Claim enters "Assigned to Assessor" status. |

**Pass Criteria:**
- Claim cannot advance beyond "Pending Document Submission" with incomplete documents.
- Agent cannot bypass the document completeness gate.
- Status updates correctly at each stage.

---

## UAT-05: Claim Approval and Settlement

**Test Objective:** Verify the full assessment → approval → settlement flow.  
**Priority:** Critical  
**BRD Ref:** BR-10 to BR-16  
**Business Rule:** BR-ASS-01–04, BR-SET-01–05  

**Preconditions:**
- Claim UAT-05-TEST-001 is in "Documents Verified" status.
- Policy IDV: ₹6,50,000; Excess: ₹1,000; Vehicle age: 36 months (depreciation: 30%).
- Assessor A001 is a Senior Assessor (IDV > ₹5L); has 5 open claims.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Auto-assignment runs. | Claim assigned to Senior Assessor A001 (IDV > ₹5L check passes). |
| 2 | Log in as Assessor A001; open claim from queue. | Claim moves to "Under Assessment." |
| 3 | Review documents, policy details and IDV. | All data displayed correctly. |
| 4 | Select Decision: Approve. | Settlement entry field appears. |
| 5 | Enter Rationale: 110-character rationale text. | Accepted. |
| 6 | Enter Recommended Settlement: ₹60,000. | — |
| 7 | Enter Settlement > IDV (test: ₹700,000). | Error: "Settlement cannot exceed IDV of ₹6,50,000." |
| 8 | Correct to ₹60,000 and submit. | Decision submitted; claim moves to "Approved." |
| 9 | Verify system-calculated settlement: IDV ₹6,50,000 − Excess ₹1,000 − Depreciation ₹1,94,700 (30% of ₹6,49,000) = ₹4,54,300. Note: assessor entered ₹60,000 (partial). | Settlement record created; calculated settlement shown. |
| 10 | Log in as Finance user; review and approve settlement. | Status → "Settlement Approved." |
| 11 | Disbursement initiated via PayGW stub. | Status → "Settled"; UTR number recorded. |
| 12 | Customer receives settlement SMS and email. | Received within 60 seconds of PayGW confirmation. |

**Pass Criteria:**
- Senior Assessor assignment enforced for high-IDV claim.
- Settlement > IDV blocked.
- Full status progression: Approved → Pending Settlement → Settlement Approved → Settled.
- Customer notification sent on settlement.

---

## UAT-06: Claim Rejection and Customer Dispute

**Test Objective:** Verify that a rejected claim can be disputed within 15 days and re-enters assessment.  
**Priority:** High  
**BRD Ref:** BR-13, BR-16  
**Business Rule:** BR-ASS-04, FR-DEC-02, FR-DEC-04  

**Preconditions:**
- Claim UAT-06-TEST-001 is in "Documents Verified" status; assigned to Assessor A002.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Log in as Assessor A002; open claim. | Claim in "Under Assessment." |
| 2 | Select Decision: Reject. | Rejection reason field appears. |
| 3 | Attempt to submit without rationale. | Blocked: "Rationale is required (minimum 100 characters)." |
| 4 | Enter 120-character rejection rationale; select reason: Coverage Exclusion. | Accepted. |
| 5 | Submit rejection. | Claim status → "Rejected." |
| 6 | Customer receives rejection email with reason and 15-day dispute window notice. | Confirmed. |
| 7 | Log in as customer within 15 days; click "Dispute This Decision." | Dispute form displayed. |
| 8 | Upload new supporting evidence; enter dispute reason. | — |
| 9 | Submit dispute. | Claim status → "Re-opened." |
| 10 | Claim is auto-assigned to a Senior Assessor (not A002). | Confirmed: different Senior Assessor assigned. |
| 11 | Attempt to submit dispute after 15 days (simulate). | Error: "Dispute window has closed for this claim." |

**Pass Criteria:**
- Rejection requires mandatory rationale; submission blocked without it.
- Customer dispute available for 15 days post-rejection.
- Re-opened claim assigned to Senior Assessor; original assessor cannot re-take it.
- Late dispute rejected with clear error message.

---

## UAT-07: Incident Date Outside 30-Day Reporting Window

**Test Objective:** Verify that FNOL is blocked when incident date is more than 30 days before submission.  
**Priority:** High  
**BRD Ref:** BR-02  
**Business Rule:** BR-ELIG-02  

**Preconditions:** Customer logged in; FNOL form open.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Enter incident date = 45 days ago. | — |
| 2 | Complete all other fields. | — |
| 3 | Submit. | Error returned: "Incident date exceeds the 30-day reporting window. Claims must be reported within 30 days of the incident." |
| 4 | No CRN generated. | Confirmed. |
| 5 | Change incident date to 29 days ago and resubmit. | FNOL accepted; CRN generated. |

**Pass Criteria:**
- Dates > 30 days prior are rejected at submission.
- Error message is clear and specific.

---

## UAT-08: Duplicate Claim Detection

**Test Objective:** Verify that a duplicate claim (same policy, same incident date) is detected and held.  
**Priority:** High  
**BRD Ref:** BR-06  
**Business Rule:** BR-FRAU-05  

**Preconditions:**
- Claim UAT-08-ORIG already exists for policy MOT-2025-00123456 with incident date 2026-10-10.

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | Log in as customer and submit a new FNOL with same policy + same incident date. | — |
| 2 | Observe response. | 409 Conflict returned; message: "A claim has already been registered for this policy and incident date. Your submission is under preliminary review." |
| 3 | Note the claim status of the new submission. | Status = "Duplicate Hold." |
| 4 | Customer receives notification. | Notification: "Your claim is under preliminary review for verification." |
| 5 | Log in as Claims Manager; view Duplicate Hold queue. | New claim appears with link to original. |
| 6 | Manager marks as "Not a Duplicate" (different incident on same day). | Claim advances to "Policy Validated." |
| 7 | Manager marks a second duplicate test as "Confirmed Duplicate." | Claim status → "Rejected." Customer notified. |

**Pass Criteria:**
- Duplicate correctly detected and placed on hold.
- Manager receives alert and can action the hold.
- Both resolution paths (clear and reject) work correctly.

---

## UAT-09: Settlement Calculation Validation

**Test Objective:** Verify settlement calculation applies correct depreciation and respects IDV cap.  
**Priority:** Critical  
**Business Rule:** BR-SET-01, BR-SET-02, BR-SET-03  

**Test Data:**

| Scenario | IDV | Excess | Vehicle Age | Expected Depreciation | Expected Settlement |
|---|---|---|---|---|---|
| A | ₹2,00,000 | ₹1,500 | 8 months | 15% = ₹29,775 | ₹1,68,725 |
| B | ₹5,00,000 | ₹2,000 | 30 months | 30% = ₹1,49,400 | ₹3,48,600 |
| C | ₹1,00,000 | ₹500 | 18 months | 20% = ₹19,900 | ₹79,600 |
| D (IDV cap test) | ₹3,00,000 | ₹1,000 | 48 months | 40% = ₹1,19,600 | ₹1,79,400 |

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | For Scenario A: approve a claim; observe system-calculated settlement. | ₹1,68,725 displayed. |
| 2 | Repeat for Scenarios B, C, D. | Each matches expected value. |
| 3 | Finance attempts override above IDV for Scenario D. | Blocked: "Settlement cannot exceed IDV of ₹3,00,000." |
| 4 | Finance enters override below IDV with justification. | Accepted; override recorded in audit log with Finance user ID. |

**Pass Criteria:**
- Each scenario produces the mathematically correct settlement.
- Override above IDV is blocked.
- Override below IDV requires and records justification.

---

## UAT-10: Fraud Flag Triggered and Escalated

**Test Objective:** Verify that a claim meeting 3+ fraud criteria is flagged, assessor controls locked, and Manager is alerted.  
**Priority:** High  
**Business Rule:** BR-FRAU-01, BR-FRAU-02, BR-ASS-05  

**Preconditions:**
- Claim UAT-10-TEST-001 configured in test data with:
  - Incident reported 20 days after occurrence (Indicator 1).
  - Policy purchased 15 days before incident (Indicator 2).
  - Previous claim on same vehicle 8 months ago (Indicator 3).

**Test Steps:**

| # | Step | Expected Result |
|---|---|---|
| 1 | FNOL submitted and policy validated normally. | — |
| 2 | Documents verified and claim assigned to Assessor. | — |
| 3 | Assessor opens claim. | System evaluates fraud indicators in background. |
| 4 | Observe claim status. | Status changes to "Fraud Review." Alert sent to Manager. |
| 5 | Assessor attempts to click "Approve" or "Reject." | Buttons are hidden / disabled. Message: "This claim requires Manager review." |
| 6 | Log in as Claims Manager; view fraud alert. | Alert shows 3 triggered indicators with details. |
| 7 | Manager selects "Clear — Not Fraud" and enters rationale. | Claim returned to "Under Assessment"; re-assigned to Senior Assessor. |
| 8 | Repeat test; Manager selects "Confirm Fraud." | Claim status → "Referred to SIU." Claim locked from further processing. |

**Pass Criteria:**
- Fraud indicators correctly evaluated after claim enters Under Assessment.
- Assessor decision controls locked on Fraud Review status.
- Manager alert contains indicator details.
- Both Manager paths (clear and confirm) function correctly.

---

## UAT Summary

| UAT ID | Scenario | Priority | Expected Outcome | Linked BRD | Status |
|---|---|---|---|---|---|
| UAT-01 | Successful FNOL submission | Critical | CRN + notifications | BR-01–03 | To Test |
| UAT-02 | Missing mandatory fields | Critical | Validation errors; no CRN | BR-02 | To Test |
| UAT-03 | Expired policy | Critical | Auto-rejection | BR-04, BR-05 | To Test |
| UAT-04 | Missing documents | High | Assessment blocked | BR-07, BR-08 | To Test |
| UAT-05 | Full approval and settlement | Critical | Settled status; payment sent | BR-10–16 | To Test |
| UAT-06 | Rejection + dispute | High | Re-open to Senior Assessor | BR-13, BR-16 | To Test |
| UAT-07 | Incident date > 30 days | High | FNOL blocked | BR-02 | To Test |
| UAT-08 | Duplicate claim | High | Duplicate Hold + Manager alert | BR-06 | To Test |
| UAT-09 | Settlement calculation | Critical | Correct formula; IDV cap | BR-14 | To Test |
| UAT-10 | Fraud flag and escalation | High | Fraud Review; assessor locked | BR-19 | To Test |
