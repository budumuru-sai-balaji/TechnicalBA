# Entity Relationship Diagram (ERD)
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

---

## Entities Overview

| Entity | Description |
|---|---|
| Customer | Policyholder / claimant registering and tracking claims |
| Policy | Motor insurance policy held by a customer (sourced from PAS) |
| Vehicle | Vehicle covered under a policy |
| Claim | A claim registered against a policy for a specific incident |
| FNOL | First Notice of Loss — the initial claim registration record |
| Document | Supporting document uploaded against a claim |
| Assessor | Internal staff member responsible for claim assessment |
| Assessment | Assessment record created by an assessor for a claim |
| Decision | Decision record (Approved / Rejected / Escalated) on a claim |
| Settlement | Financial settlement record for an approved claim |
| Notification | Record of all communications sent to the customer |
| AuditLog | Immutable record of all state transitions on a claim |
| FraudFlag | Fraud indicator records against a claim |
| ClaimStatus | Reference table of valid claim statuses |

---

## ERD (Text / Notation Format)

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                         InsureFlow — Entity Relationship Diagram                 │
└──────────────────────────────────────────────────────────────────────────────────┘

┌─────────────┐          ┌─────────────┐           ┌───────────────┐
│  CUSTOMER   │          │   POLICY    │           │    VEHICLE    │
│─────────────│          │─────────────│           │───────────────│
│ CustomerID  │1        N│ PolicyID    │1         1│ VehicleID     │
│ FullName    ├──────────┤ CustomerID  ├───────────┤ PolicyID      │
│ DateOfBirth │          │ PolicyNumber│           │ RegistrationNo│
│ Email       │          │ PolicyType  │           │ Make          │
│ PhoneNumber │          │ StartDate   │           │ Model         │
│ Address     │          │ EndDate     │           │ ManufactureYear│
│ IDProofType │          │ Status      │           │ FuelType      │
│ IDProofNo   │          │ IDV         │           │ BodyType      │
│ BankAccount │          │ Excess      │           │ EngineNo      │
│ IFSC        │          │ CoverageType│           │ ChassisNo     │
│ CreatedAt   │          │ PremiumAmt  │           │ ColourCode    │
└─────────────┘          │ SyncedAt    │           └───────────────┘
                         └──────┬──────┘
                                │1
                                │
                               N│
                         ┌──────┴──────┐
                         │    CLAIM    │
                         │─────────────│
                         │ ClaimID     │
                         │ PolicyID    │
                         │ CustomerID  │
                         │ FNOLID      │
                         │ ClaimRefNo  │
                         │ IncidentDate│
                         │ IncidentType│
                         │ IncidentLoc │
                         │ Description │
                         │ StatusID    │
                         │ DuplicateOf │ (self-ref, nullable)
                         │ FraudScore  │
                         │ CreatedAt   │
                         │ UpdatedAt   │
                         └──────┬──────┘
                                │1
              ┌─────────────────┼─────────────────────┐
              │1                │1                    │1
      ┌───────┴──────┐  ┌───────┴──────┐    ┌────────┴───────┐
      │     FNOL     │  │   DOCUMENT   │    │   FRAUD FLAG   │
      │──────────────│  │──────────────│    │────────────────│
      │ FNOLID       │  │ DocumentID   │    │ FraudFlagID    │
      │ ClaimID      │  │ ClaimID      │    │ ClaimID        │
      │ SubmittedBy  │  │ DocumentType │    │ IndicatorCode  │
      │ SubmissionCh │  │ FileName     │    │ IndicatorDesc  │
      │ FIRNumber    │  │ FileSize     │    │ DetectedAt     │
      │ AssistingAgent│  │ FileFormat   │    │ DetectedBy     │
      │ SubmittedAt  │  │ StoragePath  │    │ (System/Manual)│
      └──────────────┘  │ UploadedAt   │    └────────────────┘
                        │ VerifiedBy   │
                        │ VerifStatus  │
                        │ VerifiedAt   │
                        │ RejectReason │
                        └──────────────┘

                                │1 (from CLAIM)
                                │
                               N│
                       ┌────────┴───────┐
                       │   ASSESSMENT   │
                       │────────────────│
                       │ AssessmentID   │
                       │ ClaimID        │
                       │ AssessorID     │
                       │ AssignedAt     │
                       │ StartedAt      │
                       │ CompletedAt    │
                       │ CoverageFinding│
                       │ DamageFindings │
                       │ Rationale      │◄──── min 100 chars
                       │ RecSettlement  │
                       │ AssessmentType │
                       │   (Initial /   │
                       │    Re-open)    │
                       └────────┬───────┘
                                │1
                                │
                               1│
                       ┌────────┴───────┐         ┌──────────────┐
                       │   DECISION     │         │   ASSESSOR   │
                       │────────────────│         │──────────────│
                       │ DecisionID     │N       1│ AssessorID   │
                       │ AssessmentID   ├─────────┤ FullName     │
                       │ ClaimID        │         │ Email        │
                       │ DecisionType   │         │ Role         │
                       │  (Approved /   │         │ Specialisation│
                       │   Rejected /   │         │ OpenClaimCount│
                       │   Escalated)   │         │ IsActive     │
                       │ MadeBy         │         └──────────────┘
                       │ MadeByRole     │
                       │ Rationale      │
                       │ DecidedAt      │
                       └────────┬───────┘
                                │
                    ┌───────────┘
                   1│ (if Approved)
                    │
                   1│
           ┌────────┴──────┐
           │  SETTLEMENT   │
           │───────────────│
           │ SettlementID  │
           │ ClaimID       │
           │ DecisionID    │
           │ IDV           │
           │ PolicyExcess  │
           │ DepreciationPct│
           │ DepreciationAmt│
           │ CalcSettlement │
           │ FinalSettlement│
           │ OverrideBy    │
           │ OverrideReason│
           │ FinanceApprBy │
           │ FinanceApprAt │
           │ DisbursedAt   │
           │ UTRNO         │
           │ PaymentStatus │
           └───────────────┘

                                │1 (from CLAIM)
                                │
                               N│
                       ┌────────┴───────┐
                       │  AUDIT LOG     │
                       │────────────────│
                       │ LogID          │
                       │ ClaimID        │
                       │ FromStatus     │
                       │ ToStatus       │
                       │ ChangedBy      │
                       │ ChangedByRole  │
                       │ ChangeReason   │
                       │ ChangedAt      │
                       │ IPAddress      │
                       └────────────────┘

                                │1 (from CLAIM)
                                │
                               N│
                       ┌────────┴───────┐
                       │  NOTIFICATION  │
                       │────────────────│
                       │ NotificationID │
                       │ ClaimID        │
                       │ CustomerID     │
                       │ NotifType      │
                       │ Channel (SMS/  │
                       │   Email)       │
                       │ MessageContent │
                       │ SentAt         │
                       │ DeliveryStatus │
                       │ RetryCount     │
                       └────────────────┘

           ┌────────────────┐
           │  CLAIM STATUS  │  (Reference / Lookup Table)
           │────────────────│
           │ StatusID       │
           │ StatusCode     │
           │ StatusName     │
           │ Description    │
           │ IsTerminal     │
           │ AllowedRoles   │
           └────────────────┘
