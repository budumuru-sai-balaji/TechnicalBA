# Wireframe Descriptions — Prototype
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026  
**Type:** Lo-fidelity wireframe descriptions (text-based)

> These are structured wireframe descriptions for a developer or UI designer to implement. Each screen includes layout, components, interactions and design notes.

---

## Screen 1: Customer FNOL Submission Form

**Route:** `/portal/claims/new`  
**Actor:** Customer  
**Purpose:** Capture all FNOL data in a single multi-section form.

---

### Layout

```
┌─────────────────────────────────────────────────────────────────┐
│  [InsureFlow Logo]            [My Claims]  [Profile]  [Sign Out]│
│─────────────────────────────────────────────────────────────────│
│                                                                 │
│  Report a Claim                                                 │
│  ───────────────────────────────────────                        │
│  Step 1 of 3: Incident Details  ●──○──○                         │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  POLICY INFORMATION                                      │   │
│  │                                                          │   │
│  │  Policy Number *          Vehicle Reg. No. *             │   │
│  │  [__________________]     [__________________]           │   │
│  │                                                          │   │
│  │  Incident Date *                                         │   │
│  │  [Date Picker ▼]                                         │   │
│  │  ⚠ Must be within 30 days of today.                      │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  INCIDENT DETAILS                                        │   │
│  │                                                          │   │
│  │  Incident Type *                                         │   │
│  │  ○ Own Damage  ○ Theft  ○ Third-Party Liability          │   │
│  │                                                          │   │
│  │  Incident Location *                   Pincode *         │   │
│  │  [________________________________]    [______]           │   │
│  │                                                          │   │
│  │  FIR Number (required for Theft)                         │   │
│  │  [__________________]                                    │   │
│  │                                                          │   │
│  │  Description of Incident *  (min. 50 characters)         │   │
│  │  ┌───────────────────────────────────────────────────┐   │   │
│  │  │                                                   │   │   │
│  │  │                                                   │   │   │
│  │  └───────────────────────────────────────────────────┘   │   │
│  │  Characters entered: 0 / 50 minimum                      │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  [Cancel]                              [Next: Review Details →] │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Components

| Component | Type | Behaviour |
|---|---|---|
| Policy Number | Text input | Uppercase; max 20 chars; validated on Next |
| Vehicle Reg. | Text input | Uppercase; format validated on Next |
| Incident Date | Date picker | Max = today; min = today − 30 days |
| Incident Type | Radio group | Selecting "Theft" shows FIR Number field |
| FIR Number | Text input | Visible and required only if Theft selected |
| Description | Textarea | Character counter; min 50 enforced on Next |
| Next button | Button | Triggers field validation; proceeds to Step 2 |

### Interactions

- Selecting "Theft" dynamically shows the FIR Number field.
- Inline validation on blur for each field.
- "Next" button disabled until all required fields pass validation.

---

### Step 2: Review and Submit

```
┌─────────────────────────────────────────────────────────────────┐
│  Report a Claim                                                 │
│  Step 2 of 3: Review Your Details  ○──●──○                     │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  REVIEW DETAILS                           [Edit]         │   │
│  │                                                          │   │
│  │  Policy Number      MOT-2025-00123456                    │   │
│  │  Vehicle Reg. No.   TS09AB1234                           │   │
│  │  Incident Date      15 Oct 2026                          │   │
│  │  Incident Type      Own Damage                           │   │
│  │  Location           Near PVNR Flyover, Hyderabad 500082  │   │
│  │  Description        [first 100 characters of text...]   │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  □ I confirm the details above are accurate and I am the       │
│    registered policyholder or authorised representative.        │
│                                                                 │
│  [← Back]                              [Submit Claim →]        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Step 3: Confirmation

