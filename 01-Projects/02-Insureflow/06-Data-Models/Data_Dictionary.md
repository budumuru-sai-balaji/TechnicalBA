# Data Dictionary
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026

---

## Conventions

- **PK** = Primary Key | **FK** = Foreign Key | **UK** = Unique Key | **NN** = Not Null
- **Data Types:** VARCHAR(n), INT, DECIMAL(p,s), DATE, DATETIME, BOOLEAN, TEXT, ENUM
- All DATETIME fields stored in UTC; displayed in IST (UTC+5:30) in the UI.

---

## Table: CUSTOMER

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| CustomerID | INT | PK, NN, Auto-increment | Unique system identifier | 10045 |
| FullName | VARCHAR(150) | NN | Claimant's legal full name | Rajesh Kumar Sharma |
| DateOfBirth | DATE | NN | Date of birth | 1985-04-12 |
| Email | VARCHAR(100) | UK, NN | Primary email address | rajesh@email.com |
| PhoneNumber | VARCHAR(15) | NN | 10-digit mobile number (with country code) | +919876543210 |
| AddressLine1 | VARCHAR(200) | NN | Door number and street | 42, MG Road |
| AddressLine2 | VARCHAR(200) | — | Area / locality | Banjara Hills |
| City | VARCHAR(100) | NN | City | Hyderabad |
| State | VARCHAR(100) | NN | State | Telangana |
| Pincode | VARCHAR(6) | NN | 6-digit postal code | 500034 |
| IDProofType | ENUM | NN | Type of government ID submitted | Aadhaar / PAN / Passport / DL |
| IDProofNumber | VARCHAR(50) | NN | ID document number | ABCDE1234F |
| BankAccountNumber | VARCHAR(20) | NN, Encrypted | Beneficiary bank account number | 1234567890 |
| IFSCCode | VARCHAR(11) | NN | Bank branch IFSC code | HDFC0001234 |
| CreatedAt | DATETIME | NN | Record creation timestamp | 2026-01-15T10:30:00Z |
| UpdatedAt | DATETIME | NN | Last update timestamp | 2026-06-01T14:00:00Z |
| IsActive | BOOLEAN | NN, Default: TRUE | Whether the customer account is active | TRUE |

---

## Table: POLICY

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| PolicyID | INT | PK, NN, Auto-increment | Unique policy record identifier | 5001 |
| CustomerID | INT | FK (Customer), NN | Owning customer | 10045 |
| PolicyNumber | VARCHAR(20) | UK, NN | Policy number from PAS | MOT-2025-00123456 |
| PolicyType | ENUM | NN | Type of policy | Comprehensive / Third-Party |
| StartDate | DATE | NN | Policy start date | 2025-03-01 |
| EndDate | DATE | NN | Policy end date | 2026-02-28 |
| Status | ENUM | NN | Current policy status (from PAS) | Active / Expired / Cancelled / Suspended / Lapsed |
| IDV | DECIMAL(12,2) | NN | Insured Declared Value in INR | 650000.00 |
| PolicyExcess | DECIMAL(10,2) | NN | Compulsory deductible amount in INR | 1000.00 |
| CoverageTypes | VARCHAR(200) | NN | Comma-separated coverage types | OD, Theft, TP |
| PremiumAmount | DECIMAL(10,2) | NN | Annual premium in INR | 18500.00 |
| PASLastSyncedAt | DATETIME | NN | Last time record was synced from PAS | 2026-11-10T08:00:00Z |
| CreatedAt | DATETIME | NN | Record creation timestamp | 2025-03-01T09:00:00Z |

---

