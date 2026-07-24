---
name: Search the Clever Care provider directory
description: Query the public Da Vinci PDex Plan-Net provider directory (organizations, practitioners, roles, locations) from the Clever Care FHIR API.
api: openapi/clever-care-organization-openapi.json
operations:
  - Organization GET / (search)
  - PractitionerRole GET / (search)
  - Practitioner GET / (search)
  - Location GET / (search)
---

# Search the Clever Care provider directory

The Provider Directory resources follow the Da Vinci PDex Plan-Net profiles and
are **public but rate-limited** — they require a registered developer app key
(client credentials) rather than a member context.

## Prerequisites
- A developer application registered at `https://fhir-portal.clevercarehealthplan.com/devportal`.

## Steps
1. **Find an organization/network.** `GET https://fhir.clevercarehealthplan.com/r4/Organization?name={name}&type={type}&coverage-area={location}`
   (openapi/clever-care-organization-openapi.json, `GET /`).
2. **Find practitioners and their roles.** `GET /r4/PractitionerRole?organization={orgId}`
   (openapi/clever-care-practitioner-role-openapi.json, `GET /`), then resolve each
   `practitioner` reference via `GET /r4/Practitioner/{id}`
   (openapi/clever-care-practitioner-openapi.json, `GET /{id}`).
3. **Resolve service locations.** Follow `location` references with
   `GET /r4/Location/{id}` (openapi/clever-care-location-openapi.json, `GET /{id}`)
   and `GET /r4/HealthcareService?organization={orgId}`.
4. **Page results.** Use `_count` and `_offset`, following the searchset Bundle
   `next` link until exhausted.

## Rules
- Send `Accept: application/fhir+json`.
- Honor rate-limit signaling; a `503` OperationOutcome indicates throttling
  (see errors/clever-care-problem-types.yml).
- Reference/id graph documented in data-model/clever-care-data-model.yml.
