# BPMN Process Model — FNOL (First Notice of Loss)
## InsureFlow — Motor Insurance Claims Management Platform

**Diagram Type:** BPMN 2.0 (Text Notation)  
**Version:** 1.0  
**Author:** Sai Balaji Budumuru

---

## Process Participants (Pools / Lanes)

```
Pool: InsureFlow Claims Process

  Lane 1: Customer
  Lane 2: InsureFlow Claims System
  Lane 3: Policy Administration System (PAS)
  Lane 4: Front-Office Agent
```

---

## FNOL Process Flow

```
==========================================================================
LANE: CUSTOMER
==========================================================================

[Start Event: Incident Occurs]
        |
        v
[Task: Accesses InsureFlow Customer Portal]
        |
        v
[Task: Fills FNOL Form]
  (Policy No., Vehicle Reg., Incident Date, Incident Type,
   Location, Description)
        |
        v
[Gateway (XOR): All mandatory fields complete?]
        |                        |
       YES                       NO
        |                        |
        v                        v
[Task: Submits FNOL Form]  [Task: Reviews validation
                            messages and corrects form]
                                 |
                                 +-----------> [back to Fill FNOL Form]
        |
        v
[Intermediate Event: Receives CRN + Confirmation SMS/Email]
        |
        v
[Task: Uploads mandatory documents per checklist]
        |
        v
[Gateway (XOR): Are all documents uploaded?]
        |                        |
       YES                       NO
        |                        v
        |               [Task: Reviews document checklist
        |                and uploads missing documents]
        |                        |
        |                        +---> [loop back to upload]
        v
[Task: Monitors claim status on portal]
        |
        v
[End Event: FNOL Submitted and Acknowledged]


==========================================================================
LANE: INSUREFLOW CLAIMS SYSTEM
==========================================================================

[Start: Receives FNOL Submission]
        |
        v
[Task: Validates form field rules]
  (Incident date ≤ 30 days, mandatory fields, format checks)
        |
        v
[Gateway (XOR): Validation passed?]
        |                        |
       YES                       NO
        |                        v
        |               [Task: Returns inline error messages to portal]
        |                        |
        |                        v
        |               [End: Returns to customer]
        |
        v
[Task: Generates Claim Reference Number (CRN)]
  Format: CL-YYYY-XXXXXXXX
        |
        v
[Task: Checks for duplicate claim]
  (Same policy number + same incident date)
        |
        v
[Gateway (XOR): Duplicate found?]
        |                        |
       YES                       NO
        |                        v
        v               [Task: Sets claim status to "FNOL Registered"]
[Task: Sets status to
 "Duplicate Hold"]
        |
        v
[Task: Triggers Manager alert]
        |
        v
[Task: Sends customer notification:
 "Claim under preliminary review"]
        |
        v
[End: Duplicate path ends — awaits Manager review]
                                 |
                                 v (continuing main flow)
                        [Task: Sends FNOL Confirmation
                         (SMS + Email with CRN)]
                                 |
                                 v
                        [Task: Initiates Policy Validation
                         API call → PAS Lane]
                                 |
                                 v
                        [Gateway (XOR): PAS available?]
                                 |              |
                                YES             NO
                                 |              v
                                 |    [Task: Set status "Pending Validation"]
                                 |    [Task: Alert Agent to validate manually]
                                 |              |
                                 |              v
                                 |    [End: Manual validation path]
                                 |
                                 v
                        [Task: Receives PAS response]
                                 |
                                 v
                        [Gateway (XOR): Policy active on incident date?]
                                 |              |
                                YES             NO
                                 |              v
                                 |     [Task: Set status "Auto-Rejected
                                 |      — Expired Policy"]
                                 |     [Task: Send rejection notification
                                 |      to customer within 1 BD]
                                 |              |
                                 |              v
                                 |     [End Event: Claim Auto-Rejected]
                                 |
                                 v
                        [Task: Store IDV + policy excess against claim]
                        [Task: Set claim status → "Policy Validated"]
                        [Task: Generate document checklist per claim type]
                        [Task: Notify customer to upload documents]
                                 |
                                 v
                        [Task: Monitor document uploads]
                                 |
                                 v
                        [Gateway (XOR): All mandatory docs received?]
                                 |              |
                                YES             NO (after 5 BD)
                                 |              v
                                 |    [Task: Send reminder on Day 3]
                                 |    [Task: Set status "Documents Overdue"
                                 |     on Day 5; alert Manager]
                                 |
                                 v
                        [Task: Set status → "Pending Agent Verification"]
                        [Task: Assign document review to Agent queue]
                                 |
                                 v
                        [End: Passes to Agent Verification]


==========================================================================
LANE: POLICY ADMINISTRATION SYSTEM (PAS)
==========================================================================

[Start: Receives Policy Verification API Request]
        |
        v
[Task: Looks up policy by policy number]
        |
        v
[Task: Returns policy record:
  - Status (Active/Expired/Cancelled)
  - Policy holder name
  - Vehicle registration number
  - Coverage types
  - Policy start and end dates
  - IDV (Insured Declared Value)
  - Excess amount]
        |
        v
[End: Returns response to InsureFlow Claims System]


==========================================================================
LANE: FRONT-OFFICE AGENT
==========================================================================

[Start: Agent-Assisted FNOL Trigger]
        |
        v
[Task: Receives customer call or in-person visit]
        |
        v
[Task: Verifies customer identity (name + policy number)]
        |
        v
[Task: Enters FNOL data into InsureFlow Agent Portal
  on customer's behalf]
        |
        v
[Task: Notes verbal consent from customer in remarks field]
        |
        v
[End: Submits FNOL → continues in System lane]

[Note: Agent document verification path continues
 from System lane "Pending Agent Verification"]
        |
        v
[Task: Reviews each uploaded document in assessor workspace]
        |
        v
[Gateway (XOR): For each document — readable and correct?]
        |                        |
       YES                       NO
        |                        v
        v               [Task: Mark document "Illegible"
[Task: Mark document     or "Incorrect Document"]
 "Verified"]             [Task: Send re-upload request
                          to customer]
        |
        v
[Gateway (XOR): All documents verified?]
        |
       YES
        v
[Task: Set claim status → "Documents Verified"]
[Task: Trigger assessor assignment in System lane]
        |
        v
[End: Document verification complete]
```

---

## Status Transitions in This Process

| From Status | To Status | Trigger |
|---|---|---|
| (none) | FNOL Registered | Successful FNOL submission |
| (none) | Duplicate Hold | Duplicate detected |
| FNOL Registered | Policy Validated | PAS returns active policy |
| FNOL Registered | Auto-Rejected | PAS returns expired/cancelled policy |
| FNOL Registered | Pending Validation | PAS unavailable |
| Policy Validated | Pending Document Submission | Checklist sent to customer |
| Pending Document Submission | Pending Agent Verification | All docs uploaded |
| Pending Document Submission | Documents Overdue | 5 BD without full upload |
| Pending Agent Verification | Documents Verified | Agent marks all docs verified |

---

## Key Decision Points

1. **Duplicate Check** — prevents parallel processing of fraudulent duplicate claims.
2. **PAS Availability Gateway** — ensures fallback to manual validation; no silent failures.
3. **Policy Active on Incident Date** — the core eligibility gate; uses incident date, not submission date.
4. **Document Completeness** — hard gate before assessment to avoid wasted assessor time.
