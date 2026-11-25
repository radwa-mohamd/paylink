# Part 1 - User Stories

## Problem Summary

PayLink is a bill-payment and wallet backend for a company in Egypt, similar to Fawry. Customers pay utility bills (electricity, water, mobile top-up and internet) from a digital wallet, either in the app or through agents. PayLink holds customers' money, so it must be correct: no customer may be charged twice, and no balance may become negative. A payment can fail at many steps (fraud checks, the biller not answering, a late callback), so the system must handle each failure safely. Our team builds the backend, with a design that makes adding new billers easy and that records every money movement in a ledger.

## Stakeholders

| Stakeholder                          | What they need from the system                                        |
| ------------------------------------ | --------------------------------------------------------------------- |
| Customer                             | Pay bills quickly and safely from the wallet; see balance and history |
| Agent                                | Pay on behalf of customers and earn commission                        |
| Biller (ElectroCo, AquaCo, TopUpNet) | Receive correct payments and a matching settlement                    |
| PayLink admin                        | Manage billers, fees and limits without redeploying                   |
| Support employee                     | Find a payment, see its history, block or unblock accounts            |
| PayLink management and finance       | Earn fees, avoid losses, get daily totals                             |
| Regulators                           | Safe handling of customers' money and an audit trail                  |

## US-1: Register a customer (FR-1, Must)

As a new customer,
I want to register an account,
so that I can have a wallet and pay my bills.

Acceptance criteria:

1. Given a phone number that is not registered,
   When I submit a valid name, phone number and password,
   Then an account is created with verification level "basic" and a wallet with available balance 0.00 EGP and held balance 0.00 EGP.

2. Given a phone number that is already registered,
   When I try to register again with the same phone number,
   Then the registration is rejected with a clear error message and no second account is created.

3. Given I leave the name, phone number or password empty,
   When I submit the registration,
   Then the request is rejected and the error message names the missing field.

## US-2: Upgrade a customer to verified (FR-2, Should)

As a basic customer,
I want to be upgraded to the verified level,
so that I can pay larger amounts per day.

Acceptance criteria:

1. Given a customer with level "basic" and a daily limit of 5,000 EGP,
   When the customer is upgraded to "verified",
   Then the level is "verified" and the daily limit is 30,000 EGP.

2. Given a basic customer who already paid 4,000 EGP today,
   When the customer is upgraded to "verified",
   Then the remaining daily allowance is 26,000 EGP.

## US-3: View wallet balance and transaction history (FR-3, Must)

As a customer,
I want to see my available balance, my held balance and my transaction history,
so that I know how much I can spend and where my money went.

Acceptance criteria:

1. Given a wallet with 650.00 EGP available and 352.50 EGP held,
   When I open my wallet,
   Then I see "available: 650.00 EGP" and "held: 352.50 EGP" as two separate values.

2. Given I made a top-up of 500.00 EGP and then a payment of 352.50 EGP,
   When I open my transaction history,
   Then I see both transactions, each with date, type and amount, ordered by date.

3. Given another customer has transactions,
   When I open my transaction history,
   Then I see only my own transactions.

## US-4: Top up the wallet (FR-4, Must)

As a customer,
I want to top up my wallet by simulated card or agent cash,
so that I have money available to pay bills.

Acceptance criteria:

1. Given a wallet with 100.00 EGP available,
   When I top up 500.00 EGP by simulated card,
   Then the available balance becomes 600.00 EGP.

2. Given a wallet with 100.00 EGP available,
   When an agent tops up 200.00 EGP in cash for me,
   Then the available balance becomes 300.00 EGP.

3. Given a wallet with 100.00 EGP available,
   When I try to top up 0 EGP or a negative amount,
   Then the request is rejected and the balance stays 100.00 EGP.

## US-5: Record every balance change in a ledger (FR-5, Must)

As PayLink (the company),
I want every balance change to create a ledger entry and every balance to be computed from the ledger,
so that all money movements are traceable and balances can never be silently changed.

Acceptance criteria:

1. Given a wallet,
   When a top-up of 500.00 EGP completes,
   Then exactly one new ledger entry is created with wallet, amount, type and timestamp.

2. Given a ledger with a credit of 1,000.00 EGP and a hold of 352.50 EGP,
   When the balance is calculated,
   Then available is 647.50 EGP and held is 352.50 EGP, both computed from the ledger entries.

3. Given any operation that changes a balance (top-up, hold, release, charge, refund),
   When the operation completes,
   Then a ledger entry exists for it, and no balance value was changed without a ledger entry.

## US-6: Manage billers and services (FR-6, Must)

As an admin,
I want to add, edit and disable billers and services,
so that the catalog stays correct without changing the system.

Acceptance criteria:

1. Given I am an admin,
   When I add the biller "ElectroCo" with service "Electricity" and the required field "meter number",
   Then the biller appears in the biller catalog.

2. Given an existing service with a maximum amount of 10,000 EGP,
   When I edit the maximum amount to 8,000 EGP,
   Then the catalog shows 8,000 EGP as the maximum for that service.

