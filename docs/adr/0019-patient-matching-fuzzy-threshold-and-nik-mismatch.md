---
status: extends ADR 0004
---

# Patient Matching: 2-of-3 fuzzy threshold, and a differing NIK rules out a match entirely

ADR 0004 established that an exact NIK match auto-merges and everything else is a fuzzy match producing a Duplicate Candidate, but left "fuzzy match" unspecified. Pressure-testing it against messy, real-looking data (typo'd names, a shared household phone, a dismissed candidate later resolved by a NIK match, and near-identical twins) settled on two concrete rules:

- When neither or only one record has a NIK, at least **2 of the 3** signals — phone number, name (small typos tolerated), date of birth — must match to raise a Duplicate Candidate. One signal alone isn't enough: a shared household phone number between two otherwise-different people (e.g. a parent and child) must not be flagged.
- When **both** records have a NIK and it **differs**, that's treated as certain proof of two different people — no Duplicate Candidate is raised at all, even if every fuzzy signal matches (the "twins" case: identical name, DOB, and phone, but distinct NIKs). An explicit, differing NIK is stronger evidence than any fuzzy signal, so this case shouldn't cost a Resepsionis a manual review.

Both rules were validated in a throwaway prototype (`prototypes/patient-dedup.prototype.html`) across five guided scenarios, including a dismissed Duplicate Candidate that a later NIK match correctly overrides and merges anyway, per ADR 0004.