## Table: VEHICLE

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| VehicleID | INT | PK, NN, Auto-increment | Unique vehicle record identifier | 3001 |
| PolicyID | INT | FK (Policy), NN, UK | Associated policy (1-to-1) | 5001 |
| RegistrationNumber | VARCHAR(15) | UK, NN | Official vehicle registration number | TS09AB1234 |
| Make | VARCHAR(50) | NN | Vehicle manufacturer | Maruti Suzuki |
| Model | VARCHAR(100) | NN | Vehicle model name | Swift Dzire |
| ManufactureYear | INT | NN | Year of manufacture (YYYY) | 2021 |
| FuelType | ENUM | NN | Fuel type | Petrol / Diesel / CNG / Electric / Hybrid |
| BodyType | ENUM | NN | Vehicle category | Sedan / Hatchback / SUV / Two-Wheeler / Commercial |
| EngineNumber | VARCHAR(50) | NN | Engine serial number | K12M1234567 |
| ChassisNumber | VARCHAR(50) | NN | Chassis serial number (VIN) | MA3FJEB1S00123456 |
| ColourCode | VARCHAR(10) | — | Manufacturer colour code | ZUE (Pearl White) |

---

## Table: CLAIM

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| ClaimID | INT | PK, NN, Auto-increment | Unique claim identifier | 90001 |
| PolicyID | INT | FK (Policy), NN | Policy against which claim is filed | 5001 |
| CustomerID | INT | FK (Customer), NN | Claimant | 10045 |
| FNOLID | INT | FK (FNOL), NN | Associated FNOL record | 70001 |
| ClaimRefNo | VARCHAR(20) | UK, NN | Business-facing claim reference | CL-2026-00090001 |
| IncidentDate | DATE | NN | Date on which incident occurred | 2026-10-15 |
| IncidentType | ENUM | NN | Type of claim | Own Damage / Theft / Third-Party Liability |
| IncidentLocation | VARCHAR(300) | NN | Description of incident location | Near PVNR Flyover, Hyderabad |
| IncidentPincode | VARCHAR(6) | NN | Pincode of incident location | 500082 |
| IncidentDescription | TEXT | NN, Min 50 chars | Customer's description of the incident | Vehicle was hit from behind at a traffic signal... |
| FIRNumber | VARCHAR(50) | Conditional (NN for Theft) | FIR number if applicable | 456/2026 |
| StatusID | INT | FK (ClaimStatus), NN | Current claim status | 10 (Documents Verified) |
| DuplicateOfClaimID | INT | FK (Claim), Nullable | References original claim if duplicate | NULL / 90000 |
| FraudIndicatorCount | INT | NN, Default: 0 | Count of fraud indicators triggered | 0 |
| CreatedAt | DATETIME | NN | FNOL submission timestamp | 2026-10-16T11:00:00Z |
| UpdatedAt | DATETIME | NN | Last modification timestamp | 2026-10-20T14:30:00Z |

---

## Table: FNOL

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| FNOLID | INT | PK, NN, Auto-increment | Unique FNOL record | 70001 |
| ClaimID | INT | FK (Claim), UK, NN | Associated claim | 90001 |
| SubmittedBy | INT | FK (Customer or Agent) | Submitter ID | 10045 |
| SubmissionChannel | ENUM | NN | How FNOL was submitted | Customer Portal / Agent Assisted |
| AssistingAgentID | INT | FK (Assessor/Agent), Nullable | Agent who assisted (if applicable) | NULL |
| ConsentNoted | BOOLEAN | Conditional (NN if Agent-Assisted) | Customer consent for agent-assisted FNOL | TRUE |
| SubmittedAt | DATETIME | NN | FNOL submission timestamp | 2026-10-16T11:00:00Z |

---

## Table: DOCUMENT

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| DocumentID | INT | PK, NN, Auto-increment | Unique document record | 80001 |
| ClaimID | INT | FK (Claim), NN | Associated claim | 90001 |
| DocumentType | VARCHAR(100) | NN | Type of document per checklist | Repair Estimate |
| FileName | VARCHAR(255) | NN | Stored file name | repair_estimate_90001.pdf |
| FileFormat | ENUM | NN | File type | PDF / JPG / PNG |
| FileSizeBytes | INT | NN, Max: 5242880 | File size in bytes | 1240000 |
| StoragePath | VARCHAR(500) | NN | Encrypted storage path | /claims/90001/docs/80001 |
| UploadedAt | DATETIME | NN | Upload timestamp | 2026-10-17T09:30:00Z |
| VerificationStatus | ENUM | NN, Default: Pending | Document verification outcome | Pending / Verified / Illegible / Incorrect |
| VerifiedByAgentID | INT | FK (Agent), Nullable | Agent who verified | 2001 |
| VerifiedAt | DATETIME | Nullable | Verification timestamp | 2026-10-18T10:00:00Z |
| RejectionReason | VARCHAR(300) | Nullable | Reason if Illegible/Incorrect | Document is blurred; signature missing |

