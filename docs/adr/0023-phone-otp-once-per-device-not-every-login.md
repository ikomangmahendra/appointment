---
status: supersedes part of ADR 0015 (phone verification timing at Google SSO login)
---

# Phone OTP verifies once per device (Perangkat Terpercaya), not on every login

ADR 0015 said a Google SSO signup's phone verification "can be deferred to the User's first Appointment" rather than blocking signup. In practice, phone verification now always happens right at that first login — but only that once: after a phone's OTP succeeds on a device, the same device can log in again later (via Google or email + password) without re-verifying, until the User explicitly logs out or a different device is used. A phone already claimed by an existing account (via any login method) resolves into that same account once its OTP succeeds, rather than creating a second one.

Requiring OTP on every single login (the shape first tried while prototyping this) is needless friction for a low-frequency action like booking an appointment — most Patients don't book weekly. Trusting the device after the first verification keeps the phone-verification guarantee ADR 0015 cares about (WhatsApp OTP/reminders elsewhere in the Platform depend on it) without repeating it pointlessly. Letting trust decay only on explicit logout, not on a timer, avoids reintroducing that same friction via a back door; logout is also the deliberate "forget this device" moment (e.g. a shared or public computer), which a passive timeout wouldn't equal for.
