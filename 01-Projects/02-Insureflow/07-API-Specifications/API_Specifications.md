# API Specifications
## InsureFlow — Motor Insurance Claims Management Platform

**Version:** 1.0  
**Author:** Sai Balaji Budumuru  
**Date:** November 2026  
**Base URL:** `https://api.insureflow.internal/v1`  
**Authentication:** Bearer token (JWT); all endpoints require `Authorization: Bearer <token>` header.

> All request/response bodies are JSON. All timestamps in ISO 8601 UTC format.

---

## API 1: Policy Verification

**Purpose:** Validates a policy number against the Policy Administration System (PAS) and returns policy details to determine claim eligibility.

**Called by:** InsureFlow Claims System (internal, on FNOL submission)  
**Source:** Policy Administration System (PAS)

---

### Endpoint

```
POST /policies/verify
```

### Request Headers

| Header | Value |
|---|---|
| Content-Type | application/json |
| Authorization | Bearer {token} |
| X-Request-ID | UUID (for traceability) |
| X-Source-System | insureflow-claims |

### Request Body

```json
{
  "policyNumber": "MOT-2025-00123456",
  "vehicleRegistrationNumber": "TS09AB1234",
  "incidentDate": "2026-10-15",
  "incidentType": "OWN_DAMAGE"
}
```

### Request Field Definitions

| Field | Type | Required | Validation | Description |
|---|---|---|---|---|
| policyNumber | string | Yes | 10–20 alphanumeric chars | Policy number as entered on FNOL |
| vehicleRegistrationNumber | string | Yes | Pattern: [A-Z]{2}[0-9]{2}[A-Z]{0,2}[0-9]{4} | Vehicle registration number |
| incidentDate | string (date) | Yes | ISO 8601 date; not future; not > 30 days past | Date the incident occurred |
| incidentType | enum | Yes | OWN_DAMAGE / THEFT / THIRD_PARTY | Type of claim being registered |

### Response: 200 OK — Policy Valid

```json
{
  "requestId": "req-abc-12345",
  "status": "VALID",
  "policyNumber": "MOT-2025-00123456",
  "policyHolder": {
    "name": "Rajesh Kumar Sharma",
    "dateOfBirth": "1985-04-12"
  },
  "vehicle": {
    "registrationNumber": "TS09AB1234",
    "make": "Maruti Suzuki",
    "model": "Swift Dzire",
    "manufactureYear": 2021
  },
  "policy": {
    "type": "COMPREHENSIVE",
    "startDate": "2025-03-01",
    "endDate": "2026-02-28",
    "status": "ACTIVE",
    "idv": 650000.00,
    "policyExcess": 1000.00,
    "coverageTypes": ["OWN_DAMAGE", "THEFT", "THIRD_PARTY"]
  },
  "eligibility": {
    "isPolicyActiveOnIncidentDate": true,
    "isIncidentTypeCovered": true,
    "isVehicleMatched": true
  },
  "verifiedAt": "2026-10-16T11:00:30Z"
}
```

### Response: 200 OK — Policy Invalid

```json
{
  "requestId": "req-abc-12346",
  "status": "INVALID",
  "policyNumber": "MOT-2025-00123456",
  "reason": "POLICY_EXPIRED",
  "reasonDescription": "Policy end date (2025-02-28) is before the incident date (2026-10-15).",
  "verifiedAt": "2026-10-16T11:00:35Z"
}
```

### Response: 404 Not Found

```json
{
  "requestId": "req-abc-12347",
  "status": "NOT_FOUND",
  "policyNumber": "MOT-9999-00000000",
  "reason": "POLICY_NOT_FOUND",
  "reasonDescription": "No policy record found for the provided policy number.",
  "verifiedAt": "2026-10-16T11:00:40Z"
}
```

### Response: 503 Service Unavailable (PAS down)

```json
{
  "requestId": "req-abc-12348",
  "status": "SERVICE_UNAVAILABLE",
  "reason": "PAS_UNAVAILABLE",
  "reasonDescription": "Policy Administration System is temporarily unavailable. Please retry or validate manually.",
  "retryAfterSeconds": 300
}
```

### Status / Reason Code Reference

