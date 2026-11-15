# AYBOS — Core Supermarket Management System (MVP)

AYBOS (AI Business Operating System) is a supermarket management platform built as an academic project at the
Syrian Private University, Faculty of Artificial Intelligence Engineering, Department of Software Engineering and
Intelligent Information Systems (2025–2026).

This first stage delivers the **core transactional system**: a clean, auditable data foundation for later stages in
demand forecasting and intelligent automation.

## Modules

| # | Module | Scope |
|---|--------|-------|
| 1 | User & Role Management | BCrypt password hashing, JWT authentication, RBAC for Manager, Cashier, Inventory, Purchasing |
| 2 | Product Management | Catalog CRUD, unique EAN-13/UPC barcodes, SKUs, categories, pricing rules, reorder thresholds |
| 3 | Sales Management (POS) | USB-HID barcode scanning, cart and tax calculation, cash/card tender, receipts, atomic stock deduction |
| 4 | Inventory Management | Real-time balances (`stock >= 0`) and an append-only stock movement ledger |
| 5 | Supplier Management | Supplier directory with delete protection for suppliers that have purchase-order history |
| 6 | Purchase Order Management | DRAFT → PENDING → ISSUED → PARTIALLY_RECEIVED / COMPLETED / REJECTED, goods receiving (GRN) |
| 7 | Dashboard & Reporting | Low-stock alerts (OUT_OF_STOCK, CRITICAL, LOW) and daily sales / cash reconciliation |

Out of scope for this stage: ML demand forecasting, computer-vision checkout, multi-tenant SaaS, customer mobile apps.

## Repository layout

```
aybos/
├── frontend/   # Web client: POS terminal, inventory console, manager dashboard
├── backend/    # REST API, business services, database migrations
├── docs/       # Report material, diagrams, API notes
└── .github/    # Pull request template and repository settings
```

## Team

| Member | Roles |
|--------|-------|
| Ghaith Samer Aroudaki ([@ghaitharoudaki](https://github.com/ghaitharoudaki)) | Team Leader, Frontend (UI) Developer, Co-Tester |
| Fahed Saeed Derki ([@PinkGuyz](https://github.com/PinkGuyz)) | Backend Developer, Database Administrator, Algorithm Developer, Co-Tester |

**Supervisors:** Dr. Kadan Aljoumah, Dr. Tharaa Aslan

## Workflow

The project follows Agile Scrum with Extreme Programming practices and the **GitHub Flow** branching model.

1. `main` is always deployable and protected — no direct pushes.
2. Create a short-lived branch per user story:
   - `feature/<us-id>-<short-name>` (e.g. `feature/us-08-atomic-checkout`)
   - `fix/<short-name>` for bug fixes
   - `docs/<short-name>` for documentation
3. Write tests first (TDD), keep commits small, and use clear messages (`feat:`, `fix:`, `test:`, `docs:`, `refactor:`).
4. Open a pull request into `main`. The other team member reviews and approves it, and the CI checks must pass.
5. Squash-merge, then delete the branch.

## Getting started

Setup instructions will be added once the technology stack is finalized (see `frontend/` and `backend/`).
