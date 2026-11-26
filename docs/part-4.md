# Part 4 - Notifications, Admin and DevOps (Nourhan)

Covers FR-20 to FR-25 and FR-28. Story IDs follow `US-<FR number>-<letter>`.
Priorities come from the brief (Must / Should / Could).

## 1. User stories

### FR-20 - Support payment search (Must)

**US-20-a: Search a payment by reference** - *Must*
As a support employee, I want to search for a payment by its reference so that I can answer a customer's complaint quickly.

- **Given** a payment with reference `PL-1001` exists, **when** I search for `PL-1001`, **then** the system returns that payment with its amount, fee, biller, customer and current state.
- **Given** no payment has reference `PL-9999`, **when** I search for it, **then** the system returns "not found" and no other payment's data.
- **Given** I am logged in as a customer (not support), **when** I call the search endpoint, **then** the request is rejected with 403.

**US-20-b: See the full history of a payment** - *Must*
As a support employee, I want to see every state change of a payment so that I can explain to the customer what happened.

- **Given** payment `PL-1002` went Initiated -> FundsHeld -> SentToBiller -> Unknown -> Completed, **when** I open it, **then** I see all 5 states in order with the time of each change.
- **Given** the payment has money movements (hold, charge, fee, commission), **when** I open it, **then** I see the related ledger entries.
- **Given** the payment has notifications, **when** I open it, **then** I see each message and its delivery status (sent / undelivered).

### FR-21 - Block and unblock accounts (Should)

**US-21-a: Block an account** - *Should*
As a support employee, I want to block an account so that a suspected fraudster cannot pay.

- **Given** an active account, **when** I block it with a reason, **then** its status becomes Blocked.
- **Given** a Blocked account, **when** its owner starts a payment, **then** the payment is Rejected with the reason "account blocked" and no money is held.
- **Given** I block an account, **when** the action completes, **then** an audit record is written with my user, the time and the reason.

**US-21-b: Unblock an account** - *Should*
As a support employee, I want to unblock an account so that a wrongly blocked customer can pay again.

- **Given** a Blocked account, **when** I unblock it, **then** its status becomes Active and the next valid payment is accepted.
- **Given** the account was blocked automatically by the velocity rule (FR-19), **when** I unblock it before the 10 minutes end, **then** it becomes Active immediately.
- **Given** I unblock an account, **when** the action completes, **then** an audit record is written.

### FR-22 - Payment notifications (Must)

**US-22-a: Notification on completion** - *Must*
As a customer, I want an SMS and a push message when my payment completes so that I have proof of payment.

- **Given** a payment moves to Completed, **when** the state change is saved, **then** one SMS and one push message are created containing the amount, biller, fee and reference.
- **Given** the payment completed after being Unknown (late callback or settlement), **when** it becomes Completed, **then** the customer still gets the message, once.

**US-22-b: Notification on failure** - *Must*
As a customer, I want a clear message when my payment fails so that I know my money is safe and what to do.

- **Given** AquaCo refuses a payment with an error code, **when** the payment becomes Failed, **then** the customer gets an SMS with a clear reason (not the raw error code).
- **Given** the payment is Rejected by a fraud rule, **when** it is rejected, **then** the customer is told the reason (limit exceeded, too many payments, blocked).
- **Given** the notification provider is down, **when** a payment completes or fails, **then** the payment result is not affected (see US-23-a).

### FR-23 - Notification retry (Should)

**US-23-a: Retry failed messages** - *Should*
As the company, I want failed SMS and email to be retried so that temporary provider problems do not lose messages.

- **Given** the SMS provider returns an error, **when** the first attempt fails, **then** the system retries up to 3 more times (retry count and delay come from configuration).
- **Given** a retry succeeds, **when** the provider accepts the message, **then** the status becomes Sent and no more retries happen.
- **Given** the payment is Completed, **when** all notification attempts fail, **then** the payment stays Completed.

**US-23-b: Mark undelivered** - *Should*
As a support employee, I want messages that never arrived to be marked undelivered so that I can contact the customer another way.

- **Given** the 3 retries all failed, **when** the last one fails, **then** the message status becomes Undelivered and no further attempt is made.
- **Given** an Undelivered message, **when** I view the payment (US-20-b), **then** I see the status and the last error.

### FR-24 - Editable fees and limits (Should)

**US-24-a: Edit fee values** - *Should*
As an admin, I want to change fee values without redeploying so that business can react quickly.

