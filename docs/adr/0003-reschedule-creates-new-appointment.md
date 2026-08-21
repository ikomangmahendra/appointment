# Reschedule creates a new Appointment instead of mutating the original

Rescheduling marks the original Appointment's status as `rescheduled` and creates a new Appointment referencing it, rather than updating the original's time in place. This preserves a clean audit trail of what was originally booked and when it changed — needed for clinic reporting and eventually for SATU SEHAT-aligned history — at the cost of an Appointment no longer being a single stable record across time changes.