| Status | Reason Code | Description |
|---|---|---|
| VALID | — | Policy active, vehicle matched, incident type covered |
| INVALID | POLICY_EXPIRED | Policy end date before incident date |
| INVALID | POLICY_CANCELLED | Policy was cancelled before incident date |
| INVALID | POLICY_SUSPENDED | Policy suspended on incident date |
| INVALID | VEHICLE_MISMATCH | Vehicle registration does not match policy |
| INVALID | COVERAGE_EXCLUSION | Incident type not covered under policy type |
| NOT_FOUND | POLICY_NOT_FOUND | Policy number does not exist in PAS |
| SERVICE_UNAVAILABLE | PAS_UNAVAILABLE | PAS API not reachable |

### SLA

- Target response time: < 2 seconds (p95)
- Timeout: 10 seconds; on timeout, InsureFlow retries up to 3 times before setting claim to Pending Validation.

---

## API 2: Claim Submission

**Purpose:** Registers a new claim / FNOL in the InsureFlow system.

**Called by:** Customer Portal / Agent Portal  
**Returns:** Claim Reference Number (CRN)

---

### Endpoint

```
POST /claims
```

### Request Body

```json
{
  "policyNumber": "MOT-2025-00123456",
  "vehicleRegistrationNumber": "TS09AB1234",
  "incidentDate": "2026-10-15",
  "incidentType": "OWN_DAMAGE",
  "incidentLocation": {
    "description": "Near PVNR Flyover, Hyderabad",
    "pincode": "500082"
  },
  "incidentDescription": "My vehicle was rear-ended by an unidentified vehicle at a traffic signal near PVNR Flyover while I was stationary. The rear bumper and left taillight are damaged.",
  "firNumber": null,
  "submissionChannel": "CUSTOMER_PORTAL",
  "submittedByCustomerId": 10045,
  "assistingAgentId": null,
  "agentConsentNoted": false
}
```

### Request Field Definitions

| Field | Type | Required | Validation | Description |
|---|---|---|---|---|
| policyNumber | string | Yes | — | Policy number |
| vehicleRegistrationNumber | string | Yes | — | Vehicle reg. number |
| incidentDate | string (date) | Yes | Not future; not > 30 days past | Incident date |
| incidentType | enum | Yes | OWN_DAMAGE / THEFT / THIRD_PARTY | Claim type |
| incidentLocation.description | string | Yes | — | Free-text location |
| incidentLocation.pincode | string | Yes | 6 digits | Location pincode |
| incidentDescription | string | Yes | Min 50 chars | Claimant's account |
| firNumber | string | Conditional | Required if incidentType = THEFT | FIR number |
| submissionChannel | enum | Yes | CUSTOMER_PORTAL / AGENT_ASSISTED | Submission origin |
| submittedByCustomerId | integer | Yes | Valid CustomerID | Claimant ID |
| assistingAgentId | integer | Conditional | Required if AGENT_ASSISTED | Agent ID |
| agentConsentNoted | boolean | Conditional | Required true if AGENT_ASSISTED | Verbal consent flag |

### Response: 201 Created

```json
{
  "claimId": 90001,
  "claimRefNo": "CL-2026-00090001",
  "status": "FNOL_REGISTERED",
  "policyNumber": "MOT-2025-00123456",
  "incidentType": "OWN_DAMAGE",
  "submittedAt": "2026-10-16T11:00:00Z",
  "message": "Your claim has been registered. You will receive an SMS and email confirmation shortly.",
  "nextSteps": {
    "action": "UPLOAD_DOCUMENTS",
    "documentChecklist": [
      "Duly filled claim form",
      "RC copy",
      "Driving licence copy",
      "Repair estimate from registered workshop",
      "Photographs of damage (minimum 3)"
    ],
    "uploadDeadlineDays": 5
  }
}
```

### Response: 400 Bad Request — Validation Failure

```json
{
  "status": "VALIDATION_ERROR",
  "errors": [
    {
      "field": "incidentDescription",
      "code": "MIN_LENGTH",
      "message": "Incident description must be at least 50 characters."
    },
    {
      "field": "incidentDate",
      "code": "DATE_TOO_OLD",
      "message": "Incident date exceeds the 30-day reporting window."
    }
  ]
}
```

### Response: 409 Conflict — Duplicate Claim