---

## Table: ASSESSOR

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| AssessorID | INT | PK, NN, Auto-increment | Unique assessor record | 2001 |
| FullName | VARCHAR(150) | NN | Assessor's full name | Priya Nair |
| Email | VARCHAR(100) | UK, NN | Work email address | priya.nair@insureflow.com |
| Role | ENUM | NN | Assessor role level | Assessor / Senior Assessor / Manager |
| Specialisation | VARCHAR(200) | NN | Claim types certified to assess | Own Damage, Theft |
| OpenClaimCount | INT | NN, Default: 0 | Current number of open assigned claims | 7 |
| IsActive | BOOLEAN | NN, Default: TRUE | Whether assessor is currently available | TRUE |
| JoinedAt | DATE | NN | Date joined the claims team | 2023-06-01 |

---

## Table: ASSESSMENT

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| AssessmentID | INT | PK, NN, Auto-increment | Unique assessment record | 60001 |
| ClaimID | INT | FK (Claim), NN | Assessed claim | 90001 |
| AssessorID | INT | FK (Assessor), NN | Conducting assessor | 2001 |
| AssignedAt | DATETIME | NN | Auto-assignment timestamp | 2026-10-19T09:00:00Z |
| StartedAt | DATETIME | Nullable | When assessor first opened the claim | 2026-10-19T10:30:00Z |
| CompletedAt | DATETIME | Nullable | When decision was submitted | 2026-10-23T15:00:00Z |
| CoverageFinding | ENUM | NN | Coverage determination | Covered / Not Covered / Partially Covered |
| DamageFindings | TEXT | NN | Detailed damage observations | Rear bumper, left taillight, boot lid damage consistent with collision... |
| Rationale | TEXT | NN, Min 100 chars | Decision rationale | Coverage confirmed under OD. Damage consistent with reported incident. No exclusion clauses apply. IDV verified. Settlement recommended at... |
| RecommendedSettlement | DECIMAL(12,2) | Nullable | Assessor-recommended settlement (INR) | 45000.00 |
| AssessmentType | ENUM | NN | Initial or re-assessment | Initial / Re-open |

---

## Table: DECISION

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| DecisionID | INT | PK, NN, Auto-increment | Unique decision record | 50001 |
| AssessmentID | INT | FK (Assessment), UK, NN | Associated assessment | 60001 |
| ClaimID | INT | FK (Claim), NN | Decided claim | 90001 |
| DecisionType | ENUM | NN | Outcome of assessment | Approved / Rejected / Escalated |
| MadeByID | INT | FK (Assessor/Manager), NN | Decision maker | 2001 |
| MadeByRole | ENUM | NN | Role of decision maker | Assessor / Senior Assessor / Manager |
| Rationale | TEXT | NN, Min 100 chars | Decision rationale | — |
| DecidedAt | DATETIME | NN | Decision timestamp | 2026-10-23T15:00:00Z |

---

## Table: SETTLEMENT

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| SettlementID | INT | PK, NN, Auto-increment | Unique settlement record | 40001 |
| ClaimID | INT | FK (Claim), UK, NN | Associated claim | 90001 |
| DecisionID | INT | FK (Decision), NN | Approval decision | 50001 |
| IDV | DECIMAL(12,2) | NN | IDV at time of claim registration | 650000.00 |
| PolicyExcess | DECIMAL(10,2) | NN | Compulsory deductible | 1000.00 |
| VehicleAgeMonths | INT | NN | Vehicle age at incident date in months | 36 |
| DepreciationPct | DECIMAL(5,2) | NN | Applied depreciation percentage | 30.00 |
| DepreciationAmount | DECIMAL(12,2) | NN | Depreciation in INR | 194700.00 |
| CalculatedSettlement | DECIMAL(12,2) | NN | System-calculated amount | 45000.00 |
| FinalSettlement | DECIMAL(12,2) | NN, ≤ IDV | Final approved settlement amount | 45000.00 |
| IsOverridden | BOOLEAN | NN, Default: FALSE | Whether Finance overrode the calculated amount | FALSE |
| OverriddenByID | INT | FK (Finance User), Nullable | Finance user who overrode | NULL |
| OverrideReason | TEXT | Conditional NN if Overridden | Mandatory justification for override | — |
| FinanceApprovedByID | INT | FK (Finance User), Nullable | Approving Finance user | 3001 |
| FinanceApprovedAt | DATETIME | Nullable | Finance approval timestamp | 2026-10-25T11:00:00Z |
| DisbursedAt | DATETIME | Nullable | Disbursement initiation timestamp | 2026-10-26T10:00:00Z |
| UTRNumber | VARCHAR(50) | Nullable | Unique Transaction Reference from bank | 2026102600123456 |
| PaymentStatus | ENUM | NN | Disbursement status | Pending / Initiated / Success / Failed |

