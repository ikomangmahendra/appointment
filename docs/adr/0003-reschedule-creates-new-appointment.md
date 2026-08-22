# Reschedule creates a new Appointment, never mutates the original

Rescheduling always creates a new Appointment referencing the original, rather than changing the original Appointment's time in place. The original is marked `rescheduled` (a terminal Appointment Status) and keeps its original Slot on record; the new Appointment starts fresh as `confirmed`/`belum-tiba`. This preserves a clear history of the change instead of overwriting "what was originally booked."

Only legal while the original's Visit Status is still `belum-tiba` — once a Pasien has arrived, the booking can no longer be moved, only cancelled.

## Consequences

Each Appointment can hold an optional link in each direction: a `rescheduled`-status Appointment points to its **Rescheduled-Into Appointment**; the new Appointment points back to its **Original Appointment**.
