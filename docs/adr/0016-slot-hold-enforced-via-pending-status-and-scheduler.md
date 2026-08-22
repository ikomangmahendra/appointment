---
status: supersedes ADR 0014 (same problem, enforced server-side instead of client-side)
---

# Slot Hold is enforced via a `pending` Appointment Status and a backend scheduler

ADR 0014 described Slot Hold as a claim that exists before the Appointment record itself does, protected only by client-side countdown UI. That's not durable — a page refresh or a client that never runs the countdown wouldn't actually protect the Slot. Instead, an Appointment is created immediately at Appointment Status `pending` the moment a Slot is selected and identity is submitted, occupying the Slot exactly as a `confirmed` one would. A backend scheduler cancels any `pending` Appointment whose OTP isn't confirmed within the hold duration — still the same per-Klinik-configurable setting from ADR 0014 (default 5 minutes, bounded 2-15 minutes) — which frees the Slot for booking again. Confirming OTP in time transitions the Appointment `pending` → `confirmed`.

This is the same guarantee ADR 0014 was after, just enforced where it can't be bypassed by the client. The countdown UI built for ADR 0014 stays as a courtesy to the Patient (so they know their time budget), but it no longer does the actual enforcement.
