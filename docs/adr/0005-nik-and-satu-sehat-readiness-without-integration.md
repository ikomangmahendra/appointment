---
status: extended by ADR 0013 (specifies where/how the consent flag this ADR requires gets captured)
---

# Capture NIK and shape Patient data for SATU SEHAT now; defer the actual integration

The Patient model includes an optional NIK field, format validation, and a data shape loosely aligned with the FHIR Patient resource used by Indonesia's SATU SEHAT platform — but no SATU SEHAT API integration is built in this scope. This lets the Platform start collecting the data it will eventually need without taking on the cost and complexity of the integration itself before it's required.

Because NIK is a sensitive national identifier, storing it requires the Patient's explicit consent, captured before the value is saved. See [ADR 0013](0013-nik-consent-at-point-of-entry.md) for where and how that consent is captured.