```
┌─────────────────────────────────────────────────────────────────┐
│  Report a Claim                                                 │
│  Step 3 of 3: Claim Registered  ○──○──●                        │
│                                                                 │
│           ✅  Claim Registered Successfully                     │
│                                                                 │
│           Your Claim Reference Number                           │
│           ┌──────────────────────────────┐                     │
│           │   CL-2026-00090001           │  [Copy]             │
│           └──────────────────────────────┘                     │
│                                                                 │
│  Confirmation sent to: r*****@email.com and +91-98765-XXXXX    │
│                                                                 │
│  NEXT STEPS                                                     │
│  ─────────────────────────────────────────                      │
│  Please upload the following documents within 5 business days: │
│                                                                 │
│  □  Duly filled claim form                                      │
│  □  RC copy                                                     │
│  □  Driving licence copy                                        │
│  □  Repair estimate (registered workshop)                       │
│  □  Photographs of damage (minimum 3)                           │
│                                                                 │
│  [Upload Documents Now →]       [Go to My Claims]              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Screen 2: Customer — My Claims Dashboard

**Route:** `/portal/claims`  
**Actor:** Customer

```
┌─────────────────────────────────────────────────────────────────┐
│  [InsureFlow Logo]            [Report a Claim]  [Profile] [Out] │
│─────────────────────────────────────────────────────────────────│
│                                                                 │
│  My Claims                                                      │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2026-00090001                    Status: Under Review│   │
│  │  Own Damage · Policy: MOT-2025-00123456                  │   │
│  │  Incident: 15 Oct 2026                                   │   │
│  │  Submitted: 16 Oct 2026                                  │   │
│  │                                                          │   │
│  │  Documents: ████████████ 5/5 Verified ✅                │   │
│  │                                                          │   │
│  │  [View Details →]                                        │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2025-00071234                    Status: Settled ✅  │   │
│  │  Own Damage · Policy: MOT-2024-00098765                  │   │
│  │  Settled: 10 Mar 2025 · Amount: ₹32,500                 │   │
│  │  [View Details →]                                        │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Screen 3: Customer — Claim Detail and Status Timeline

**Route:** `/portal/claims/{claimRefNo}`

```
┌─────────────────────────────────────────────────────────────────┐
│  ← My Claims                                                    │
│                                                                 │
│  Claim CL-2026-00090001                                         │
│  Own Damage · Submitted 16 Oct 2026                             │
│                                                                 │
│  ┌────────────────────────────────┐                             │
│  │ STATUS: Under Assessment 🔍   │                             │
│  │ Estimated resolution: 26 Oct  │                             │
│  └────────────────────────────────┘                             │
│                                                                 │
│  CLAIM TIMELINE                                                 │
│  ──────────────────────────────────                             │
│  ✅  Claim Registered          16 Oct, 11:00                   │
│  ✅  Policy Validated          16 Oct, 11:01                   │
│  ✅  Documents Submitted        17 Oct, 10:15                  │
│  ✅  Documents Verified         18 Oct, 14:00                  │
│  ✅  Assigned to Assessor       19 Oct, 09:00                  │
│  🔄  Under Assessment          19 Oct, 10:30                   │
│  ○   Decision                  Pending                         │
│  ○   Settlement                Pending                         │
│                                                                 │
│  DOCUMENTS                                                      │
│  ──────────────────────────────────                             │
│  ✅  Claim Form                 Verified                        │
│  ✅  RC Copy                    Verified                        │
│  ✅  Driving Licence            Verified                        │
│  ✅  Repair Estimate            Verified                        │
│  ✅  Damage Photographs (3)     Verified                        │
│                                                                 │
│  Have a question? [Contact Support]                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Screen 4: Assessor Dashboard

**Route:** `/assessor/dashboard`  
**Actor:** Assessor / Senior Assessor

```
┌─────────────────────────────────────────────────────────────────┐
│  InsureFlow — Assessor Portal     [Priya Nair] [Sign Out]       │
│─────────────────────────────────────────────────────────────────│
│                                                                 │
│  DASHBOARD SUMMARY                                              │
│  ─────────────────────────────────────────────────────────      │
│  Open Claims: 7    Due Today: 2    SLA Breach Risk: 1    ⚠      │
│                                                                 │
│  MY CLAIM QUEUE                              [Filter ▼] [Sort ▼]│
│  ──────────────────────────────────────────────────────          │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2026-00090001                         ⚠ DUE TODAY   │   │
│  │  Own Damage · IDV: ₹6,50,000              SENIOR        │   │
│  │  Assigned: 19 Oct · SLA: 24 Oct                         │   │
│  │  Documents: 5/5 Verified                                 │   │
│  │  [Open Claim →]                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2026-00089875                         On Track       │   │
│  │  Theft · IDV: ₹2,10,000                                 │   │
│  │  Assigned: 20 Oct · SLA: 25 Oct                         │   │
│  │  Documents: 6/6 Verified                                 │   │
│  │  [Open Claim →]                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Screen 5: Assessor — Claim Assessment Workspace

**Route:** `/assessor/claims/{claimId}`

