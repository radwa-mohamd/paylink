# Prompt Log

Rules: add a new entry for every AI prompt. Write only in your own section.
Be honest: rejected or fixed answers earn marks when you explain why.

## Member 1 - Radwa

### Entry 1

- **Date:** 2026-11-22
- **Author:** Radwa
- **Stage:** Requirements
- **Tool and model:** Claude
- **Prompt:**
  > Role: business analyst. Context: PayLink, wallet with available and held balance, ledger, limits and fee rules. Task: user stories for FR-1 to FR-7 and FR-16. Constraints: As a / I want / so that, 2 to 3 Given/When/Then criteria each, measurable, keep priorities, do not invent features. Output: Markdown, US-1, US-2, ...
- **Summary of answer:** 10 user stories with 30 acceptance criteria. Fee numbers and limits matched the brief.
- **Verdict:** Fixed: the prompt said "Do not invent features", but some acceptance criteria were not in the brief (for example, a customer sees only their own transactions, rejecting empty fields). I checked pages 7 to 9 of the brief and listed them in an "Assumptions" section.

### Entry 2

- **Date:** 2026-11-22
- **Author:** Radwa
- **Stage:** Requirements review
- **Tool and model:** Claude
- **Prompt:**
  > Review the user stories you wrote against the PayLink brief. List every acceptance criterion that is not directly stated in the brief or in the requirements I gave you, and mark each as "assumption". Do not add new stories.
- **Summary of answer:** The AI listed 11 of 30 acceptance criteria as assumptions.
- **Verdict:** Accepted: I checked the 11 items against the brief and agree. I added them to the "Assumptions" section of part-1.md and sent the open questions to the requirements lead.

### Entry 3

- **Date:** 2026-11-22
- **Author:** Radwa
- **Stage:** Design (ER diagram)
- **Tool and model:** Claude
- **Prompt:**
  > Review the US against the PayLink brief. Write a Mermaid Entity Relationship diagram for the App we built
- **Summary of answer:** The AI produced a table design with a separate "transactions" table next to "ledger_entries". The design also had no wallet for agents, and the ledger could not separate available from held balance.
- **Verdict:** Rejected: I did not accept the first design. I reviewed it and checked each point against the brief. The "transactions" table duplicated "ledger_entries", which breaks FR-5 (the ledger is the single source of truth for money). I removed it, and added top_ups, account_type and typed entry_type values (TOP_UP, HOLD, RELEASE, CHARGE, REFUND, COMMISSION).

### Entry 4

- **Date:** 2026-11-22
- **Author:** Radwa
- **Stage:** Design (class diagram)
- **Tool and model:** Claude (write the model name shown in the app)
- **Prompt:**
  > Write a Mermaid domain class diagram for the accounts, wallet, biller catalog and fee classes, using the same names as the ER diagram.
- **Summary of answer:** 9 classes and 3 enumerations. The wallet has no stored balance; getAvailableBalance() and getHeldBalance() compute it from ledger entries.
- **Verdict:** Accepted: I rendered it in mermaid.live and checked it against the ER diagram.

## Member 2 - Habiba

### Entry 1

- **Date:** 2026-11-24
- **Author:** Habiba
- **Stage:** Design clarification
- **Tool and model:** ChatGPT
- **Prompt (English translation):**
  > Does the PayLink assignment require us to write the diagrams in Mermaid?
- **Summary of answer:** The brief allows Mermaid or PlantUML and requires
  the diagram source files in `docs/design/`.
- **Verdict:** Accepted: I used Mermaid `.mmd` files to match the team's
  existing diagrams.

### Entry 2

- **Date:** 2026-11-24
- **Author:** Habiba
- **Stage:** Design (payment diagrams)
- **Tool and model:** ChatGPT
- **Prompt (English summary):**
  > Using the PayLink brief and the Part 2 payment scenarios, help me
  > prepare the payment state diagram and the pay-a-bill sequence diagram
  > in Mermaid.
- **Summary of answer:** ChatGPT drafted a state diagram for the payment
  lifecycle and a sequence diagram showing how the customer, PayLink,
  the wallet, and ElectroCo interact during a payment.
- **Verdict:** Accepted: I reviewed both Mermaid diagrams against the
  PayLink brief. I checked that a timeout moves the payment to `Unknown`
  with the funds still held, and that a confirmed failure moves from
  `Failed` to `Refunded` after the hold is released.

### Entry 3

- **Date:** 2026-11-24
- **Author:** Habiba
- **Stage:** Requirements and design review
- **Tool and model:** ChatGPT
- **Prompt (English summary):**
  > Review my Part 2 user stories and payment diagrams. Flag any
  > acceptance criteria or diagram steps that need clarification.
- **Summary of answer:** ChatGPT flagged that the concurrent duplicate
  request scenario in US-13 expected one hold and one biller call
  without specifying a valid payment and sufficient wallet funds.
  It also checked that a biller timeout leaves the funds held.
- **Verdict:** Fixed: I updated the second acceptance criterion in US-13
  to specify a valid request and sufficient available funds. The scenario
  now checks that two copies of the same valid request create one payment,
  one hold, and one request to the biller.