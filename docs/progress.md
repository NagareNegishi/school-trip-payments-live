<!-- Update convention: read at session start; update after each major feature
     completes. One line per decision — state the decision, skip the reasoning. -->

School payments: FastAPI + React + Postgres app where a parent registers a child for a school trip and pays through a mock legacy processor. Backend and frontend done (Steps 1-8); README and `AI_WORKINGS.md` done (Step 9). Step 10 done: live at https://school-trips-web.onrender.com (Render backend and static site, Neon database). Browser check done.

## Key Decisions

### Backend
- Unknown `student_id` or `activity_id` on `POST /registrations` returns 404; the plan lists only 409 and 422.
- A repeated child name under one parent returns 409 `student_exists`; a new child goes to the first school.
- A declined or crashed payment returns 402 `payment_declined` in the shared error shape; the attempt is stored as `failed`.
- `POST /registrations/{id}/payments` and `GET /payments/{id}` both return the receipt data (`ReceiptOut`); `GET` returns 404 unless the payment succeeded.
- Endpoint tests run inside a rolled-back outer transaction (`tests/conftest.py`), so the dev database stays untouched.

## In Progress — pick up next session

- Open items are in `rapid-prototypes-plan.md`
