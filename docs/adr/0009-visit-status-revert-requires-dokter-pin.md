# Reverting a Visit Status transition requires Dokter PIN approval for clinical stages

A Visit Status transition can be reverted one step at a time, back to the immediately previous stage only — not an arbitrary jump. Reverting among the Dokter-owned stages (`menunggu` ↔ `sedang dilayani` ↔ `selesai`) requires the assigned Dokter's PIN, entered on the acting Staf Layanan's device: undoing "this Dokter treated this patient" needs the Dokter's own attribution, not a Staf Layanan self-correction with no accountability trail. Reverting a Check-In (`menunggu` → `belum tiba`) has no such requirement — it corrects a front-desk data-entry slip with no clinical content, and requiring Dokter approval there would add friction for no accountability benefit.

Full history corrections beyond one step back (e.g. undoing a `selesai` from an hour ago) are an Admin Klinik data-fix, not a board action.
