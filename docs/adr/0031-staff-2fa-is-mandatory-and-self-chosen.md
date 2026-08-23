# Staff Verifikasi Dua Langkah (2FA) is mandatory, but self-chosen by the Staff member

Issue #1's Implementation Decisions specify Staff auth as email+password only, with no OTP/2FA step. The Staff Management prototype opened the question of adding one — WhatsApp OTP or an Authenticator App (TOTP). A parallel effort on the Klinik staff login screen (issue #33) already decided every staff login requires this second factor, with no way to skip it; that resolves the question here too, and supersedes this ADR's original draft, which had proposed making it opt-in.

Decision: Verifikasi Dua Langkah is mandatory for every Staff role, including Admin Klinik — there is no "none" option. The Staff member still chooses *which* method, WhatsApp OTP or Authenticator App, at Undangan Akun onboarding; Admin Klinik cannot configure or see which method a colleague uses. This keeps the self-service spirit of ADR 0028 (the Staff member controls their own credentials) while treating the second factor itself, unlike the password, as non-optional.

Considered and rejected: opt-in 2FA (this ADR's original position). Rejected because issue #33 already committed to mandatory OTP as part of the staff login flow, and Staff accounts guard Patient and schedule data, not just their own — a security control that's easy to skip is one that will get skipped.

Note: `prototypes/staff-detail.prototype.html`'s "Belum diatur" (not yet set) state and its Admin-Klinik-facing toggle buttons are a demo stand-in from before this was resolved as mandatory and self-chosen. The real flow should require picking a method during Undangan Akun onboarding (see issue #33), with no unset state and no Admin-Klinik-facing control to set it on a colleague's behalf.
