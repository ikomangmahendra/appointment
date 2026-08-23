---
status: supersedes ADR 0026 (Spesialisasi moves from a platform-wide catalog to a per-Klinik one)
---

# Spesialisasi becomes a per-Klinik attribute, seeded at onboarding

ADR 0026 gave Spesialisasi a single Platform-wide list curated by Super-Admin, on the reasoning that a medical specialty is a recognized taxonomy independent of any one Klinik. In practice, Admin Klinik wants the same management convenience Layanan already gives them — adding, renaming, and retiring their own values, and filtering their own Dokter directory by it — and a shared taxonomy protects nothing once every Klinik is free to diverge from it anyway. Spesialisasi is now defined and owned per-Klinik by its own Admin Klinik, the same shape as Layanan's catalog (ADR 0022): add a value, rename one (updates in place across that Klinik's own Dokter), or retire one (blocked while any of that Klinik's Dokter still holds it).

A new Klinik's list isn't blank: it's seeded once, at onboarding, from a fixed default set baked into the onboarding process. From that moment on, each Klinik's list is entirely independent — no further syncing back to any shared source, and no ongoing Super-Admin role in Spesialisasi at all. A Super-Admin-maintained "evolving template" was considered and rejected: it's a maintenance screen in service of a consistency guarantee that no longer exists once every Klinik can freely edit their own copy after the fact.

Spesialisasi stays purely informational — no Harga or Deskripsi, no constraint on which Layanan a Dokter may be assigned to — and remains passive, non-filterable context on the Patient-facing booking picker. The only behavioral change is that it's now also usable as an admin-side filter/search facet in Admin Klinik's own Dokter directory.
