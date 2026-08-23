# Spesialisasi is a platform-wide, informational attribute of Dokter — distinct from Layanan

A Dokter gains a mandatory **Spesialisasi** (e.g. "Umum", "Gigi", "Anak"): exactly one value per Dokter, drawn from a single platform-wide list curated by Super-Admin, deliberately *unlike* Layanan's per-Klinik catalog (ADR 0022) — a medical specialty is a recognized taxonomy independent of any one Klinik, whereas a service menu is not. Renaming a value updates it in place everywhere it's referenced; retiring one is blocked while any Dokter still holds it, so data never silently goes stale.

Spesialisasi is purely informational, carrying no Harga or Deskripsi and imposing no eligibility constraint on which Layanan a Dokter may be assigned to — Layanan remains the sole bookable, priced concept (ADR 0025). It's shown wherever a Dokter's name appears, including the Patient-facing booking picker, but only as passive context, never as a filter or search facet.

## Considered Options

- **Merge into Layanan** (a Dokter's "specialty" is just the Layanan they perform) — rejected: conflates a descriptive attribute with a priced, bookable resource, and doesn't fit a Dokter who performs several distinct Layanan under one specialty (e.g. a "Dokter Gigi" doing both scaling and tambal).
- **Per-Klinik list, matching Layanan's own scoping** — rejected: unlike a clinic's bespoke service menu, medical specialties are a standard taxonomy that shouldn't be reinvented per Klinik.
- **Functional role** (constrain Layanan eligibility by Spesialisasi, or let Patients filter/search by it) — rejected for now: nothing in the existing booking flow needs it, and a hard eligibility constraint would foreclose real-world exceptions (e.g. a generalist credentialed for a specific procedure). Can be layered on later without a model change.
- **Multiple Spesialisasi per Dokter** — rejected: the existing data shape is a single scalar value, and there's no evidence multi-specialty Dokter are a real case here.
