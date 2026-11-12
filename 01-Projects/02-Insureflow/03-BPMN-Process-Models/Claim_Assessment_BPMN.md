# BPMN Process Model — Claim Assessment, Decision and Settlement
## InsureFlow — Motor Insurance Claims Management Platform

**Diagram Type:** BPMN 2.0 (Text Notation)  
**Version:** 1.0  
**Author:** Sai Balaji Budumuru

---

## Process Trigger
This process begins when the claim status reaches **"Documents Verified"** (output of the FNOL Process BPMN).

---

## Process Participants

```
Pool: InsureFlow Claims Process — Assessment to Settlement

  Lane 1: InsureFlow Claims System
  Lane 2: Claims Assessor / Senior Assessor
  Lane 3: Claims Manager
  Lane 4: Finance / Settlement Team
  Lane 5: Payment Gateway
  Lane 6: Customer
```

---

## Assessment to Settlement Process Flow

```
==========================================================================
LANE: INSUREFLOW CLAIMS SYSTEM
==========================================================================

[Start Event: Claim status = "Documents Verified"]
        |
        v
[Task: Evaluate assignment criteria]
  - Retrieve claim type, IDV, assessor queue sizes
  - Apply assignment rules (specialisation, IDV threshold, queue < 10)
        |
        v
[Gateway (XOR): IDV > ₹5,00,000?]
        |                        |
       YES                       NO
        v                        v
[Task: Filter to          [Task: Filter to any
 Senior Assessors]         eligible Assessor]
        |                        |
        +------------------------+
        |
        v
[Gateway (XOR): Eligible assessor available within 2 hrs?]
        |                        |
       YES                       NO
        |                        v
        |               [Task: Alert Claims Manager —
        |                no eligible assessor available]
        |               [Task: Manager manually assigns]
        |                        |
        +------------------------+
        |
        v
[Task: Set claim status → "Assigned to Assessor"]
[Task: Send claim to Assessor workspace]
[Task: Notify customer: "Claim under assessment"]
        |
        v
[Task: Monitor fraud indicators continuously]
        |
        v
[Gateway (XOR): 3 or more fraud indicators triggered?]
        |                        |
       YES                       NO
        |                        v
        v               [Continue assessment — Assessor lane]
[Task: Set claim status → "Fraud Review"]
[Task: Alert Claims Manager with indicator details]
[Task: Lock Approve/Reject buttons for Assessor]
        |
        v
[End: Passes to Manager lane — Fraud Review]


==========================================================================
LANE: CLAIMS ASSESSOR / SENIOR ASSESSOR
==========================================================================

[Start: Claim appears in assessor queue]
        |
        v
[Task: Reviews claim summary, documents, policy details, IDV]
        |
        v
[Task: Assesses coverage:
  - Is incident type covered under the policy?
  - Are there applicable exclusions?
  - Is damage consistent with reported incident?]
        |
        v
[Gateway (XOR): Assessment complete?]
  (If more information needed → Task: Request additional info from Customer)
        |
       YES
        v
[Gateway (XOR): Decision?]
       /            |            \
   APPROVE       REJECT        ESCALATE
     |              |              |
     v              v              v
[Task: Enter   [Task: Enter   [Task: Enter
 approval       rejection      escalation
 findings +     reason +       reason +
 settlement     notification   supporting
 amount]        letter text]   notes]
     |              |              |
     v              v              v
[Task: Submit  [Task: Submit  [Task: Transfer
 to System]     to System]     claim to
                               Manager queue]
     |              |
     +------+--------
            |
            v
[End: Returns to System lane]


==========================================================================
LANE: CLAIMS MANAGER
==========================================================================

[Start: Receives escalated or fraud-flagged claim]
        |
        v
[Gateway (XOR): Claim type?]
       /                    \
  ESCALATION             FRAUD REVIEW
     |                        |
     v                        v
[Task: Reviews        [Task: Reviews fraud
 assessor notes        indicators, policy
 and coverage          history, claim
 question]             documents]
     |                        |
     v                        v
[Gateway (XOR):       [Gateway (XOR):
 Decision?]            Fraud confirmed?]
   /      \               /         \
 APPROVE  REJECT      YES (refer)   NO (clear)
   |        |             |              |
   v        v             v              v
[Approve] [Reject]  [Task: Refer to  [Task: Return
                     Special          claim to
                     Investigation    Assessor or
                     Unit]            Approve/Reject]
   |        |
   +--+-----+
      |
      v
[Task: Record decision with mandatory rationale]
[Task: Set claim status → Approved or Rejected]
      |
      v
[End: Returns to System lane]


==========================================================================
LANE: CLAIMS SYSTEM (Decision Processing)
==========================================================================

[Start: Receives decision from Assessor or Manager]
        |
        v
[Gateway (XOR): Decision type?]
       /           |           \
  APPROVED      REJECTED     ESCALATED
     |              |             |
     v              v             v
[Task: Set     [Task: Set    [Task: Set
 status →       status →      status →
 "Approved"]    "Rejected"]   "Escalated"]
     |              |
     v              v
[Task: Notify  [Task: Notify
 customer:      customer:
 "Claim         rejection reason
 Approved"]     + dispute rights
     |           (15-day window)]
     v              |
[Task: Trigger      v
 settlement    [End: Rejected path]
 calculation]
     |
     v
[Task: Calculate settlement:
  Settlement = IDV − Excess − Depreciation]
[Task: Store calculated amount]
[Task: Set status → "Pending Settlement"]
[Task: Route to Finance queue]


==========================================================================
LANE: FINANCE / SETTLEMENT TEAM
==========================================================================

[Start: Receives claim in Finance queue]
        |
        v
[Task: Reviews settlement calculation]
  (IDV, excess, depreciation, recommended amount)
        |
        v
[Gateway (XOR): Override required?]
        |                        |
       YES                       NO
        v                        |
[Task: Enter override         [Task: Approve
 amount with mandatory         settlement as
 justification]                calculated]
        |                        |
        +------------------------+
        |
        v
[Task: Set status → "Settlement Approved"]
[Task: Trigger disbursement via Payment Gateway]
        |
        v
[End: Passes to Payment Gateway lane]


==========================================================================
LANE: PAYMENT GATEWAY
==========================================================================

[Start: Receives disbursement instruction]
        |
        v
[Task: Validates bank account details of claimant]
        |
        v
[Task: Initiates bank transfer (NEFT/IMPS)]
        |
        v
[Gateway (XOR): Transfer successful?]
        |                        |
       YES                       NO
        v                        v
[Task: Returns         [Task: Returns failure
 success + UTR          notification with reason]
 reference]            [Task: Finance team
        |               retries or contacts
        v               claimant to verify details]
[Task: System updates claim status → "Settled"]
[Task: Sends settlement confirmation
 (amount + UTR) to customer via SMS + Email]
        |
        v
[End Event: Claim lifecycle complete — status "Settled"]


==========================================================================
LANE: CUSTOMER
==========================================================================

[Intermediate: Receives "Claim under assessment" notification]
        |
        v
[Task: Monitors claim status on portal]
        |
        v
[Gateway (XOR): Receives decision notification]
       /                    \
  APPROVED                REJECTED
     |                        |
     v                        v
[Task: Monitors        [Gateway (XOR):
 disbursement           Wishes to dispute?]
 status]                     |          |
     |                      YES         NO
     v                       v          v
[Task: Receives       [Task: Submits   [End:
 settlement            dispute within  Customer
 confirmation]         15 days with    accepts
     |                 new evidence]   rejection]
     v                       |
[End Event:           [Task: Claim
 Satisfied]            re-opened →
                       Senior Assessor]
```

---

## Status Transitions in This Process

| From | To | Trigger |
|---|---|---|
| Documents Verified | Assigned to Assessor | Auto-assignment |
| Assigned to Assessor | Fraud Review | 3+ fraud indicators |
| Assigned to Assessor | Under Assessment | Assessor opens claim |
| Under Assessment | Approved | Assessor approves |
| Under Assessment | Rejected | Assessor rejects |
| Under Assessment | Escalated | Assessor escalates |
| Escalated | Approved | Manager approves |
| Escalated | Rejected | Manager rejects |
| Fraud Review | Referred to SIU | Manager confirms fraud |
| Fraud Review | Approved / Rejected | Manager clears and decides |
| Approved | Pending Settlement | System triggers settlement |
| Pending Settlement | Settlement Approved | Finance approves |
| Settlement Approved | Settled | Payment Gateway confirms |
| Rejected | Re-opened | Customer disputes within 15 days |
