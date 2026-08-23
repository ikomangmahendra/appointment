# Staff 2FA (Verifikasi Dua Langkah) is opt-in and self-chosen, not Admin-Klinik-configured

Issue #1's Implementation Decisions specify Staff auth as email+password only, with no OTP/2FA step. The Staff Management prototype opened the question of adding one — WhatsApp OTP or an Authenticator App (TOTP) — after feedback asked for it.

Decision: Verifikasi Dua Langkah is optional per Staff member, and the Staff member chooses their own method (or none) at Undangan Akun onboarding — the same self-service pattern already established for passwords (ADR 0028). It is not a setting Admin Klinik configures on a colleague's behalf, and it is not mandatory for any role, including Admin Klinik despite its elevated privileges.

Considered and rejected for now: Admin-Klinik-mandated 2FA, either platform-wide or per role. Rejected as unnecessary friction during the platform's trial phase (see Issue #1's Out of Scope — self-service Klinik onboarding and billing are both still manual/deferred, consistent with keeping onboarding lightweight elsewhere too). This can be revisited later without breaking the self-chosen model — a future mandate would just add a "must have one enabled" check on top of the same picker, not replace it.

Note: `prototypes/klinik-staff-login.prototype.html` (the staff login screen prototype) predates this decision and does not yet model a Verifikasi Dua Langkah step in the login flow. It should be updated to match in a follow-up pass.
