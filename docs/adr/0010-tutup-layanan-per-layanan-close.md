# Tutup Layanan closes per-Layanan, not per-Klinik, and gates the Klinik's daily reset

Each Layanan's own Staf Layanan manually closes their queue for the day via Tutup Layanan, rather than the Klinik closing everything at once — different Layanan (e.g. Gigi vs. Vaksinasi) finish their day at different times, so a single Klinik-wide close would either cut some Layanan off early or hold others open needlessly.

Tutup Layanan auto-resolves Appointment Status only for leftovers where the outcome is safely inferable: `belum tiba` → `no-show` (never arrived), `menunggu` → `cancelled` (still waiting when service ended). It refuses to run — listing the offending Appointments — while any Appointment in that Layanan is still `sedang dilayani`, since a patient physically mid-consultation can't be assumed abandoned; those must be manually moved to `selesai` or reverted first.

The Klinik-wide daily board reset (ADR 0008) only occurs once every Layanan at that Klinik has run Tutup Layanan. A forgotten close blocks the whole Klinik's rollover rather than letting that Layanan's unresolved Appointments silently vanish from the working view when the day flips.