```

---

## Relationships Summary

| Relationship | Cardinality | Description |
|---|---|---|
| Customer → Policy | 1 : N | A customer may hold multiple policies |
| Policy → Vehicle | 1 : 1 | Each policy covers one vehicle (Phase 1 scope) |
| Policy → Claim | 1 : N | Multiple claims may be filed against a policy over time |
| Customer → Claim | 1 : N | A customer may have multiple claims |
| Claim → FNOL | 1 : 1 | Each claim has exactly one FNOL |
| Claim → Document | 1 : N | A claim has multiple supporting documents |
| Claim → Assessment | 1 : N | A claim may have multiple assessments (e.g., after re-open) |
| Assessment → Assessor | N : 1 | Each assessment is conducted by one assessor |
| Assessment → Decision | 1 : 1 | Each assessment results in one decision |
| Decision → Settlement | 1 : 0..1 | Only approved decisions lead to a settlement |
| Claim → AuditLog | 1 : N | Every state transition creates an audit log entry |
| Claim → Notification | 1 : N | Multiple notifications sent across the lifecycle |
| Claim → FraudFlag | 1 : N | Multiple fraud indicators may be flagged per claim |
| Claim → ClaimStatus | N : 1 | Claims reference the status lookup table |
| Claim → Claim (self) | 0..1 : 1 | Duplicate claims reference the original claim |

---

## Key Constraints

| Constraint | Rule |
|---|---|
| Claim.FinalSettlement ≤ Policy.IDV | Settlement cap enforced at DB level |
| AuditLog entries are INSERT-only | No UPDATE or DELETE permitted on audit log table |
| Assessment.Rationale length ≥ 100 chars | CHECK constraint |
| Document.FileSize ≤ 5,242,880 bytes (5 MB) | CHECK constraint |
| FNOL.IncidentDate ≤ FNOL.SubmittedAt | Incident cannot be in the future |
| ClaimRefNo format: CL-YYYY-XXXXXXXX | Generated by application layer; unique index |
