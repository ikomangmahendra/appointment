---
status: extends ADR 0005 (specifies where/how the consent it requires gets captured)
---

# Require explicit consent before storing NIK, captured at the point of entry

Whenever a Patient provides their NIK — currently only in the patient mobile booking flow — a consent checkbox must be checked before the flow can proceed, right below the NIK field itself. Leaving NIK blank requires no consent, since nothing sensitive is being stored. ADR 0005 requires consent before storing NIK but doesn't specify where or how that flag gets captured; this ADR fills that gap.

Capturing consent at the same point NIK is entered — rather than once during a separate Akun Pasien registration step — keeps the request tied to the specific data being collected, and matches the only place in the product today where NIK is actually asked for.
