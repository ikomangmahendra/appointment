# Patient deduplication only auto-merges on exact NIK match

Patient Matching auto-merges two Patient records only when both share the same NIK. Every other match — fuzzy match on phone number, name, and date of birth — is surfaced as a Duplicate Candidate for manual review by a Resepsionis or Admin Klinik, never merged automatically. This trades some duplicate clutter for safety: wrongly merging two different patients' records is a worse outcome, medically and for privacy, than living with an unresolved duplicate for a while.
