# PayLink

PayLink is a bill-payment and wallet backend for a company in Egypt (similar to Fawry).
Customers pay utility bills (electricity, water, mobile top-up, internet) from a digital wallet.
The main goal is to be correct with money: no double charges and no negative balances.

Year Work Project - Faculty of Computers and Information, Cairo University.

## Team

| # | Member | Main area |
|---|--------|-----------|
| 1 | Radwa | Accounts, Wallet, Biller Catalog, Fees |
| 2 | Habiba | Payment Processing, Fraud Checks |
| 3 | Jana | Biller Integration, Reconciliation |
| 4 | Nourhan | Notifications, DevOps, Admin |

## Tech stack

- Language: Python
- Framework: FastAPI
- Database: relational (to be chosen)
- Tools: Docker, docker compose, GitHub Actions, PlantUML / Mermaid

## Project status

- [ ] Phase 1: requirements, design, DevOps foundation (due 25 Nov 2026)
- [ ] Phase 2: complete system (due 18 Dec 2026)

## Repository structure

docs/
  part-1.md ... part-4.md   requirements written by each member
  requirements.md           merged requirements pack
  design/                   diagrams (source files)
  prompt-log.md             log of all AI prompts and our verdicts

## How to run

Coming soon. The goal is two commands:

    docker compose up

## Working rules

- Nobody pushes directly to main. Work on a branch and open a pull request.
- Another teammate reviews every pull request before merge.
- Every AI prompt is recorded in docs/prompt-log.md.
