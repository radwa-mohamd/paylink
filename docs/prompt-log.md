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

 >Role: senior requirements analyst. Context: I own the Biller Integration and Reconciliation modules of PayLink (brief attached; billers ElectroCo, AquaCo, TopUpNet, NetCo). Task: write user stories with Given/When/Then acceptance criteria, measurable non-functional requirements, and a list of ambiguities with a proposed decision for each. Constraints: use only the rules in the brief; respect the payment life cycle in section 2.5; use Must/Should/Could priorities. Output: markdown tables.

- **Summary of answer:** 13 Biller Integration stories, 7 Reconciliation stories, 6 NFRs and 5 ambiguities with decisions.
- **Verdict:** Fixed. (1) US-BI-4 made an AquaCo overpayment Rejected, but section 2.5 allows Rejected only for a failed fraud check or insufficient funds; changed to  Failed. (2) NFR-RC-2 (100,000 rows in 60 s) was invented, because the brief gives no file size or time; replaced with a target the team can justify.

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


## Member 4 - Nourhan

### Entry 1

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Requirements
- **Tool and model:** Claude 
- **Prompt:**
  > Role: business analyst. Context: PayLink bill-payment backend; my part covers notifications, support and administration (FR-20 to FR-25 and FR-28 in the brief). Task: write user stories for these requirements. Constraints: "As a / I want / so that" format; 2 to 4 Given/When/Then acceptance criteria per story, including failure cases; keep the Must/Should/Could priorities of the brief; do not add features that are not in the brief. Output: Markdown grouped by FR, with IDs of the form `US-<FR>-<letter>`.
- **Summary of answer:** 13 user stories grouped by FR, each with 2 to 4 acceptance criteria, covering the notification retry rule and the audit log rules.
- **Verdict:** Fixed. I checked the stories against FR-20 to FR-25 and FR-28 in the brief. Some criteria added rules that the brief does not state (audit records are append only, only admins can read the audit log, an edited fee does not change payments that already exist). I did not keep them as requirements: I moved them to the "Questions and decisions" table in part-4.md and recorded them as my decisions.

### Entry 2

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design (use-case diagram)
- **Tool and model:** Claude
- **Prompt:**
  > Role: software architect. Context: PayLink with these actors: customer, agent, support employee, admin, biller, SMS/push provider and scheduler. Task: draw one use-case diagram for the whole system. Constraints: PlantUML; one package per module; FR numbers in the use-case labels; use include and extend only where a real dependency exists. Output: the .puml source only.
- **Summary of answer:** A diagram with 7 actors and about 25 use cases that renders without errors.
- **Verdict:** Fixed. The first layout had many crossing associations. I rejected the top-to-bottom layout because the picture was cut off at the right edge, and I kept left-to-right with the support, admin and biller actors on the right side. I then reviewed the content of the diagram separately (Entry 4).

### Entry 3

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps foundation
- **Tool and model:** Claude 
- **Prompt:**
  > Role: DevOps engineer. Context: Python FastAPI application with a PostgreSQL database, built with GitHub Actions. Task: create a Dockerfile, a docker-compose.yml and a CI workflow for an empty application that exposes /health. Constraints: the system starts with `docker compose up`; the application waits for the database; the CI runs lint and tests with coverage, builds the image and checks /health; no secrets are stored in the repository. Output: the files only.
- **Summary of answer:** A Dockerfile, a compose file with a database health check, and a CI workflow with two jobs. Lint and the test passed in the AI's environment.
- **Verdict:** Fixed. I noticed that the compose file had no health check for the application, while the CI uses `docker compose up --wait`, so it could report success before the application was ready; I asked for a health check on /health. I ran `docker compose up` on my machine: both images were built, the database became healthy and /health returned `{"status":"ok"}`.

### Entry 4

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design review (use-case diagram)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > Role: senior software architect reviewing a student use-case diagram. Context: PayLink bill-payment backend; the diagram is the attached PlantUML file (use-case.puml) and the project brief is attached. A set of review notes on the diagram follows in full. Task: check each comment of the review against the brief and state which changes the diagram really needs. Constraints: judge each comment separately; say clearly when a comment is wrong or unnecessary; do not add a use case only because a requirement exists. Output: one line per comment (correct / wrong / optional), the reason taken from the brief, and the exact change to the .puml file.
- **Summary of answer:** The AI checked each comment against the brief and the .puml file. It agreed with most of them: remove the Agent-Customer generalization, rename FR-25 to "Record audit log entry", add the Fees & Commissions and Biller Integration packages, split Hold and Release, and add the audit record to the agent commission. It rejected adding the ledger (FR-5) and the payment life cycle (FR-11) as use cases, because they are internal functions that belong in the ER and state diagrams. It also found that an extend arrow pointed in the wrong direction, although the review had called that relation correct.
- **Verdict:** Fixed. I did not accept the first diagram as it was. I checked each comment against the brief and decided as follows. Accepted: (1) Agent must not be a specialization of Customer, because the brief does not say that; (2) FR-25 means recording every money movement and admin action, so I replaced "View audit log" with "Record audit log entry"; (3) the extend arrow must point from Retry to Send; (4) Hold and Release are separate use cases, because Release only happens when a payment fails; (5) Request refund moves no money, so it has no audit include, while the agent commission does move money and now has one. Rejected: adding the ledger (FR-5) and the payment life cycle (FR-11) as use cases, because they are internal functions and belong in the ER and state diagrams.

### Entry 5

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps (CI)
- **Tool and model:** Claude
- **Prompt:**
  > Role: DevOps engineer. Context: the GitHub Actions workflow of PayLink (FastAPI, pytest, ruff, Docker) failed after my push: "CI / build-lint-test (push), failing after 13s" (a screenshot of the failed check was attached). The same code passed on my machine. Task: find the cause of the failure and fix it. Constraints: verify the fix with the package versions that the workflow installs; change as few files as possible. Output: the cause in one sentence and the corrected file.
- **Summary of answer:** The AI reproduced the error locally. Running `pytest` by name does not add the project folder to the import path, so the test failed with `No module named 'app'`.
- **Verdict:** Fixed. The pipeline failed on GitHub (job build-lint-test, 13 s) although the same code passed on my machine, so I checked the failed run and sent it to the AI. The cause was that `pytest` run by name does not add the project folder to the import path, while the AI had tested with `python -m pytest`. I applied the fix `pythonpath = ["."]` in pyproject.toml, pushed it, and confirmed that the pipeline passed.