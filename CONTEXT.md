# Klinik Appointment

Multi-tenant clinic appointment booking and visit-tracking platform. Patients book an Appointment with a Klinik online or by phone; on the day of the visit, staff track same-day progress on a kanban board.

## Language

**Appointment Status**:
The lifecycle field on an Appointment: `confirmed`, `completed`, `no-show`, `cancelled`, or `rescheduled`. All but `confirmed` are terminal — once set, Visit Status is frozen and no further transitions are legal.

**Visit Status**:
The same-day kanban progress field on an Appointment while its Appointment Status is `confirmed`: `belum-tiba` → `telah-tiba` → `menunggu` → `sedang-dilayani` → `selesai`. Reaching `selesai` automatically sets Appointment Status to `completed`.
_Avoid_: "check-in status" for the field as a whole — Check-In is one specific transition, not the whole field.

**Check-In**:
The staff action that moves Visit Status from `belum-tiba` to `telah-tiba` when a Pasien arrives at the clinic.

**No-Show**:
An Appointment Status set manually by staff when a Pasien never arrives (Visit Status stays `belum-tiba`). Never set automatically by the system, even after the appointment time has passed.

**Cancel**:
An Appointment Status set when an Appointment is called off before service starts. Legal any time before `sedang-dilayani`, including after the Pasien has checked in and is waiting — the "arrived, then left" case. Distinct from No-Show, which only applies when the Pasien never arrived at all.
_Avoid_: using Cancel and No-Show interchangeably for "patient wasn't seen" — they encode different facts (did the patient arrive?).

**Reschedule**:
An Appointment Status set only while Visit Status is still `belum-tiba`, recording that the Pasien moved this booking to a different Slot. Creates a new Appointment; never changes the original Appointment's time in place.

**Original Appointment**:
From a rescheduled-into Appointment's point of view, the Appointment it replaced.

**Rescheduled-Into Appointment**:
From an Appointment marked `rescheduled`'s point of view, the new Appointment created in its place.
