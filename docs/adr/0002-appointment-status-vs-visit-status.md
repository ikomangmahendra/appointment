# Split Appointment lifecycle into Appointment Status and Visit Status

An Appointment carries two independent status fields instead of one combined enum: Appointment Status (`confirmed`/`completed`/`no-show`/`cancelled`/`rescheduled`) for the booking's final disposition, and Visit Status (`belum tiba`/`telah tiba`/`menunggu`/`sedang dilayani`/`selesai`) for real-time day-of tracking on the clinic's kanban board.

Visit Status reaching `selesai` automatically sets Appointment Status to `completed`, but `no-show` is always set manually by staff — an Appointment sitting at `belum tiba` past its scheduled time is never auto-marked no-show, since patients often just arrive late. Keeping the two dimensions separate avoids conflating "did the booking happen" with "where is the patient right now."
