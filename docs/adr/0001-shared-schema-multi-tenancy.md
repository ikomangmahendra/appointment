# Shared-schema multi-tenancy instead of schema-per-tenant

All Klinik live in one PostgreSQL database and schema, scoped by a `clinic_id` column on every tenant-owned table, rather than each Klinik getting its own schema or database. Chosen for simpler migrations and operations while the Platform is in trial with a small number of Klinik. The trade-off: tenant isolation is enforced entirely at the application/query layer, not by the database — worth revisiting if the Platform scales, or if per-tenant isolation of sensitive data (NIK) becomes a hard requirement.
