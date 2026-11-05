# Direction & deferrals

checked: 2026-10-26

Where things are heading and what is deliberately not being done. The "why" is the
durable part. Verify status against `docs/progress.md`, `docs/plans/`, and issues.

## Heading toward

Nothing. This repo is a copy of the 4-hour build and gets no further features. The browser check and the Render deploy are done -> `docs/plans/manual-check.md`, `docs/school-payments/impl.md`

## Deferred but real

- A seeded trip date is set once, 30 days after the first start; a database kept longer has no upcoming trip -> `README.md`
- No reconciliation if the processor approves and the database commit then fails -> `README.md`

## Deliberately deferred (why)

- Login: a header dropdown stands in, to keep the first round small -> `docs/school-payments/product.md`
- Registration expiry, cancellation and refunds: out of the first round -> `docs/school-payments/product.md`
- Many schools: one school for now, so a new child goes to the lowest-numbered school -> `backend/app/routers/parents.py`

## Not in scope for the product

- Real card charging: the processor is a mock -> `README.md`
- Anything outside the implementation plan -> `CLAUDE.md` "Project rules"

## Where the live plans are

- `docs/school-payments/` = the plan docs for this app: product, implementation plan, impl steps, deploy guide -> `docs/school-payments/`
- `docs/plans/` = per-feature plan files; only the manual check lives there now -> `docs/plans/`
- This file indexes the live plans; it does not replace them.

## Notes for a reader

- `README.md` is the current description of the app -> `README.md`
