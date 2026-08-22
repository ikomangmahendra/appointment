# Appointment Status and Visit Status are separate fields

Appointment lifecycle (`confirmed`/`completed`/`no-show`/`cancelled`/`rescheduled`) and same-day visit progress (`belum-tiba`/`telah-tiba`/`menunggu`/`sedang-dilayani`/`selesai`) are modeled as two independent fields rather than one combined status, because they change at different rates and for different reasons: Visit Status moves several times in a single day as staff tap through the kanban board, while Appointment Status changes at most once. Reaching `selesai` automatically sets Appointment Status to `completed`; every other Appointment Status transition is manual — in particular, No-Show is never inferred from a missed appointment time.

Validated in a throwaway prototype (`prototype/appointment-visit-status` branch, `prototypes/appointment-visit-status.prototype.html`) against the full transition table below, including "patient arrives then leaves" (Cancel stays legal after Check-In, up until service starts) and "reschedule/no-show become illegal once the patient has arrived."

## Transition table

| Action | Precondition | Effect |
|---|---|---|
| Check-In | Appointment Status = confirmed, Visit Status = belum-tiba | Visit Status → telah-tiba |
| Move to Menunggu | confirmed, telah-tiba | Visit Status → menunggu |
| Start Service | confirmed, menunggu | Visit Status → sedang-dilayani |
| Complete Service | confirmed, sedang-dilayani | Visit Status → selesai; Appointment Status → completed (automatic) |
| Mark No-Show | confirmed, belum-tiba | Appointment Status → no-show (always manual) |
| Cancel | confirmed, Visit Status ∈ {belum-tiba, telah-tiba, menunggu} | Appointment Status → cancelled |
| Reschedule | confirmed, belum-tiba only | Appointment Status → rescheduled; new Appointment created (see [ADR-0003](./0003-reschedule-creates-new-appointment.md)) |

Any action outside its precondition is rejected with a reason, never silently ignored.

## Consequences

- **No backward/undo transition.** Correcting a mis-click (e.g. the wrong patient checked in) is an operational/admin concern, not a domain transition.
- **No clinic-initiated interruption mid-visit.** Both Cancel and Reschedule become illegal once service has started (`sedang-dilayani`); there is no transition for "doctor emergency, visit can't finish today." Treated as rare enough to handle manually outside the system rather than modeled.
- **Role authorization is out of scope here.** Which role may invoke which transition is a cross-cutting access-control concern, not part of this state machine.
