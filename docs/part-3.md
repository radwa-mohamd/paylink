# Part 3 – Biller Integration and Reconciliation

**Author:** Jana
**Modules:** Biller Integration, Reconciliation (extra)

## 1. Scope

**Biller Integration** hides each biller's protocol behind one adapter interface: ElectroCo (REST/JSON, fast), AquaCo (XML over HTTP, slow, own error codes), TopUpNet (REST/JSON, asynchronous callback), NetCo (daily batch CSV, optional). It owns timeouts, retries of Unknown payments, and callback handling. It reports outcomes to Payment Processing; it does **not** own the payment state machine.

**Reconciliation** compares PayLink's payments with each biller's settlement file, resolves Unknown payments when the file gives a definite answer, and flags all other differences for support.

## 2. User stories

### Biller Integration

| ID | Story | Acceptance criteria (Given / When / Then) | Priority |
|---|---|---|---|
| US-BI-1 | As the system, I send a payment to ElectroCo through its adapter. | **Given** funds are held, **when** the adapter calls ElectroCo with a unique reference, **then** a "paid" answer is returned to Payment Processing as a confirmation. | Must |
| US-BI-2 | As a customer, I can run a bill inquiry for any biller. | **Given** a valid meter or phone number, **when** I request an inquiry, **then** the adapter returns the amount due in one common internal format, whatever the biller's protocol. | Must |
| US-BI-3 | As the system, I talk to AquaCo in its XML format and map its error codes. | **Given** AquaCo answers with an error code, **when** the adapter parses it, **then** it returns a refusal with a clear internal reason code. | Must |
| US-BI-4 | As the system, I map AquaCo's rejection of an amount above the bill. | **Given** AquaCo rejects a payment above the bill amount, **when** the adapter receives the rejection, **then** it returns a refusal with reason "amount exceeds bill" and the payment becomes Failed. | Should |
| US-BI-5 | As the system, I treat a slow biller as Unknown. | **Given** a live biller does not answer within 10 s (configurable), **when** the timeout fires, **then** the adapter reports "no answer", the payment becomes Unknown and the money stays held. | Must |
| US-BI-6 | As the system, I re-query Unknown payments. | **Given** a payment is Unknown, **when** 1, 5 and 15 minutes have passed, **then** the adapter re-queries the biller; a definite answer moves the payment to Completed or Failed; after the third attempt it waits for reconciliation. | Must |
| US-BI-7 | As the system, I accept TopUpNet's asynchronous callback. | **Given** TopUpNet replied "accepted", **when** it calls the callback URL with success or failed, **then** the result is passed to Payment Processing. | Must |
| US-BI-8 | As the system, I ignore duplicate callbacks. | **Given** a payment is already Completed, **when** a second "success" callback arrives, **then** the state is unchanged and the duplicate is logged. | Must |
| US-BI-9 | As the system, I verify that callbacks come from TopUpNet. | **Given** a callback without a valid secret or signature, **when** it arrives, **then** it is rejected with 401 and logged. | Should |
| US-BI-10 | As a developer, I can add a new biller without touching other modules. | **Given** a new adapter and its configuration, **when** the application starts, **then** the biller works and no file outside Biller Integration and configuration changed. | Must |
| US-BI-11 | As the system, a biller outage does not stop payments to other billers. | **Given** ElectroCo is down, **when** a customer pays AquaCo, **then** the payment completes normally. | Must |
| US-BI-12 | As the system, I absorb ElectroCo API v2 (new field names, piasters, signed requests). | **Given** v2 is enabled by configuration, **when** a payment is sent, **then** the amount is converted to piasters, the request is signed, and only the ElectroCo adapter changed. | Should |
| US-BI-13 | As the system, I exchange NetCo's daily batch files. | **Given** payments for NetCo during the day, **when** the daily job runs, **then** one CSV is uploaded and the next morning's result file updates the payment states. | Could |

### Reconciliation

| ID | Story | Acceptance criteria (Given / When / Then) | Priority |
|---|---|---|---|
| US-RC-1 | As support, I want the system to import a biller's settlement file. | **Given** a valid settlement file, **when** it is uploaded or found in the configured folder, **then** every row is parsed and stored. | Should |
| US-RC-2 | As support, I want a daily reconciliation report. | **Given** a settlement file, **when** the nightly job runs, **then** PayLink's payments for that biller and day are compared with it and a report is produced. | Should |
| US-RC-3 | As support, I want differences flagged. | **Given** the file lists a payment PayLink has as Failed, **when** reconciliation runs, **then** the difference appears in the report for support to review. | Must |
| US-RC-4 | As the system, I resolve Unknown payments from the settlement file. | **Given** a payment is Unknown, **when** the file lists it, **then** it becomes Completed; **when** the file does not list it, **then** it becomes Failed. | Must |
| US-RC-5 | As support, I want amount differences flagged. | **Given** the same reference has different amounts on both sides, **when** reconciliation runs, **then** it is reported as an amount difference. | Should |
| US-RC-6 | As support, I want references unknown to PayLink flagged. | **Given** the file contains a reference PayLink has no record of, **when** reconciliation runs, **then** it is reported as "unknown to PayLink". | Should |
| US-RC-7 | As an auditor, I want reconciliation actions logged. | **Given** reconciliation changes a payment's state, **when** the change is saved, **then** an audit record with who, when and why is written. | Must |

Total: 13 + 7 = 20 stories.

## 3. Non-functional requirements

| ID | Quality | Measurable target | How we measure |
|---|---|---|---|
| NFR-BI-1 | Resilience | With ElectroCo down, payments to AquaCo and TopUpNet still complete (success rate unchanged) | Integration test, mock set to fail |
| NFR-BI-2 | Modifiability | Adding a biller changes 0 files outside Biller Integration and configuration | Diff of the pull request |
| NFR-BI-3 | Timeout | Live biller calls time out at 10 s ± 0.5 s, value read from configuration | Test with a 12 s mock |
| NFR-BI-4 | Idempotency | 100% of duplicate callbacks cause 0 extra state changes | Duplicate-callback test |
| NFR-RC-1 | Auditability | 100% of reconciliation state changes have an audit record | Test over all reconciliation paths |
| NFR-RC-2 | Performance | A settlement file of 100,000 rows is reconciled in under 60 s | Benchmark in CI report |

## 4. Ambiguities and decisions

| # | Ambiguity / question | Decision |
|---|---|---|
| 1 | If a retry and the settlement file disagree about an Unknown payment, which wins? | The settlement file wins; any conflict is flagged in the report. |
| 2 | Does reconciliation fix differences or only report them? | It resolves only Unknown payments automatically. Everything else is flagged for support. |
| 3 | What format do settlement files use? | One internal format; one parser per biller converts to it. |
| 4 | What if TopUpNet's callback never arrives? | The payment is treated as Unknown and follows the retry and reconciliation rules. |
| 5 | How do we know a callback is really from TopUpNet? | Shared secret or signature header, checked by the callback endpoint. |
| 6 | Who owns the payment state transitions? | Payment Processing (Habiba). Biller Integration and Reconciliation only report outcomes through its interface. |
| 7 | Which payments may AquaCo reject above the bill amount? | Payment Processing never sends more than the inquired amount. If AquaCo still rejects, the payment becomes Failed (a refusal), because the life cycle in section 2.5 has no other path. |
