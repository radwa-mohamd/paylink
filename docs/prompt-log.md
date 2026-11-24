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
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > Role: business analyst. Context: PayLink bill-payment backend; I own notifications, support and admin. Task: user stories for FR-20 to FR-25 and FR-28 from the brief. Constraints: As a / I want / so that, 2 to 4 Given/When/Then criteria each, include failure cases, keep the Must/Should/Could priorities from the brief, do not invent features. Output: Markdown grouped by FR, IDs US-<FR>-<letter>.
- **Summary of answer:** 13 user stories grouped by FR, each with 2 to 4 criteria, including the retry rule (3 retries) and the audit log rules.
- **Verdict:** Fixed: the AI added rules that are not in the brief (audit records are append only, only admins read the audit log, an edited fee does not change payments already created). I moved them to the "Questions and decisions" table in part-4.md and marked them as my decisions.

### Entry 2

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Requirements review
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > Review the FR-23 wording in the brief: "Failed notifications are retried 3 times then marked undelivered". List the possible meanings and the questions I should ask the requirements lead.
- **Summary of answer:** Two meanings: 3 attempts in total, or 1 first attempt plus 3 retries. It also asked whether the delay between retries is fixed.
- **Verdict:** Accepted: I chose 1 attempt + 3 retries, with the number in configuration, and wrote it in the decisions table.

### Entry 3

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design (use-case diagram)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > Role: software architect. Context: PayLink modules and actors (customer, agent, support, admin, biller, SMS/push provider, scheduler). Task: one PlantUML use-case diagram for the whole system. Constraints: one package per module, FR numbers in the labels, include/extend only where a real dependency exists. Output: .puml source only.
- **Summary of answer:** A diagram with 7 actors and about 25 use cases, rendered correctly.
- **Verdict:** Fixed: the first layout was crowded with crossing arrows. I tried top-to-bottom, but the picture was cut off at the right, so I kept left-to-right and moved the support, admin and biller actors to the right side.

### Entry 4

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > Role: DevOps engineer. Context: Python FastAPI app, PostgreSQL, GitHub Actions. Task: Dockerfile, docker-compose.yml and a CI workflow for an empty app with /health. Constraints: start with `docker compose up`, app waits for the database, CI runs lint, tests with coverage, builds the image and checks /health, no secrets in the repo. Output: the files only.
- **Summary of answer:** Dockerfile, compose with a database health check, CI with two jobs. Lint and the test passed on the AI's machine.
- **Verdict:** Fixed: the first compose file had no health check for the app, but the CI job uses `docker compose up --wait`, so it could report success before the app was ready. A health check on /health was added. I tested `docker compose up` on my laptop: both images built, the database became healthy and /health returned `{"status":"ok"}`.

### Entry 5

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** Design review (use-case diagram)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > [write the review prompt you really used]
- **Summary of answer:** [write what the AI answered, in 2 lines]
- **Verdict:** Fixed: the AI's first diagram had five mistakes, and I corrected each one against the brief. (1) Agent was drawn as a kind of Customer, but the brief does not say that. (2) FR-25 was "View audit log", but the requirement is to record every money movement and admin action. (3) The extend arrow pointed from Send to Retry instead of from Retry to Send. (4) Hold and Release were one use case, but Release only happens when a payment fails. (5) Request refund had an audit include although it moves no money, and the agent commission had none although it does.

### Entry 6

- **Date:** 2026-11-24
- **Author:** Nourhan
- **Stage:** DevOps (CI)
- **Tool and model:** Claude Sonnet 5.5
- **Prompt:**
  > [write the prompt you used when the CI failed, or "none: I sent the AI the screenshot of the failing check"]
- **Summary of answer:** The AI reproduced the error locally: `pytest` run by name does not add the project folder to the import path, so the test failed with `No module named 'app'`.
- **Verdict:** Fixed: the first workflow passed locally with `python -m pytest` but failed on GitHub (job build-lint-test, 13 s). I added `pythonpath = ["."]` to pyproject.toml and the pipeline turned green.