```json
{
  "status": "DUPLICATE_DETECTED",
  "claimRefNo": "CL-2026-00090001",
  "message": "A claim has already been registered for this policy and incident date. Your submission has been placed under preliminary review.",
  "existingClaimRef": "CL-2026-00089998"
}
```

---

## API 3: Claim Status

**Purpose:** Retrieves the current status and summary of a claim. Used by the customer portal, agent portal and assessor dashboard.

---

### Endpoint

```
GET /claims/{claimRefNo}
```

### Path Parameter

| Parameter | Type | Description |
|---|---|---|
| claimRefNo | string | Claim Reference Number (e.g., CL-2026-00090001) |

### Query Parameters (optional)

| Parameter | Type | Description |
|---|---|---|
| includeTimeline | boolean | If true, returns full status history |
| includeDocuments | boolean | If true, returns document checklist and upload status |

### Response: 200 OK

```json
{
  "claimId": 90001,
  "claimRefNo": "CL-2026-00090001",
  "policyNumber": "MOT-2025-00123456",
  "incidentType": "OWN_DAMAGE",
  "incidentDate": "2026-10-15",
  "currentStatus": {
    "code": "UNDER_ASS",
    "label": "Under Assessment",
    "description": "Your claim is being reviewed by our assessor.",
    "updatedAt": "2026-10-19T10:30:00Z"
  },
  "submittedAt": "2026-10-16T11:00:00Z",
  "timeline": [
    {
      "status": "FNOL_REGISTERED",
      "label": "Claim Registered",
      "occurredAt": "2026-10-16T11:00:00Z"
    },
    {
      "status": "POL_VAL",
      "label": "Policy Validated",
      "occurredAt": "2026-10-16T11:01:00Z"
    },
    {
      "status": "DOC_VER",
      "label": "Documents Verified",
      "occurredAt": "2026-10-18T14:00:00Z"
    },
    {
      "status": "ASSIGNED",
      "label": "Assigned to Assessor",
      "occurredAt": "2026-10-19T09:00:00Z"
    },
    {
      "status": "UNDER_ASS",
      "label": "Under Assessment",
      "occurredAt": "2026-10-19T10:30:00Z"
    }
  ],
  "documents": {
    "totalRequired": 5,
    "uploaded": 5,
    "verified": 5,
    "items": [
      { "type": "Claim Form", "status": "VERIFIED", "uploadedAt": "2026-10-17T09:00:00Z" },
      { "type": "RC Copy", "status": "VERIFIED", "uploadedAt": "2026-10-17T09:05:00Z" },
      { "type": "Driving Licence", "status": "VERIFIED", "uploadedAt": "2026-10-17T09:10:00Z" },
      { "type": "Repair Estimate", "status": "VERIFIED", "uploadedAt": "2026-10-17T10:00:00Z" },
      { "type": "Damage Photographs", "status": "VERIFIED", "uploadedAt": "2026-10-17T10:15:00Z" }
    ]
  },
  "customerMessage": "Your claim is currently under review by one of our assessors. We will notify you once a decision has been made.",
  "estimatedResolutionDate": "2026-10-26"
}
```

### Response: 403 Forbidden

```json
{
  "status": "FORBIDDEN",
  "message": "You are not authorised to access this claim."
}
```

### Response: 404 Not Found

```json
{
  "status": "NOT_FOUND",
  "message": "No claim found for reference CL-2026-00099999."
}
```

---

## Common Error Codes

| HTTP Status | Code | Description |
|---|---|---|
| 400 | VALIDATION_ERROR | One or more request fields failed validation |
| 401 | UNAUTHORIZED | Missing or invalid JWT token |
| 403 | FORBIDDEN | Authenticated user does not have access to this resource |
| 404 | NOT_FOUND | Requested resource does not exist |
| 409 | DUPLICATE_DETECTED | Duplicate claim for same policy + incident date |
| 429 | RATE_LIMITED | Too many requests; retry after indicated seconds |
| 500 | INTERNAL_ERROR | Unexpected server error; contact support |
| 503 | SERVICE_UNAVAILABLE | Downstream service (PAS / PayGW) is unavailable |

---

## Rate Limits

| Endpoint | Limit | Window |
|---|---|---|
| POST /policies/verify | 100 requests | Per minute, per client |
| POST /claims | 50 requests | Per minute, per customer |
| GET /claims/{ref} | 200 requests | Per minute, per token |
