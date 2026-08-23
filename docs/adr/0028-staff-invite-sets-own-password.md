# Staff accounts are invited by Admin Klinik; the staff member sets their own password

When Admin Klinik grants a Resepsionis, Staf Layanan, or Dokter login access, Admin Klinik sends an email invitation and the recipient sets their own password via a link in it — Admin Klinik never types, sets, or learns that password. The alternative (Admin Klinik assigning a temporary password the staff member changes at first login) was considered and rejected: it means a manager handles a colleague's credential, even briefly, for no real benefit, and it would break from the self-set-password pattern already established for User (ADR 0015 — a User sets their own password whenever they choose, never assigned by anyone else).

Until an invitation is accepted, the staff profile still exists and is usable for non-login purposes — e.g. a Dokter can already be assigned Layanan and a Jadwal before ever logging in — it simply has no usable login yet.
