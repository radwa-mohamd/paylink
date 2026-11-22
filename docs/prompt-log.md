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

## Member 2 - (name)

## Member 3 - (name)

## Member 4 - (name)
