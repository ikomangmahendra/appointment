# Dokter deactivation and login-revocation are independent, and deactivation never deletes

Admin Klinik can deactivate a Dokter (removing them from active use — no longer assignable to new Layanan or Jadwal) and can independently revoke that Dokter's login access; neither implies the other. A single combined status was considered and rejected because it can't represent two real cases at once: a Dokter on extended leave who keeps their profile and history intact while login is revoked, and a Dokter leaving the Klinik entirely who is deactivated while their account technically still exists.

Deactivation never hard-deletes the Dokter record. Existing Jadwal and Appointment entries reference that Dokter and would otherwise be orphaned — the same reasoning already applied to Patient, which is only ever soft-deleted.
