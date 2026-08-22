# Cap `sedang dilayani` at one Appointment per Dokter, checked Klinik-wide

A Dokter can have at most one Appointment in `sedang dilayani` at a time. A Dokter is a single person, not a queue slot, and `Appointment` already pins each booking to one specific Dokter rather than a Layanan-wide pool — so "Mulai Layani" is blocked for any other Appointment booked with a Dokter who is already serving someone else.

The check is Klinik-wide, not scoped to the current Layanan's filtered board (ADR 0008). Nothing in the domain prevents a real Dokter from working across more than one Layanan the same day, and scoping the check per-Layanan would make the invariant silently unenforceable in exactly that case — a Dokter could end up shown as `sedang dilayani` with two different patients in two different Layanan views at once.

A booked Dokter who isn't actually on duty today (sick, covered by a substitute) is a known gap: reassigning an Appointment to a different Dokter isn't supported yet. This is deliberately deferred — it's a distinct feature (editing who an Appointment is booked with) rather than part of the capacity rule itself.