```
┌─────────────────────────────────────────────────────────────────┐
│  ← Dashboard                            CL-2026-00090001        │
│─────────────────────────────────────────────────────────────────│
│  ┌────────────────────┐  ┌─────────────────────────────────┐   │
│  │  CLAIM SUMMARY     │  │  DOCUMENT VIEWER                 │   │
│  │                    │  │                                  │   │
│  │  Type: Own Damage  │  │  [1. Claim Form]  ← active       │   │
│  │  Date: 15 Oct 2026 │  │  [2. RC Copy]                    │   │
│  │  IDV:  ₹6,50,000  │  │  [3. Driving Lic.]               │   │
│  │  Excess: ₹1,000   │  │  [4. Repair Est.]                │   │
│  │  Policy: COMP      │  │  [5. Photos (3)]                 │   │
│  │  Coverage: OD+TP   │  │                                  │   │
│  │  Vehicle: 2021     │  │  ┌─────────────────────────────┐ │   │
│  │   Swift Dzire      │  │  │  [PDF / Image Preview Area] │ │   │
│  │  TS09AB1234        │  │  │                             │ │   │
│  │                    │  │  │                             │ │   │
│  │  CUSTOMER          │  │  └─────────────────────────────┘ │   │
│  │  Rajesh K. Sharma  │  │                                  │   │
│  │  +91-9876543210    │  │                                  │   │
│  └────────────────────┘  └─────────────────────────────────┘   │
│                                                                 │
│  ASSESSMENT DECISION                                            │
│  ──────────────────────────────────────────────────────────     │
│  Coverage Finding *                                             │
│  ○ Covered   ○ Not Covered   ○ Partially Covered                │
│                                                                 │
│  Damage Findings *                                              │
│  [______________________________________________________]       │
│                                                                 │
│  Rationale * (minimum 100 characters)                           │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Recommended Settlement Amount (₹) *                            │
│  [__________________]                                           │
│                                                                 │
│  Fraud Indicators Detected: ⚠ 2 of 5  (threshold: 3)          │
│                                                                 │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐   │
│  │   ✅ Approve   │  │   ❌ Reject    │  │  ⬆ Escalate   │   │
│  └────────────────┘  └────────────────┘  └────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Notes:**
- If fraud indicator count reaches 3+, Approve and Reject buttons are disabled and replaced with: "⚠ This claim has been flagged for Fraud Review. Decision authority has been transferred to the Claims Manager."
- Settlement field validates in real time against IDV.
- Rationale character counter shown beneath the textarea.

---

## Screen 6: Claims Manager Dashboard

**Route:** `/manager/dashboard`  
**Actor:** Claims Manager

```
┌─────────────────────────────────────────────────────────────────┐
│  InsureFlow — Manager Portal         [Vikram S.]  [Sign Out]    │
│─────────────────────────────────────────────────────────────────│
│                                                                 │
│  OVERVIEW                                                       │
│  ──────────────────────────────────────────────────────         │
│  Total Open: 142   SLA Breach: 4 ⚠   Escalated: 3   Fraud: 1   │
│                                                                 │
│  ESCALATED CLAIMS                        [View All →]           │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2026-00088001  ·  Escalated by: Assessor A003       │   │
│  │  Ambiguous coverage — Third-Party claim, disputed extent │   │
│  │  Waiting: 1 day 4 hrs  ·  SLA: 2 BD  ⚠ Approaching      │   │
│  │  [Review →]                                              │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  FRAUD REVIEW QUEUE                      [View All →]           │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  CL-2026-00090050  ·  Indicators: 3                     │   │
│  │  (1) Reported 18 days late  (2) Policy < 30 days old    │   │
│  │  (3) Previous claim 8 months ago                        │   │
│  │  [Review →]                                              │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  SLA BREACH ALERTS                                              │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  ⚠ CL-2026-00089200 — Assessment SLA breached (5 BD)    │   │
│  │  Assessor: A007 — Last activity: 3 days ago              │   │
│  │  [Reassign →]  [Contact Assessor →]                     │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ASSESSOR WORKLOAD                       [Manage Team →]        │
│  ─────────────────────────────────────────────────────          │
│  A001 Priya Nair      ████████░░  8 / 10 claims                 │
│  A002 Ramesh D.       █████░░░░░  5 / 10 claims                 │
│  A003 Meena S.        ██░░░░░░░░  2 / 10 claims                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Design Notes

| Principle | Implementation |
|---|---|
| Status colour coding | FNOL Registered: Blue; Under Assessment: Orange; Approved: Green; Rejected: Red; Fraud Review: Purple; Settled: Teal |
| SLA urgency indicators | Green (> 50% time remaining); Yellow (< 25% remaining); Red (breached) |
| Role-based visibility | Customer: sees own claims and status only. Agent: sees all claims in document stage. Assessor: sees own queue only. Manager: sees all queues + SLA dashboard. |
| Mobile responsiveness | Customer portal must be mobile-friendly (responsive layout). Assessor and Manager portals are desktop-primary. |
| Accessibility | All form fields have labels; error messages are inline and colour + icon indicated (not colour alone). |