- **Given** the electricity fee is 2.50 EGP, **when** I change it to 3.00 EGP, **then** new payments use 3.00 and payments already created keep their original fee.
- **Given** I enter a negative or non-numeric fee, **when** I save, **then** the change is rejected with a validation message.
- **Given** I set a promotion with start and end dates for one biller, **when** a payment happens inside the period, **then** its fee is 0.

**US-24-b: Edit limits** - *Should*
As an admin, I want to change daily and single-payment limits so that limits follow company policy.

- **Given** the basic daily limit is 5,000 EGP, **when** I change it to 4,000 EGP, **then** the next fraud check uses 4,000.
- **Given** minimum is greater than maximum, **when** I save, **then** the change is rejected.
- **Given** any fee or limit change, **when** it is saved, **then** an audit record stores the old value, new value, admin and time.

### FR-25 - Audit log (Must)

**US-25-a: Audit every money movement** - *Must*
As an auditor, I want every money movement recorded with who, when and why so that we can prove the books are correct.

- **Given** any ledger entry is created (hold, release, charge, fee, commission, top-up, refund), **when** it is saved, **then** an audit record exists with actor, time, reason and payment reference.
- **Given** the audit write fails, **when** the money movement is being saved, **then** the whole movement is rolled back (no movement without an audit record).
- **Given** an audit record exists, **when** anyone tries to edit or delete it, **then** the system refuses (append only).

**US-25-b: Audit admin and support actions** - *Must*
As an auditor, I want admin and support actions recorded so that nobody can change settings silently.

- **Given** an admin edits a biller, fee or limit, or support blocks/unblocks an account, **when** the action succeeds, **then** an audit record stores who, what, old and new values, and time.
- **Given** I search the audit log by user or date range, **when** records match, **then** they are returned in time order.

### FR-28 - Daily totals (Could)

**US-28-a: View daily totals** - *Could*
As an admin, I want daily totals so that I can follow the business.

- **Given** completed payments exist today, **when** I open the daily report, **then** I see the number and sum of payments, fees earned and commissions paid.
- **Given** some payments failed, **when** I open the report, **then** I see failures per biller.
- **Given** I choose another date, **when** I request it, **then** the totals match the ledger for that date (same numbers as the ledger sum).
- **Given** no payments on that date, **when** I request it, **then** all totals are 0, not an error.

## 2. Diagram

Use-case diagram for the whole system (source: `docs/design/use-case.puml`, PlantUML).
Actors: Customer, Agent, Support Employee, Admin, Biller, Notification provider and the Scheduler.
My use cases (FR-20 to FR-25, FR-28) sit in the "Support & Admin" and "Notifications" packages.

## 3. Extra: Docker and GitHub Actions

### Docker
- `Dockerfile`: Python 3.12 slim, installs `requirements.txt`, runs `uvicorn` on port 8000.
- `docker-compose.yml`: two services, `db` (PostgreSQL 16, with a health check) and `app` (waits for the database to be healthy, has its own `/health` check). Data is kept in a named volume.
- The database password comes from an environment variable with a dev default; no real secrets are committed.
- Run: `docker compose up`, then open `http://localhost:8000/health` and `http://localhost:8000/docs`.
- Mock billers are added to the same compose file in Phase 2.

### GitHub Actions (`.github/workflows/ci.yml`)
On every push and pull request:
1. install dependencies
2. lint with `ruff`
3. run `pytest` with coverage (target 70% on domain logic in Phase 2)
4. build the Docker image
5. second job: `docker compose up --wait`, call `/health`, then shut down.

Phase 2 adds: deploy on `main` to a local runner, a rollback step, and a coverage gate.

### Repository rules (set in GitHub settings)
- Protect `main`: require a pull request, at least 1 approval, and the CI check to pass.
- Nobody pushes directly to `main`.

## 4. Questions and decisions for my part

| # | Question / ambiguity | Decision |
|---|---|---|
| 1 | FR-23 says "retried 3 times". Is that 3 retries after the first attempt or 3 attempts in total? | 1 first attempt + 3 retries; the number is configurable. |
| 2 | Who can read the audit log? | Admin only; support can read history of a single payment only. |
| 3 | Does a fee edit change payments already in progress? | No. The fee is saved on the payment when it is created. |
| 4 | Are audit records ever deleted? | No (append only). |
| 5 | Which roles can call support endpoints? | Role-based: customer, agent, support, admin. |
