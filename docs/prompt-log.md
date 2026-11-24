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

## Member 3 - Jana

## Entry 1
- **Date:** 2026-11-24
- **Author:** Jana
- **Stage:** Requirements (part-3.md: Biller Integration and Reconciliation)
- **Tool and model:** Claude (sonnet 5.5)
- **Prompt:**

 > Role: senior requirements analyst. Context: I own the Biller Integration and Reconciliation modules of PayLink (brief attached; billers ElectroCo, AquaCo, TopUpNet, NetCo). Task: write user stories with Given/When/Then acceptance criteria, measurable non-functional requirements, and a list of ambiguities with a proposed decision for each. Constraints: use only the rules in the brief; respect the payment life cycle in section 2.5; use Must/Should/Could priorities. Output: markdown tables.

- **Summary of answer:** 13 Biller Integration stories, 7 Reconciliation stories, 6 NFRs and 5 ambiguities with decisions.
- **Verdict:** Fixed. (1) US-BI-4 made an AquaCo overpayment Rejected, but section 2.5 allows Rejected only for a failed fraud check or insufficient funds; changed to Failed. (2) NFR-RC-2 (100,000 rows in 60 s) was invented, because the brief gives no file size or time; replaced with a target the team can justify.

## Entry 2
- **Date:** 2026-11-24
- **Author:** Jana
- **Stage:** Design (sequence, state and class diagrams)
- **Tool and model:** Claude (sonnet 5.5)
- **Prompt:**

 > Write Mermaid diagrams for PayLink: a sequence diagram for "pay a bill", a sequence diagram for "refund a failed payment", the payment state diagram, and a class diagram for Biller Integration and Reconciliation. Use only the transitions in section 2.5 of the brief and keep names consistent across the diagrams.

- **Summary of answer:** Four Mermaid files. Payment Processing orchestrates; an Unknown payment becomes Failed first and is only then refunded.
- **Verdict:** Accepted after checking: the state diagram matches the table in section 2.5. Rendering result: (fill in after trying mermaid.live).

## Entry 3
- **Date:** 2026-11-24
- **Author:** Jana
- **Stage:** Design (use-case and ER diagrams)
- **Tool and model:** Claude (sonnet 5.5)
- **Prompt:**

 > Write a Mermaid use-case diagram (as a flowchart) and a Mermaid ER diagram for Biller Integration and Reconciliation only. Actors: Customer, Support Employee, Admin, Scheduler, Biller. Tables I own: settlement file, settlement row, reconciliation report, reconciliation difference, callback log, retry attempt; show payment and biller as id-only stubs. Money columns must be exact decimals. Do not add states or tables outside my modules.

- **Summary of answer:** 12 use cases in two boundaries; 8 entities (6 owned, 2 stubs) with nullable payment links for references PayLink does not know.
Verdict: Pending: render both in mermaid.live, compare the stubs with Radwa's and Habiba's tables, then write Accepted or Fixed here with the reason.

## Member 4 - (name)
