# Klinik Appointment Booking

A multi-tenant web application where Klinik (clinics) manage their own Dokter, Layanan, and Appointment scheduling, and Pasien book, cancel, or reschedule Appointments online or through clinic staff.

## Language

### Tenancy & Actors

**Platform**:
The overall multi-tenant SaaS system hosting multiple Klinik under one shared database.

**Klinik**:
A single clinic tenant on the Platform, with its own Dokter, Layanan, staff, operating hours, and timezone.
_Avoid_: Tenant, Clinic Account

**Super-Admin**:
A Platform-level operator who provisions new Klinik and oversees the Platform as a whole, without managing individual clinics' staff or resources.

**Admin Klinik**:
The administrator of a single Klinik, responsible for managing its Layanan, Dokter, staff accounts, and operating hours.

**Resepsionis**:
Front-desk staff at a Klinik who create Appointments from phone calls and Check-In patients on arrival. Can view (read-only) each Dokter's effective schedule via Kalender Audit Jadwal to inform phone bookings, but cannot create, edit, or delete Jadwal, Pengecualian Jadwal, or Jam Operasional — that stays exclusive to Admin Klinik.
_Avoid_: Staf (too broad — use Resepsionis specifically for the front-desk role)

**Staf Layanan**:
Staff assigned to a specific Layanan who progress a patient through the remaining Visit Status stages (menunggu → sedang dilayani → selesai) on the day of the appointment.
_Avoid_: Perawat (clinics use this loosely for the role, but not every Staf Layanan is a nurse)

**Dokter**:
A practitioner at a Klinik who performs Layanan and has their own Jadwal, with a required Jenis Kelamin and Spesialisasi, and an optional Foto and Biografi (see each). See Undangan Akun for how a Dokter gains login access, and Menonaktifkan Dokter for removing one from active use.

