# Patient Mobile Booking — Prototype

Answers: "What should the patient-facing appointment booking flow look like on a
smartphone browser?"

Single static HTML file, no backend, no build step, no persistence:
`patient-booking-mobile.prototype.html`.

## How to open it

No server needed — just open the file directly in a browser:

- **Desktop**: double-click the file, or drag it into a browser window, or
  `open patient-booking-mobile.prototype.html` (macOS) /
  `xdg-open patient-booking-mobile.prototype.html` (Linux).
- **Phone**: AirDrop/transfer the file to the device and open it, or serve the
  folder over your LAN (`python3 -m http.server 8934` from this directory)
  and browse to `http://<your-machine-ip>:8934/patient-booking-mobile.prototype.html`.
- Narrow the browser window (or use dev tools' device toolbar) to a phone
  width — the layout is designed for that viewport.

This repo/branch lives at `prototype/patient-mobile-booking`, checked out in
its own worktree — it is not on `master`, so it won't show up if you're
browsing the main `appointment` working directory.

## Deep link

To simulate arriving via a deep link (see `CONTEXT.md`), add:
`?layanan=gigi&dokter=bagus`

## Verdict

**Wizard Bertahap** (linear step-by-step) is the pick, over the
accumulating-scroll accordion and the calendar/bottom-sheet variants that
were tried and dropped. Carry this structure into the real Angular booking
flow when it's built.
