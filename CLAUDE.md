# AYBOS — Project Context for Claude

Read this before working in this repository. It summarizes decisions already made in the project report
(`AYBOS_Report`), so that code and report stay consistent.

## Project

- **AYBOS (AI Business Operating System)** — Core Supermarket Management System (MVP), single store, on-premise.
- University applications course, Syrian Private University, Faculty of AI Engineering, 2025–2026. 14-week semester.
- **Team:** Ghaith Samer Aroudaki (@ghaitharoudaki) — Team Leader, Frontend/UI, Co-Tester, Scrum Product Owner.
  Fahed Saeed Derki (@PinkGuyz) — Backend, DBA, Algorithm Developer, Co-Tester, Scrum Master.
- **Supervisors:** Dr. Kadan Aljoumah, Dr. Tharaa Aslan.
- Spelling is always **AYBOS** (never "AIBOS").

## Scope (7 modules) — do not add features outside this list

1. User & Role Management — BCrypt (work factor 12), JWT (HMAC-SHA256, 8 h expiry), RBAC.
   Roles: `MANAGER`, `CASHIER`, `INVENTORY`, `PURCHASING`.
2. Product Management — catalog CRUD, unique EAN-13/UPC barcode + unique SKU, categories, cost/selling price,
   reorder level. Selling price below cost only with explicit manager override (audit-logged).
3. Sales / POS — USB-HID scanner (keyboard wedge, buffered), cart + tax math, CASH/CARD tender, receipt printing,
   atomic stock deduction.
4. Inventory — `current_stock >= 0` (DB CHECK constraint) and an **append-only** `stock_movement_log`
   (types: SALE, PURCHASE_RECEIPT, DAMAGE, EXPIRY, ADJUSTMENT) recording product, signed delta, reference, user, time.
5. Suppliers — CRUD; a supplier with PO history is set `INACTIVE`, never hard-deleted.
6. Purchase Orders — state machine `DRAFT → PENDING → ISSUED → PARTIALLY_RECEIVED / COMPLETED`, or `PENDING → REJECTED`.
   Only MANAGER approves/rejects. Goods receiving creates a GRN; over-delivery > 10% needs manager sign-off.
7. Dashboard & Reporting — low-stock tiers: `OUT_OF_STOCK` (stock = 0), `CRITICAL` (≤ reorder/2), `LOW` (≤ reorder);
   daily sales summary (revenue, cash vs card, tax, count, top 5 products, average basket).

Out of scope this semester: ML forecasting, computer vision, multi-tenant SaaS, customer mobile apps, offline POS.

## Non-negotiable technical rules

- **Layered monolith:** UI → API/middleware (JWT + RBAC guard) → services → repositories → relational DB.
- **Relational DB in 3NF** with PK/FK/UNIQUE/CHECK/DEFAULT constraints. Must support row-level locking
  (`SELECT … FOR UPDATE`) — **SQLite is not acceptable** (no row locks).
- **Every stock change** (sale, write-off, receiving) runs in one transaction: lock inventory row `FOR UPDATE`,
  validate, update stock, append ledger row, commit — or roll back entirely.
- **Checkout never trusts client prices.** Client sends `{productId, qty}`; the server reads selling prices.
- RBAC is enforced server-side in middleware (never only by hiding UI). Unauthorized → 403.
- Parameterized queries / ORM only. No secrets in the repo (`.env` is git-ignored; commit `.env.example`).

## Names used in the report's UML diagrams (keep code consistent or update the report)

Controllers/services: `AuthController`, `AuthService`, `JwtProvider`, `RoleGuardMiddleware`, `UserService`,
`ProductController`, `ProductService`, `ProductValidator`, `CartEngine`, `BarcodeScannerHook`, `SalesController`,
`CheckoutService`, `TaxCalculator`, `InventoryController`, `InventoryAdjustmentService`, `StockLedgerService`,
`SupplierService`, `PurchaseOrderService`, `PurchaseOrderStateMachine`, `GoodsReceivingService`, `AlertService`,
`ReportingService`. Repositories: `UserRepository`, `ProductRepository`, `InventoryRepository`,
`StockMovementRepository`, `SupplierRepository`, `PurchaseOrderRepository`, `SalesRepository`, `PriceAuditRepository`.

Key endpoints (prefix `/api/v1`): `POST /auth/login`, `POST /users`, `GET|POST /products`,
`PUT /products/{id}/price`, `POST /sales/checkout`, `GET /inventory/{productId}/movements`, `POST /inventory/adjust`,
`POST|DELETE /suppliers`, `POST /purchases`, `POST /purchases/{id}/approve|reject`, `PUT /purchases/{id}/receive`,
`GET /alerts/low-stock`, `GET /reports/daily?date=YYYY-MM-DD`.

Tables: `users`, `products`, `categories`, `inventory`, `stock_movement_log`, `suppliers`, `purchase_orders`,
`po_items`, `goods_received_notes`, `sales_receipts`, `sales_items`, `price_audit_log`.

## Technology stack — NOT FINAL

The report's Chapters 1–4 are deliberately stack-neutral. Chapters 5–6 currently describe
Node.js 20 + TypeScript + Express + PostgreSQL + Prisma + Docker + Jest. Confirm with Fahed before building;
once decided, record it here and update report Chapter 6. Frontend is a single-page app (framework to be chosen
by Ghaith).

## Workflow

- Agile Scrum (7 two-week sprints, Sprint 0–6) + XP: TDD, pair programming on checkout/receiving, CI.
- **GitHub Flow:** `main` protected; branches `feature/us-XX-short-name`, `fix/...`, `docs/...`;
  PR reviewed by the other member; CI must pass; squash-merge.
- Coverage target **≥ 80%** on core services (Definition of Done).
- Commit prefixes: `feat:`, `fix:`, `test:`, `docs:`, `refactor:`, `chore:`.

Sprint plan: S0 setup + schema + CI · S1 auth/RBAC + products · S2 inventory ledger + suppliers ·
S3 POS + atomic checkout · S4 purchase orders + receiving · S5 adjustments, alerts, daily report ·
S6 security/concurrency testing, bug fixing, demo data, defense.

## Report status (as of 15 Nov 2026)

- Front matter and Chapters 1–4 revised to the faculty guide (structure, formatting, 13 use cases with
  activity + sequence diagrams, RTM with sources, backlog with story points).
- Still to fill: results in both abstracts, AI-tool names in section 1-6, burndown chart (Figure 2),
  RTM status column, real interview evidence.
- Chapters 5–9 and appendices not yet audited. Known issue: Chapter 5 refers to a non-existent "Table 8"
  permission matrix. Results/coverage/latency numbers in Chapters 7–8 must be replaced with real measurements.
- Report rules: Times New Roman 12 body (headings 18/16/14 bold), 1.15 spacing, 2.54 cm margins, justified,
  headings numbered `1-1`, tables captioned above, figures below, APA 7 references, no LaTeX, portrait pages only
  (printed as a book). Do not mention "the guide" inside the report text.