**Spesialisasi**:
A Dokter's field of medical practice (e.g. "Umum", "Gigi", "Anak") — a required, single-valued attribute, purely informational and distinct from Layanan: it carries no Harga or Deskripsi and constrains nothing about which Layanan a Dokter may be assigned to. Defined and owned per-Klinik by its Admin Klinik — who may add values, rename one (updates in place across that Klinik's own Dokter), or retire one (blocked while any of that Klinik's Dokter still holds it) — the same per-Klinik shape as Layanan's own catalog (see Layanan). A new Klinik's list is seeded once, at onboarding, from a fixed platform-wide default; from that point on each Klinik's list is entirely independent, with no further syncing and no ongoing Super-Admin role. Shown wherever a Dokter's name appears, including to Patients during booking, but only as passive context there — never a Patient-facing filter or search facet, though Admin Klinik may filter their own Dokter directory by it. See [ADR 0027](docs/adr/0027-spesialisasi-becomes-per-klinik.md), superseding [ADR 0026](docs/adr/0026-spesialisasi-is-platform-wide-informational-attribute.md).
_Avoid_: Spesialis (names the person/role, not the attribute)

**Jenis Kelamin**:
A Dokter's gender — Laki-laki or Perempuan, the same two-value vocabulary already used for Patient. Required, purely informational: shown as passive context wherever a Dokter's name appears (including Admin Klinik's own Dokter directory and the Patient-facing booking picker), never a filter or search facet on either side.

**Foto**:
An optional photo of a Dokter, addable at any time (a Dokter profile is usable without one). Once set, it's Patient-facing — shown wherever a Dokter's name appears during booking, not just to Admin Klinik.
_Avoid_: Avatar, Gambar Profil (Foto is the term already used in the UI)

**Biografi**:
An optional, short Patient-facing description of a Dokter — background, experience, focus areas — shown during booking for the same reason Layanan's Deskripsi is: it helps a Patient decide who to book with.
_Avoid_: Bio (Biografi is the term already used in the UI)

**Undangan Akun**:
How Admin Klinik grants a Resepsionis, Staf Layanan, or Dokter their login access: Admin Klinik invites them by email, and they set their own password via a link in that invitation — Admin Klinik never sets or learns the password itself. Until accepted, the profile exists (e.g. a Dokter can already be assigned Layanan and Jadwal) but has no usable login. See [ADR 0028](docs/adr/0028-staff-invite-sets-own-password.md).
_Avoid_: Registrasi Staf (this is admin-initiated, not self-registration)

**Menonaktifkan Dokter**:
Admin Klinik removing a Dokter from active use (no longer assignable to new Layanan or Jadwal) without deleting their record — existing Jadwal and Appointment entries referencing that Dokter are preserved, the same soft-delete-only treatment Patient already gets. Independent of revoking that Dokter's Undangan Akun-granted login access: neither implies the other, so a Dokter on leave can keep their profile and history while login is revoked, and a Dokter leaving the Klinik can be deactivated while their account technically still exists. See [ADR 0029](docs/adr/0029-dokter-deactivation-and-login-revocation-are-independent.md).
_Avoid_: Menghapus Dokter (deletion isn't how this works)

**Patient**:
A person receiving care from a Dokter, identified across the Platform by phone number and optionally NIK. A Patient never holds credentials or logs in itself — it can be created by a Resepsionis from a phone call, by a Kios walk-in registration, or as a profile a User manages. See User for the account that acts on a Patient's behalf.

**User**:
The account that performs booking actions — as distinct from Patient, who receives the care being booked. A User manages one or more Patient profiles; the first is created automatically, representing the User themself, the moment they register. A User can also add other people they book for — a child, a parent, a coworker — either as a brand-new Patient or, via phone-match verification, an existing one (see Menambahkan Patient); more than one User can end up managing the same Patient (e.g. both of a child's parents), and any of them can independently register their own User account later using that Patient's own phone number, at which point that history links to them too (ADR 0017). A User account is Platform-wide, not scoped to one Klinik — the same login works at any Klinik's booking page, and a User's managed Patients carry over across every Klinik they've booked at, matching Patient's own Platform-wide identity. A phone number can be shared across several Patient records (see Patient), but is unique to exactly one User — it is the one field OTP-verifies a User's identity, so a signup or login attempt with an already-claimed number succeeds into that existing account once the OTP for that phone is confirmed, regardless of which entry point (Google SSO, guest conversion) triggered it. See [ADR 0024](docs/adr/0024-user-accounts-platform-wide-unique-phone.md). Login is via Google SSO or email + password; a User sets a password whenever they choose to (never required at registration), and Google SSO doesn't require one at all. Signing in with Google under an email that matches an existing email+password User logs into that same account rather than creating a second one. See [ADR 0015](docs/adr/0015-introduce-user-distinct-from-patient.md).

There is no separate "register" entry point distinct from booking as a guest: a phone-only User account is created by converting a completed guest booking (see Booked By) with a single tap — the phone is already OTP-verified from the booking itself, and email isn't collected at that moment either, per ADR 0015's "collected but can be verified later." Google SSO, by contrast, self-registers on first use. See Perangkat Terpercaya for what happens at that first login going forward.
_Avoid_: Akun Pasien (retired term — see ADR 0015 for why)

**Perangkat Terpercaya**:
The trust a device earns from a User's phone OTP the first time they log in on it — after that, the same device can log in again (Google session or email+password) without another OTP, until the User logs out or moves to a different device. This supersedes ADR 0015's original framing that Google SSO's phone verification "can be deferred to the User's first Appointment": phone verification now always happens at that first login itself, on whichever device it occurs. See [ADR 0023](docs/adr/0023-phone-otp-once-per-device-not-every-login.md).
_Avoid_: Remembered device, trusted session (this is device-scoped, not just a time-boxed session)

**Menambahkan Patient**:
How a User adds someone to the set of Patients they manage, the *first* time — always by entering that person's details as if creating new, never by searching or browsing existing Patient records, which would leak whether a stranger is a patient here at all. If the details strongly match an existing Patient (the same threshold as Patient Matching, ADR 0004/0019), an OTP goes to that Patient's phone before the two are linked; unconfirmed, a separate Patient is created instead and the match falls into the ordinary Duplicate Candidate queue. See [ADR 0020](docs/adr/0020-adding-a-patient-never-exposes-live-search.md). This is distinct from booking for a Patient the User already manages — that's a plain pick from the User's own list, no search-safety concern, since it's the User's own data.

### Scheduling & Appointments

**Layanan**:
A type of medical service offered by a Klinik (e.g. general checkup, vaccination), with its own duration and set of Dokter *eligible* to perform it. Defined independently per Klinik — there is no Platform-wide service catalog. Eligibility doesn't imply concurrency: a Layanan is a single exclusive resource (one service room, in effect) — only one of its eligible Dokter may hold a Jadwal block for it at any given moment. See Jadwal, [ADR 0022](docs/adr/0022-layanan-is-a-single-exclusive-resource.md). Harga is a single, mandatory price per Layanan (not per-Dokter or per-Patient-category) — see [ADR 0025](docs/adr/0025-harga-is-informational-and-locked-at-booking.md). Deskripsi is a short, Patient-facing blurb about what the Layanan is (not clinical/procedural documentation), optional so a Layanan can be created quickly and described later.
_Avoid_: Service, Treatment

**Jadwal**:
A Dokter's recurring weekly availability template, made of blocks each scoped to exactly one Layanan the Dokter performs during it. No two blocks may overlap in time — neither two of the same Dokter's own blocks across different Layanan (a Dokter can only be in one place at a time), nor two different Dokter's blocks for the same Layanan (a Layanan is a single exclusive resource; see Layanan). From this exclusivity-checked template, bookable Slot are generated. See [ADR 0022](docs/adr/0022-layanan-is-a-single-exclusive-resource.md).
_Avoid_: Schedule, Availability

**Pengecualian Jadwal**:
A one-off override to a Dokter's Jadwal for a specific date, taking precedence over the recurring template. Comes in exactly two kinds — see Cuti and Jam Khusus. At most one Pengecualian Jadwal may exist per Dokter per date; changing it (e.g. Cuti → Jam Khusus for the same date) means deleting the old one and creating a new one, not editing a shared record in place. Only ever creatable for today or a future date, never backdated — a past no-show or unplanned absence is instead recorded via the Appointment's own Visit Status/Appointment Status, not a retroactive Pengecualian Jadwal.

**Cuti**:
A Pengecualian Jadwal marking a Dokter fully unavailable for a date — bypasses Jam Operasional entirely, unlike Jam Khusus. May be submitted in bulk across a date range as a matter of data-entry convenience, but each resulting date is its own independent Pengecualian Jadwal record from the moment it's created (individually editable/deletable) — there is no "leave period" entity remembering the batch. Bulk creation is Cuti-only.

**Jam Khusus**:
A Pengecualian Jadwal replacing a Dokter's usual Jadwal for one date, for one Layanan, with a different start/end time (e.g. shortened hours) — unlike Cuti, still bounded by the Klinik's Jam Operasional for that date, the same as the recurring Jadwal is; entering hours outside Jam Operasional produces a warning rather than being rejected. Subject to the same per-Layanan, per-Dokter exclusivity as Jadwal (see Jadwal) — an override date is not a loophole around it. Always entered per-date, never in bulk — a recurring pattern of special hours belongs in the Jadwal template itself.

**Jam Operasional**:
A Klinik's own opening hours and holiday calendar, independent of any single Dokter's Jadwal. A Slot is only bookable when it falls within both the Klinik's Jam Operasional and the relevant Dokter's Jadwal. Editing any of Jadwal, Pengecualian Jadwal, or Jam Operasional in a way that would orphan an already-`confirmed` Appointment requires an explicit, reasoned admin override rather than silently dropping or moving that Appointment — see [ADR 0018](docs/adr/0018-jadwal-conflict-flags-appointment-for-reschedule.md). Because Jam Operasional is Klinik-wide, its conflict check aggregates affected Appointments across every Dokter into one review, unlike a Jadwal/Pengecualian Jadwal edit's single-Dokter scope — see [ADR 0021](docs/adr/0021-jam-operasional-conflict-check-spans-all-dokter.md).

**Kalender Audit Jadwal**:
A read-only, Klinik-wide calendar view across every Dokter's effective schedule (Jadwal, Pengecualian Jadwal, and Jam Operasional combined), including past dates — distinct from the per-Dokter, current-week-onward-only view an Admin Klinik uses to actually edit a Jadwal.

**Slot**:
A discrete, bookable unit of time for one Dokter, sized to the duration of a specific Layanan. Because a Layanan is a single exclusive resource (see Layanan), two different Dokter's Slots for the same Layanan may never overlap in time — enforced at booking time as a backstop, not only when Jadwal is authored.

**Slot Hold**:
The Slot claim held by an Appointment sitting at Appointment Status `pending` — the moment a Slot is selected and identity submitted, an Appointment is created at `pending` and occupies the Slot exactly like a `confirmed` one would, so no other booking can take it. Enforced server-side: a scheduler cancels the Appointment if OTP isn't confirmed within a per-Klinik-configurable duration (default 5 minutes, bounded 2-15 minutes), releasing the Slot. See [ADR 0016](docs/adr/0016-slot-hold-enforced-via-pending-status-and-scheduler.md), which supersedes the client-side-only framing in the original [ADR 0014](docs/adr/0014-slot-hold-during-booking.md).

**Appointment**:
A reservation of one Slot with one Dokter for one Layanan, made for exactly one Patient. See Booked By for who performed the reservation. Also locks in the Layanan's Harga at the moment the Appointment is created, the same way Booked By is fixed at creation — if Harga changes later, already-created Appointments keep showing what they were quoted. A rescheduled Appointment (see Reschedule) is a new Appointment, so it locks in whatever Harga is current at that later moment, not the original booking's price. See [ADR 0025](docs/adr/0025-harga-is-informational-and-locked-at-booking.md).
_Avoid_: Booking (use only as the verb for creating an Appointment, not as the noun for the record), Reservation

**Booked By**:
The actor who created an Appointment: a guest Patient (self-service, no User account), a User (booking for one of the Patients they manage), or a Resepsionis (phone booking). Distinct from Booking Source, which records the channel (`online`/`phone`), not the actor — an `online` Appointment can be Booked By either a guest Patient or a User. Fixed at the moment the Appointment is created and never rewritten afterward — if a guest Patient later converts to a User (see User), their past Appointments stay Booked By "guest Patient" even though the same User can now see them, since Booked By records what happened, not who the actor is today. See [ADR 0015](docs/adr/0015-introduce-user-distinct-from-patient.md).

**Booking Source**:
The channel through which an Appointment was created: `online` (self-service, by a guest Patient or a User) or `phone` (Resepsionis entered it from a phone call).

**Generic Booking Link**:
A booking link that starts the Patient at a Klinik's default booking flow with no pre-filled selections.

**Deep Link**:
A booking link pre-filled with a specific Layanan and/or Dokter, generated by a Resepsionis to send a Patient via WhatsApp.

**Appointment Status**:
The lifecycle disposition of an Appointment: `pending`, `confirmed`, `completed`, `no-show`, `cancelled`, `rescheduled`, or `perlu_dijadwal_ulang`. Distinct from Visit Status, which tracks a Patient's physical progress through a visit on the appointment day. `pending` and `perlu_dijadwal_ulang` are non-terminal, alongside `confirmed`: a `pending` Appointment becomes `confirmed` once OTP is verified, or `cancelled` by the Slot Hold scheduler if it isn't (see [ADR 0016](docs/adr/0016-slot-hold-enforced-via-pending-status-and-scheduler.md)); a `perlu_dijadwal_ulang` Appointment is resolved by staff into `rescheduled` or `cancelled` (see [ADR 0018](docs/adr/0018-jadwal-conflict-flags-appointment-for-reschedule.md)).

**Reschedule**:
Changing an Appointment's time by marking the original Appointment `rescheduled` and creating a new Appointment that references it — never a mutation of the original Appointment's time in place.

**Keluhan**:
An optional free-text description of the Patient's reason for visit, captured alongside identity at booking time so the Dokter can prepare ahead of the Appointment.
_Avoid_: Chief complaint, alasan kunjungan

### Visit Tracking

**Visit Status**:
The real-time stage of a Patient's presence at the Klinik on the day of their Appointment: `belum tiba` → `menunggu` → `sedang dilayani` → `selesai`. Distinct from Appointment Status. (An earlier design considered a `telah tiba` stage between `belum tiba` and `menunggu`; see [ADR 0007](docs/adr/0007-merge-telah-tiba-into-menunggu.md) for why it was merged away.)

**Check-In**:
A Patient's Visit Status being marked as `menunggu` on arrival at the Klinik — either manually by a Resepsionis, or by the Patient themselves via **Self Check-In** at a Kios. See [ADR 0012](docs/adr/0012-self-check-in-via-kios.md).

**Self Check-In**:
A Patient performing their own Check-In by scanning their Kode Check-In at a Kios, with no Resepsionis involved. Writes Visit Status directly to `menunggu` — the same single-step transition a Resepsionis's Check-In performs, so [ADR 0007](docs/adr/0007-merge-telah-tiba-into-menunggu.md)'s reasoning still holds; Self Check-In is a second way to trigger that transition, not a new stage. Only available when the Appointment needs no exception handling (e.g. nothing has changed since booking) — anything else is routed to a Resepsionis instead. Assumes the Appointment already exists: a Patient without one (walk-in) must be registered by a Resepsionis first, since a Kios never creates an Appointment. A Kode Check-In identifies exactly one Appointment — a Patient with two Appointments the same day has two separate codes and scans once per Appointment, mirroring the Visit Status Board's one-card-per-Appointment rule. Valid any time within the Klinik's Jam Operasional that day, with no "too early" cutoff, matching a Resepsionis's own unrestricted timing. If the Appointment is no longer eligible (already resolved away by Tutup Layanan, or already past `menunggu`), the Kios shows an error and directs the Patient to a Resepsionis — it never reverts or overrides Visit Status itself.
_Avoid_: Kiosk Check-In (Kios is the device; Self Check-In is the act)

**Kode Check-In**:
A QR code generated for an Appointment at the moment it's created, used to perform Self Check-In. Delivered via WhatsApp (extending the Deep Link delivery channel) when the Appointment's Booking Source is `phone`; when `online`, shown immediately on the booking confirmation screen for a guest Patient, or in the User's booking history for a User-booked Appointment.

**Kios**:
A self-service device at a Klinik where a Patient scans their Kode Check-In to perform Self Check-In. Bound to exactly one Klinik. Confirms a scan by showing the Patient's name for the Patient to confirm — no further identity input is required. On success, shows the Patient's name, the Layanan they're checked in for, and an estimated Waktu Tunggu — nothing is printed or sent elsewhere; it's a live screen, not a ticket.

**Kanal Check-In**:
Which path performed a given Check-In: `resepsionis` or `kios` (Self Check-In). Recorded on the Appointment for audit and board display only — it doesn't change [ADR 0009](docs/adr/0009-visit-status-revert-requires-dokter-pin.md): reverting a Check-In is always a Resepsionis action, regardless of which Kanal Check-In performed it.

**Visit Status Board**:
The kanban view of an Appointment's Visit Status, scoped to the viewing staff's own Klinik and its current operating day (per Jam Operasional). One card represents one Appointment, not one Patient — a Patient with two Appointments the same day appears as two independent cards. `cancelled` and `rescheduled` Appointments never appear on the board; an Appointment marked `no-show` is removed from it immediately. A Resepsionis sees one combined board across all of the Klinik's Layanan; a Staf Layanan sees only the board for their own Layanan. Each Layanan's board header shows: the Layanan's name, its on-duty Staf Layanan (one assigned per shift, not per-Appointment), the Dokter(s) currently on duty for that Layanan today, and the Klinik's current local date/time with its timezone label (WIB/WITA/WIT).
_Avoid_: Papan Pasien, Patient board (the board tracks Appointments, not Patients — a Patient can appear more than once)

**Durasi Layanan**:
The elapsed time since an Appointment entered `sedang dilayani`, stopping once it reaches `selesai`. Shown per card, in that column only — unlike Waktu Tunggu, it is not part of the Visit Status Board's KPI bar.

**Kapasitas Dokter**:
A Dokter may have at most one Appointment in `sedang dilayani` at any moment — a Dokter is a person, not a queue slot. Checked across the whole Klinik, not just one Layanan, since a Dokter could in principle serve more than one Layanan the same day. "Mulai Layani" is blocked for any other Appointment booked with a Dokter who is already serving someone else. A booked Dokter who isn't actually on duty (sick, covered by a substitute) is a known gap — reassigning an Appointment to a different Dokter isn't supported yet.

**Interval Pembaruan Papan**:
A per-Klinik setting controlling how often the Visit Status Board polls for updates. Configured by Admin Klinik; defaults to 15 seconds, bounded to 5–60 seconds.

**Target Waktu Tunggu**:
A per-Klinik setting for the wait considered reasonable before a `menunggu` card is flagged (shown in red). Configured by Admin Klinik; a fixed target, not the board's live-computed average — comparing against the live average would make the flag move whenever the average did, regardless of whether that patient's own wait changed.

**Pembatalan Visit Status**:
A single-step revert of an Appointment's Visit Status to the immediately previous stage. Reverting among the Dokter-owned stages (`menunggu` ↔ `sedang dilayani` ↔ `selesai`) requires the assigned Dokter's PIN, entered on the acting Staf Layanan's screen. Reverting a Check-In (`menunggu` → `belum tiba`) is a Resepsionis action and needs no Dokter involvement — it corrects a front-desk entry, not a clinical record.

**Tutup Layanan**:
An end-of-day action performed by a Layanan's own Staf Layanan, closing that Layanan's queue for the day. Refuses to run while any Appointment in that Layanan is still `sedang dilayani` (must first be moved to `selesai` or reverted). On success, sets Appointment Status to `no-show` for any Appointment still `belum tiba`, and to `cancelled` for any Appointment still `menunggu` — removing both from the Visit Status Board. The Klinik-wide daily board reset only happens once every Layanan at that Klinik has run Tutup Layanan.

**Waktu Tunggu**:
The duration since an Appointment's Check-In (i.e. since it entered `menunggu`), frozen the moment Visit Status reaches `sedang dilayani`. Drives both the per-card wait display and the Visit Status Board's average-wait KPI.
_Avoid_: Wait time counted through service — it measures pre-service waiting only.

### Patient Identity & Data

**NIK**:
Nomor Induk Kependudukan — Indonesia's national ID number. An optional Patient field, used as the highest-confidence identifier for Patient Matching when present. Storing it requires the Patient's explicit consent, captured at the same point NIK is entered — see [ADR 0013](docs/adr/0013-nik-consent-at-point-of-entry.md).

**Patient Matching**:
The process of determining whether two Patient records represent the same real person. When both records have a NIK: an exact match is certain (auto-merge), a differing NIK is certain proof of two different people — no Duplicate Candidate is raised, even if every other signal matches. Otherwise, at least 2 of 3 fuzzy signals (phone number, name allowing small typos, date of birth) must match to raise a Duplicate Candidate. See [ADR 0019](docs/adr/0019-patient-matching-fuzzy-threshold-and-nik-mismatch.md), extending [ADR 0004](docs/adr/0004-patient-dedup-auto-merge-only-on-nik.md).

**Duplicate Candidate**:
A pair of Patient records flagged by Patient Matching as possibly the same person, pending manual review by a Resepsionis or Admin Klinik. Never merged automatically unless the match was on NIK.

**Patient Merge**:
Consolidating two Patient records confirmed (by NIK match or manual review) to represent the same person into one.
