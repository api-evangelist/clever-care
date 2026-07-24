---
name: Retrieve a Clever Care member's claims and coverage
description: Use SMART on FHIR to authenticate a member and pull their CARIN Blue Button coverage and claims (ExplanationOfBenefit) from the Clever Care Patient Access API.
api: openapi/clever-care-patient-openapi.json
operations:
  - Patient GET / (search)
  - Coverage GET / (search)
  - ExplanationOfBenefit GET / (search)
---

# Retrieve a Clever Care member's claims and coverage

Clever Care exposes CARIN Blue Button (C4BB) member data over HL7 FHIR R4. These
resources are **secured** — they require a SMART on FHIR patient-context token.

## Prerequisites
- A developer application registered at `https://fhir-portal.clevercarehealthplan.com/devportal`.
- SMART on FHIR OAuth 2.0 with PKCE (S256).

## Steps
1. **Authorize (SMART patient launch).** Send the member through the authorization
   endpoint `https://fhir-portal.clevercarehealthplan.com/oauth2/authorize` requesting
   scopes `openid launch/patient patient/*.cruds`. Complete the `authorization_code`
   exchange at `https://fhir-portal.clevercarehealthplan.com/oauth2/token`. The token
   response carries the launched `patient` id.
2. **Confirm the patient.** `GET https://fhir.clevercarehealthplan.com/r4/Patient/{id}`
   (openapi/clever-care-patient-openapi.json, `GET /{id}`) to confirm the member.
3. **List coverage.** `GET /r4/Coverage?patient={id}`
   (openapi/clever-care-coverage-openapi.json, `GET /`).
4. **List claims.** `GET /r4/ExplanationOfBenefit?patient={id}` with optional
   `type`, `service-date`, or `_lastUpdated` filters
   (openapi/clever-care-explanation-of-benefit-openapi.json, `GET /`). Page through
   the returned searchset Bundle using `_count`/`_offset` and the `next` link.

## Rules
- Send `Authorization: Bearer <token>` and `Accept: application/fhir+json` on every request.
- Errors return a FHIR `OperationOutcome` (see errors/clever-care-problem-types.yml);
  a `403` means the token lacks the required scope, `401` means it is missing/expired.
- This surface is read-only. Conventions: conventions/clever-care-conventions.yml.
