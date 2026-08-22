---
status: broadened — originally scoped to guest history only; now covers any existing Patient record regardless of who created it
---

# Existing Patient history auto-links to a User by phone match

When someone registers as a User, any existing Appointment history and Patient record under that same (now-verified) phone number is automatically linked to the new User, rather than the User starting with a blank slate. This applies regardless of how that Patient record came to exist — a guest booking, a Resepsionis phone booking, or a Patient another User added as a dependent (e.g. a child who grows up and registers with their own phone, per ADR 0020) — the phone match and the verification are the same in every case. Linking is additive: it doesn't remove any other User already managing that Patient (ADR 0020).

Phone number is already the primary identity signal Patient Matching relies on (ADR 0004), so reusing it here to link a Patient's past visits to their new account doesn't introduce a new identity mechanism — it applies the existing one at a new moment. The alternative — leaving that history permanently disconnected from an account created later — would silently lose booking history a Patient would reasonably expect to see once they bother to register.