---

## Table: AUDIT_LOG

| Field | Data Type | Constraints | Description | Example |
|---|---|---|---|---|
| LogID | BIGINT | PK, NN, Auto-increment | Unique audit entry | 1000001 |
| ClaimID | INT | FK (Claim), NN | Claim being audited | 90001 |
| FromStatusID | INT | FK (ClaimStatus), Nullable | Previous status (NULL for first entry) | 1 |
| ToStatusID | INT | FK (ClaimStatus), NN | New status | 5 |
| ChangedByID | INT | NN | User ID making the change | 2001 |
| ChangedByRole | ENUM | NN | Role of user | System / Agent / Assessor / Senior Assessor / Manager / Finance / Customer |
| ChangeReason | TEXT | Nullable | Optional free-text reason | Policy validated via PAS API |
| ChangedAt | DATETIME | NN | Transition timestamp (UTC) | 2026-10-16T11:02:00Z |
| IPAddress | VARCHAR(45) | Nullable | Source IP (IPv4 or IPv6) | 192.168.1.10 |

*Note: This table is INSERT-only. No UPDATE or DELETE operations are permitted.*

---

## Table: CLAIM_STATUS (Reference)

| StatusID | StatusCode | StatusName | IsTerminal | Description |
|---|---|---|---|---|
| 1 | FNOL_REG | FNOL Registered | No | FNOL submitted; CRN issued |
| 2 | DUP_HOLD | Duplicate Hold | No | Duplicate detected; Manager review pending |
| 3 | PEND_VAL | Pending Validation | No | PAS unavailable; manual validation needed |
| 4 | AUTO_REJ | Auto-Rejected | Yes | Policy invalid; auto-rejected |
| 5 | POL_VAL | Policy Validated | No | Policy confirmed active |
| 6 | PEND_DOC | Pending Document Submission | No | Awaiting customer documents |
| 7 | DOC_OVRD | Documents Overdue | No | 5 BD elapsed; customer not responded |
| 8 | PEND_VER | Pending Agent Verification | No | Documents uploaded; under Agent review |
| 9 | DOC_VER | Documents Verified | No | All documents verified |
| 10 | ASSIGNED | Assigned to Assessor | No | In assessor queue |
| 11 | UNDER_ASS | Under Assessment | No | Assessor actively reviewing |
| 12 | FRAUD_REV | Fraud Review | No | Fraud indicators triggered; Manager review |
| 13 | SIU_REF | Referred to SIU | Yes | Fraud confirmed; SIU handling |
| 14 | ESCALATED | Escalated | No | Assessor escalated; Manager queue |
| 15 | APPROVED | Approved | No | Claim approved; pending settlement |
| 16 | REJECTED | Rejected | No | Claim rejected (re-open available for 15d) |
| 17 | PEND_SET | Pending Settlement | No | Settlement calculation in progress |
| 18 | SET_APPR | Settlement Approved | No | Finance approved disbursement |
| 19 | SETTLED | Settled | Yes | Disbursement complete |
| 20 | REOPENED | Re-opened | No | Customer disputed; re-assessment |
| 21 | CLOSED_NR | Closed — No Response | Yes | Customer unresponsive for 30+ days |
| 22 | WITHDRAWN | Withdrawn | Yes | Customer withdrew the claim |
