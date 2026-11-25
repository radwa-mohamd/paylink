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

## Member 4 - Nourhan

### Entry 1

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Requirements
- **Tool and model:** Claude 
- **Prompt:**
  > Role: business analyst. Context: PayLink bill-payment backend; my part covers notifications, support and administration (FR-20 to FR-25 and FR-28 in the brief). Task: write user stories for these requirements. Constraints: "As a / I want / so that" format; 2 to 4 Given/When/Then acceptance criteria per story, including failure cases; keep the Must/Should/Could priorities of the brief; do not add features that are not in the brief. Output: Markdown grouped by FR, with IDs of the form `US-<FR>-<letter>`.
- **Summary of answer:** 13 user stories grouped by FR, each with 2 to 4 acceptance criteria, covering the notification retry rule and the audit log rules.
- **Verdict:** Fixed. Some criteria introduced rules that the brief does not state (audit records are append only, only admins can read the audit log, an edited fee does not change payments that already exist). I moved these rules to the "Questions and decisions" table in part-4.md and recorded them as team decisions.

### Entry 2

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design (use-case diagram)
- **Tool and model:** Claude 
- **Prompt:**
  > Role: software architect. Context: PayLink with these actors: customer, agent, support employee, admin, biller, SMS/push provider and scheduler. Task: draw one use-case diagram for the whole system. Constraints: PlantUML; one package per module; FR numbers in the use-case labels; use include and extend only where a real dependency exists. Output: the .puml source only.
- **Summary of answer:** A diagram with 7 actors and about 25 use cases that renders without errors.
- **Verdict:** Fixed. The first layout had many crossing associations. A top-to-bottom layout was cut off at the right edge, so I kept left-to-right and placed the support, admin and biller actors on the right side of the system boundary. The content was reviewed separately in Entry 4.

### Entry 3

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps foundation
- **Tool and model:** Claude 
- **Prompt:**
  > Role: DevOps engineer. Context: Python FastAPI application with a PostgreSQL database, built with GitHub Actions. Task: create a Dockerfile, a docker-compose.yml and a CI workflow for an empty application that exposes /health. Constraints: the system starts with `docker compose up`; the application waits for the database; the CI runs lint and tests with coverage, builds the image and checks /health; no secrets are stored in the repository. Output: the files only.
- **Summary of answer:** A Dockerfile, a compose file with a database health check, and a CI workflow with two jobs. Lint and the test passed in the AI's environment.
- **Verdict:** Fixed. The first compose file had no health check for the application, but the CI uses `docker compose up --wait`, so it could report success before the application was ready. A health check on /health was added. I ran `docker compose up` on my machine: both images were built, the database became healthy and /health returned `{"status":"ok"}`.

### Entry 4

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design review (use-case diagram)
- **Tool and model:** Claude 
- **Prompt:**
  > Role: senior software architect reviewing a student use-case diagram. Context: PayLink bill-payment backend; the diagram is the attached PlantUML file (use-case.puml) and the project brief is attached. A set of review notes on the diagram follows in full. Task: check each comment of the review against the brief and state which changes the diagram really needs. Constraints: judge each comment separately; say clearly when a comment is wrong or unnecessary; do not add a use case only because a requirement exists. Output: one line per comment (correct / wrong / optional), the reason taken from the brief, and the exact change to the .puml file.
- **Summary of answer:** The AI checked each comment against the brief and the .puml file. It agreed with most of them: remove the Agent-Customer generalization, rename FR-25 to "Record audit log entry", add the Fees & Commissions and Biller Integration packages, split Hold and Release, and add the audit record to the agent commission. It rejected adding the ledger (FR-5) and the payment life cycle (FR-11) as use cases, because they are internal functions that belong in the ER and state diagrams. It also found that an extend arrow pointed in the wrong direction, although the review had called that relation correct.
- **Verdict:** Fixed. The first diagram contained five errors, which I corrected against the brief. (1) Agent was drawn as a specialization of Customer, which the brief does not say. (2) FR-25 was modeled as "View audit log", but the requirement is to record every money movement and admin action. (3) The extend arrow pointed from Send to Retry instead of from Retry to Send. (4) Hold and Release were one use case, although Release only happens when a payment fails. (5) Request refund had an audit include although it moves no money, while the agent commission, which does move money, had none.

### Entry 5

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps (CI)
- **Tool and model:** Claude 
- **Prompt:**
  > Role: DevOps engineer. Context: the GitHub Actions workflow of PayLink (FastAPI, pytest, ruff, Docker) failed after my push: "CI / build-lint-test (push), failing after 13s" (a screenshot of the failed check was attached). The same code passed on my machine. Task: find the cause of the failure and fix it. Constraints: verify the fix with the package versions that the workflow installs; change as few files as possible. Output: the cause in one sentence and the corrected file.
- **Summary of answer:** The AI reproduced the error locally. Running `pytest` by name does not add the project folder to the import path, so the test failed with `No module named 'app'`.
- **Verdict:** Fixed. The first workflow passed locally with `python -m pytest` but failed on GitHub. I added `pythonpath = ["."]` to pyproject.toml, pushed the change, and the pipeline passed.