3. Given an active biller,
   When I disable it,
   Then it no longer appears in the list shown to customers.

## US-7: See the list of billers and required fields (FR-7, Must)

As a customer,
I want to see the available billers and the input fields each one requires,
so that I know what information I must enter before paying.

Acceptance criteria:

1. Given active billers ElectroCo, AquaCo and TopUpNet,
   When I open the biller list,
   Then I see all three, each with its services.

2. Given the biller ElectroCo requires a meter number,
   When I open its details,
   Then I see "meter number" as a required field.

3. Given a biller is disabled,
   When I open the biller list,
   Then that biller is not shown.

## US-8: Fixed and percentage fees (FR-16, Must)

As a customer,
I want to see the service fee and the total before I pay,
so that I know exactly what will be taken from my wallet.

Acceptance criteria:

1. Given an electricity bill of 350.00 EGP,
   When the fee is calculated,
   Then the fee is 2.50 EGP and the total is 352.50 EGP.

2. Given a water bill of 120.00 EGP,
   When the fee is calculated,
   Then the fee is 2.50 EGP and the total is 122.50 EGP.

3. Given a mobile top-up of 200.00 EGP,
   When the fee is calculated,
   Then the fee is 3.00 EGP (1.5%).

4. Given a mobile top-up of 50.00 EGP,
   When the fee is calculated,
   Then the fee is 1.00 EGP (the minimum, because 1.5% is 0.75 EGP).

## US-9: Tiered fee for internet bills (FR-16, Must)

As a customer,
I want the internet fee to follow the tier of my bill amount,
so that the fee I pay is the correct one.

Acceptance criteria:

1. Given an internet bill of 150.00 EGP,
   When the fee is calculated,
   Then the fee is 2.00 EGP.

2. Given an internet bill of exactly 200.00 EGP,
   When the fee is calculated,
   Then the fee is 2.00 EGP.

3. Given an internet bill of 200.01 EGP,
   When the fee is calculated,
   Then the fee is 4.00 EGP.

## US-10: Promotion period with zero fee (FR-16, Must)

As PayLink (the company),
I want to run a promotion where the fee is 0 for one chosen biller between two configured dates,
so that we can attract customers to that biller.

Acceptance criteria:

1. Given a promotion for ElectroCo from 1 December to 7 December,
   When a customer pays a 350.00 EGP electricity bill on 3 December,
   Then the fee is 0.00 EGP and the total is 350.00 EGP.

2. Given the same promotion,
   When a customer pays an electricity bill on 8 December,
   Then the normal fee of 2.50 EGP applies.

3. Given the same promotion for ElectroCo,
   When a customer pays a water bill to AquaCo on 3 December,
   Then the normal fee of 2.50 EGP applies.

## 4. Questions and decisions for my part

| #   | Question / ambiguity                                                                           | Decision                                                                                                                    |
| --- | ---------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 1   | What is required to register, and is a wallet created automatically? (US-1)                    | Name, phone number and password. A wallet is created automatically with 0.00 EGP available and 0.00 EGP held.               |
| 2   | What happens if the phone number is already registered, or a required field is missing? (US-1) | The registration is rejected with a clear error message and no account is created.                                          |
| 3   | Who performs the upgrade to verified: an admin or an automatic process? (US-2, FR-2)           | An admin upgrades the customer. Verification is simulated.                                                                  |
| 4   | Do payments made before the upgrade count toward the new daily limit? (US-2)                   | Yes. Remaining allowance = new limit minus what was already paid today.                                                     |
| 5   | What must each history item show, and who can see it? (US-3)                                   | Date, type and amount, ordered by date. A customer sees only their own transactions.                                        |
| 6   | What happens if a top-up amount is 0 or negative? (US-4)                                       | It is rejected and the balance does not change.                                                                             |
| 7   | What happens if a simulated card top-up is declined? (US-4)                                    | It is saved with status DECLINED. No ledger entry is created and the balance does not change.                               |
| 8   | What must a ledger entry contain? (US-5)                                                       | Wallet, entry type, amount, reference type and id, and timestamp, as in the ER diagram.                                     |
| 9   | Is a hold recorded as its own ledger entry? (US-5, FR-5)                                       | Yes. HOLD reduces available and increases held; RELEASE and CHARGE reduce held as in the ER diagram.                        |
| 10  | Is a disabled biller visible to customers? (US-6, US-7)                                        | No. It is hidden from the biller list.                                                                                      |
| 11  | Are the required input fields of a biller configurable? (US-7)                                 | Yes, they are stored as data (service_input_fields). In Phase 1 validation only checks that each required field is present. |
| 12  | If a promotion makes the fee 0, is the agent commission also 0? (FR-16, FR-17)                 | Yes, because the commission is 40% of the service fee.                                                                      |
| 13  | Are the promotion start and end dates inclusive? (US-10)                                       | Yes, both days are inside the promotion.                                                                                    |
| 14  | How are percentage fees rounded? (US-8)                                                        | Exact decimal, rounded to 2 decimal places (half up).                                                                       |
