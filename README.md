# IMP-PLAN/B2B/001 · VERSION 1.0 · AUGUST 2026
## HEMIN BUSINESS PLC · ERP PROGRAMME
# B2B COMMERCE PORTAL MODULE
## Implementation Plan

*   **Stack:** NestJS · Next.js App Router · PostgreSQL 16 · Prisma · BullMQ
*   **Architecture:** Modular Monolith · Schema-per-module ownership
*   **SOP Source:** B2B Commerce SOP — Starchain Medical PLC / Hemin Business PLC
*   **Framework Baseline:** Foundation Architecture and Planning Framework v2.1
*   **Status:** Ready for Planning-Gate Review and Authorization
*   **Prepared:** August 13, 2026
*   **Metrics:** 16 Functional Requirements · 8 Workflows · 3 State Machines · 12 Domain Entities · 20 Business Rules · 4-Week Delivery

---

## Table of Contents
1. Executive Summary
2. Module Charter
3. B2B Commerce Domain Overview
4. Business Process Analysis
5. Capability Map
6. Workflow Catalogue
7. State Models
8. Domain Entity Catalogue
9. Data Ownership & Module Boundaries
10. Business Rules & Invariants
11. RBAC / Permissions Matrix
12. Multi-Tenant / Security Requirements
13. API Boundary Specification
14. Event & Integration Specification
15. Notifications / SLA Monitoring
16. Audit & Compliance
17. Reporting & Dashboard Requirements
18. Frontend Scope
19. Backend Scope
20. Testing Strategy
21. Implementation Work Breakdown Structure (WBS)
22. Implementation Phases
23. Gantt Chart & Milestones
24. Definition of Done
25. Risks / Dependencies / Open Decisions
26. Traceability Matrix
27. Planning Gate Checklist

---

## 1. Executive Summary
This document defines the complete, implementation-ready plan for the B2B Commerce Portal module of the Hemin Business PLC ERP system. It serves as the authoritative guide for all design, database, API, and frontend development. 

The portal provides Starchain Medical’s hospital and pharmacy clients with secure self-service access to search clinical product catalogs, view contract-specific pricing, place multi-branch orders, check credit limits, track shipments, download statements, upload bank transfer payments, and log support tickets for installed medical devices. All components comply with the HEMIN ERP Foundation Framework v2.1.

---

## 2. Module Charter
*   **Module Name:** `b2b_portal`
*   **Business Purpose:** Transition manual hospital ordering, RFQs, statement tracking, and service logging into an integrated, auditable digital portal, reducing operational delay and improving client trust.
*   **Primary Users:** Hospital Procurement Officers, Hospital Biomedical Engineers, Pharmacy Managers.
*   **Secondary Users:** Starchain Sales Agents, Service Engineers, Finance Officers.
*   **Owned PostgreSQL Schema:** `b2b_portal.*`
*   **Consumed External Data:** identity.users, inventory.items, sales.contract_pricing, finance.customer_credit, organization.branches.
*   **Published Events:** `b2b.order.placed`, `b2b.quotation.requested`, `b2b.ticket.created`, `b2b.payment_slip.uploaded`.
*   **Non-Functional Requirements:** Catalog search latency p95 < 300ms, idempotency checks on checkout, nightly PM expiry scan.

---

## 3. B2B Commerce Domain Overview
The B2B Commerce domain coordinates customer digital interactions. It covers product discovery, transaction execution, financial transparency, and after-sales service tracking for medical devices.

### 3.1 Domain Glossary
*   **B2B Client:** A healthcare institution (hospital, clinic, NGO) authorized to purchase medical equipment.
*   **Customer Admin:** The lead hospital user who creates and manages branch user permissions.
*   **Contract Price:** A pre-negotiated discount price applied to specific clinical product categories.
*   **Credit Guard:** An automated system block preventing orders if the value exceeds the client's available credit.
*   **Biomedical Ticket:** A support ticket raised by a client's biomedical engineer for equipment troubleshooting.
*   **Calibration History:** Recorded logs of preventive maintenance (PM) and service checks performed on installed hardware.

### 3.2 Reference Documents
*   `EX/SC/B2B/001` B2B Customer Registration Policy
*   `EX/SC/B2B/002` Clinical Equipment Warranty & Support Guidelines
*   `OF/SC/B2B/001` Institutional Contract Price Overrides

---

## 4. Business Process Analysis
This module covers eight primary business workflows derived from B2B operations:

### PROCESS 1 — CUSTOMER SELF-SERVICE PORTAL ACCESS (FR-CP-001)
Establish secure credentials and dashboard context for hospital buyers.
*   **SLA:** User invitation processed within 24 hours.
*   **Input:** Institutional account details, verified email.
*   **Output:** Argon2 hashed credentials, active session context.
*   **Risk:** Unauthorized logins or credentials sharing.

### PROCESS 2 — SEARCHABLE DIGITAL CATALOG (FR-CP-002)
Render product categories (ICU, NICU, Operating Room) and related files.
*   **SLA:** Real-time search response.
*   **Input:** Search query, category filters.
*   **Output:** Product detail page, user manuals, EFDA certificates.
*   **Risk:** Outdated EFDA certificate files shown.

### PROCESS 3 — TRANSACTION CHECKOUT & ORDERING (FR-CP-003, FR-CP-015)
Validate stock availability and credit status before creating order.
*   **SLA:** Transaction completed in < 1 second.
*   **Input:** Cart details, `Idempotency-Key` header.
*   **Output:** Placed B2B order, reserved inventory.
*   **Risk:** Double submissions due to network retries.

### PROCESS 4 — QUOTATION REQUEST / RFQ FLOW (FR-CP-004)
Allow customers to request formal quotes for complex medical installations.
*   **SLA:** Quote reviewed by Sales Manager within 2 days.
*   **Input:** RFQ details, requested product specs.
*   **Output:** Approved PDF quotation.
*   **Risk:** Technical specification mismatch.

### PROCESS 5 — MULTI-BRANCH ROUTING (FR-CP-016, FR-CP-009)
Direct order line items to specific hospital branches and departments.
*   **SLA:** Delivery locations validated during checkout.
*   **Input:** Line-item branch mapping.
*   **Output:** Grouped delivery sub-orders.
*   **Risk:** Selection of inactive hospital branches.

### PROCESS 6 — INVOICE & STATEMENT ACCESSIBILITY (FR-CP-006, FR-CP-014)
Provide transparent ledger values and credit visibility.
*   **SLA:** Account statements fetched in real-time.
*   **Input:** Customer ID context.
*   **Output:** Active balance due, historical invoice listing.
*   **Risk:** Slow DB join performance on ledger tables.

### PROCESS 7 — SUPPORT TICKETING (FR-CP-008)
Enable client biomedical teams to log maintenance requests by serial number.
*   **SLA:** Ticket assigned to engineer within 4 hours.
*   **Input:** Equipment serial number, problem description.
*   **Output:** Active support ticket, engineer notification.
*   **Risk:** Inaccurate serial numbers entered.

### PROCESS 8 — REPEAT REPLENISHMENT (FR-CP-013)
Duplicate past successful orders into a new active cart.
*   **SLA:** Cart populated instantly.
*   **Input:** Historical order ID.
*   **Output:** Active cart populated with identical items.
*   **Risk:** Re-ordering discontinued products.

---

## 5. Capability Map
| Cap. ID | Capability | Actor | Source | Expected System Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **C-B2B-01** | Account Self-Service | Customer User | FR-CP-001 | User log-in, profile update, session validation. |
| **C-B2B-02** | Catalog Browsing | Customer User | FR-CP-002 | Search inventory catalog, download manuals. |
| **C-B2B-03** | Order Checkout | Customer User | FR-CP-003 | Enforce idempotency, process cart items to order. |
| **C-B2B-04** | RFQ Routing | Customer User | FR-CP-004 | Submit RFQ, track negotiations, download quotes. |
| **C-B2B-05** | Service Ticketing | Biomed Engineer | FR-CP-008 | Create ticket by serial number, track progress. |
| **C-B2B-06** | Branch Mapping | Customer Admin | FR-CP-009 | Add/remove branch users, map default branches. |
| **C-B2B-07** | Custom Pricing | Customer User | FR-CP-012 | Load specific price list based on customer contract. |
| **C-B2B-08** | Multi-Branch Checkout | Customer User | FR-CP-016 | Map individual items to separate branch IDs. |

---

## 6. Workflow Catalogue

### WF-B2B-01 — CUSTOMER USER ONBOARDING
*   **Trigger:** Sales Manager registers customer account.
*   **Actors:** Sales Manager, Customer Admin.
*   **Preconditions:** Customer record created in system.
*   **Main Flow:**
    1. Sales Manager enters customer email and default branch.
    2. System sends invitation link with unique token.
    3. User clicks link, sets password (Argon2 hashed).
    4. Profile initialized. Status set to `ACTIVE`.
*   **SLA:** Profile activation link expires in 24 hours.

### WF-B2B-02 — DIGITAL CART CHECKOUT
*   **Trigger:** Customer clicks "Checkout" in shopping cart.
*   **Actors:** Customer User.
*   **Preconditions:** Cart contains at least 1 item.
*   **Main Flow:**
    1. System queries `finance.v_customer_credit` for credit limit.
    2. System queries `inventory.v_available_products` for stock levels.
    3. User sets `Idempotency-Key` header.
    4. B2BOrder created in `Placed` state.
    5. Event `b2b.order.placed` published.
*   **Failure Flow:** Over-credit -> display block message; Out-of-stock -> show modification alert.
*   **SLA:** Checkout validation executes in < 500ms.

### WF-B2B-03 — RFQ AND NEGOTIATION
*   **Trigger:** Customer requests custom price for clinical package.
*   **Actors:** Customer User, Sales Manager.
*   **Preconditions:** Active customer session.
*   **Main Flow:**
    1. Customer creates RFQ, uploads specifications document.
    2. Status set to `SUBMITTED`.
    3. Sales Manager reviews, attaches draft PDF, sets price.
    4. Status updated to `APPROVED` (sent to customer).
    5. Customer accepts -> converted to active order.
*   **SLA:** Sales Manager review deadline: 48 hours.

### WF-B2B-04 — TICKET LOGGING & CALIBRATION TRACKING
*   **Trigger:** Biomedical Engineer logs device issue.
*   **Actors:** Biomedical Engineer, Service Manager.
*   **Preconditions:** Device serial number exists in `b2b_portal.installed_equipment`.
*   **Main Flow:**
    1. User enters serial number and priority level.
    2. System verifies active warranty/SLA.
    3. Ticket created in `Open` state.
    4. Service Manager receives email notification.
*   **SLA:** Priority 1 tickets notify Service Manager immediately.

---

## 7. State Models

### 7.1 B2B Order State Machine
| State | Meaning | Transition To | Trigger | Actor / Permission | Terminal? |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **DRAFT** | Cart active; items being added | **PLACED** | User checks out | `b2b.order.create` | No |
| **PLACED** | Order recorded; stock reserved | **CONFIRMED** | Sales confirms contract | `sales.order.confirm`| No |
| **CONFIRMED** | Order locked; processing | **SHIPPED** | Warehouse dispatches | `inventory.dispatch` | No |
| **SHIPPED** | Courier delivery in progress | **DELIVERED** | Customer signs POD | `b2b.order.update` | No |
| **DELIVERED** | Delivery finalized | — | — | — | **YES** |
| **CANCELLED** | Order rejected/reversed | — | — | `b2b.order.cancel` | **YES** |

### 7.2 B2B Quotation State Machine
| State | Meaning | Transition To | Terminal? |
| :--- | :--- | :--- | :--- |
| **DRAFT** | RFQ details compiled by client | **SUBMITTED** | No |
| **SUBMITTED** | Under evaluation by Sales team | **UNDER_REVIEW** | No |
| **UNDER_REVIEW** | Sales Manager attaching price and warranty | **APPROVED**, **REJECTED** | No |
| **APPROVED** | Quote sent to client; pending signature | **CONVERTED_TO_ORDER**, **REJECTED** | No |
| **CONVERTED_TO_ORDER**| Client accepted; order initialized | — | **YES** |
| **REJECTED** | Rejected by either party | — | **YES** |

---

## 8. Domain Entity Catalogue
*   **CustomerAccount:** Main company record (`b2b_portal.customers`). Attributes: id, customerCode, name, efdaLicenseStatus, createdDate.
*   **CustomerUser:** Portal user profile (`b2b_portal.users`). Attributes: id, customerAccountId, email, passwordHash, role, status.
*   **CustomerBranch:** Physical delivery location (`b2b_portal.branches`). Attributes: id, customerAccountId, branchName, city, address.
*   **Cart:** Temporary purchase lines (`b2b_portal.carts`). Attributes: id, customerAccountId, lastUpdated.
*   **CartItem:** Shopping cart items (`b2b_portal.cart_items`). Attributes: id, cartId, productId, quantity, branchId, departmentId.
*   **B2BOrder:** Order header (`b2b_portal.orders`). Attributes: id, orderNumber, customerAccountId, status, totalAmount, idempotencyKey, createdDate.
*   **B2BOrderLine:** Order details (`b2b_portal.order_lines`). Attributes: id, orderId, productId, quantity, price, branchId, departmentId.
*   **B2BQuotation:** RFQ requests (`b2b_portal.quotations`). Attributes: id, quotationNumber, customerAccountId, status, totalPrice, pdfFileId.
*   **SupportTicket:** Service logs (`b2b_portal.tickets`). Attributes: id, equipmentId, customerAccountId, status, priority, description, createdDate.
*   **InstalledEquipment:** Register of customer clinical hardware (`b2b_portal.installed_equipment`). Attributes: id, serialNumber, productName, warrantyExpiry, lastCalibratedDate.
*   **PaymentSlip:** Uploaded bank documents (`b2b_portal.payment_slips`). Attributes: id, orderId, fileId, uploadDate, status.

---

## 9. Data Ownership & Module Boundaries
```
b2b_portal.* (Exclusive Write Ownership)
 ├── b2b_portal.orders
 ├── b2b_portal.quotations
 ├── b2b_portal.tickets
 └── b2b_portal.installed_equipment
```
All external queries are read-only and consume views:
*   `inventory.v_available_products` -> Product specifications and real-time stock levels.
*   `finance.v_customer_credit` -> Customer credit limit checks.
*   `sales.v_contract_pricing` -> Customer-specific contract price overrides.
*   `identity.v_users` -> Authentication credentials.

---

## 10. Business Rules & Invariants
*   **BR-B2B-01 (Credit Block):** Orders cannot progress to `PLACED` if total amount exceeds available credit from `finance.v_customer_credit`.
*   **BR-B2B-02 (Unique Key):** Duplicate checkout POST submissions with matching `Idempotency-Key` headers return the original order.
*   **BR-B2B-03 (Audit Log):** Every state change in orders, quotations, and tickets must insert an append-only row into `b2b_portal.audit_log`.
*   **BR-B2B-04 (EFDA Status):** Catalog items with an `EXPIRED` or `MISSING` EFDA certificate cannot be purchased.
*   **BR-B2B-05 (Lock State):** Orders in `CONFIRMED` or later status are immutable.

---

## 11. RBAC / Permissions Matrix

### 11.1 Roles
*   `customer_admin`: Read catalog, checkout orders, manage branch user profiles.
*   `customer_user`: Read catalog, checkout orders for assigned branches only.
*   `biomed_engineer`: Log support tickets, view installed calibration history.
*   `sales_agent`: View customer orders, review RFQs, upload approved quotes.
*   `finance_viewer`: View credit limits, outstanding balances, and download statements.

### 11.2 Permission Catalogue
*   `b2b.order.create` -> Build cart and checkout.
*   `b2b.order.read` -> Track active order delivery timelines.
*   `b2b.quotation.create` -> Submit RFQs.
*   `b2b.ticket.create` -> Raise biomedical service tickets.
*   `b2b.billing.read` -> View financial ledger records.

---

## 12. Multi-Tenant / Security Requirements
*   **Tenant Isolation:** All entities contain a non-nullable `tenantId` field. Every SQL query applies `tenantId = :currentTenantId` filters.
*   **Security:** Cryptographic session cookies for web portal. Token authentication for external integrations.
*   **Scope Isolation:** Every API endpoint verifies that the user’s `customerAccountId` matches the entity being accessed.

---

## 13. API Boundary Specification

### 13.1 Catalog & Profile
*   `GET /api/b2b/catalog` -> List catalog items (with custom contract pricing applied).
*   `GET /api/b2b/profile` -> Retrieve current user context and branch scopes.

### 13.2 Orders & Billing
*   `POST /api/b2b/orders` -> Place new order. (Header: `Idempotency-Key`).
*   `GET /api/b2b/orders/:id` -> View order details.
*   `GET /api/b2b/billing/statements` -> Retrieve account balance and download invoices.

### 13.3 Service Ticketing
*   `POST /api/b2b/tickets` -> Log support ticket.
*   `GET /api/b2b/equipment` -> Retrieve list of installed clinical equipment.

---

## 14. Event & Integration Specification
*   **Event `b2b.order.placed`:** Published on checkout completion. Consumed by Inventory module for stock reservation.
*   **Event `b2b.ticket.created`:** Published when support ticket is logged. Consumed by Service module to allocate engineers.
*   **Idempotency Guarantee:** Event payload contains `correlationId`. Duplicate events are discarded by downstream services.

---

## 15. Notifications / SLA Monitoring
*   **SLA Alert: PM Calibration due:** BullMQ cron runs nightly. Emits alert if installed equipment warranty expires in < 30 days.
*   **SLA Alert: Ticket unassigned:** Alert generated if a Priority 1 ticket remains unassigned for > 2 hours.

---

## 16. Audit & Compliance
State changes write to `b2b_portal.audit_log`:
```sql
CREATE TABLE b2b_portal.audit_log (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    entity_type VARCHAR(50) NOT NULL, -- 'order', 'quotation', 'ticket'
    entity_id UUID NOT NULL,
    actor_id UUID NOT NULL,
    previous_state VARCHAR(50),
    new_state VARCHAR(50) NOT NULL,
    changed_at TIMESTAMP DEFAULT now()
);
```

---

## 17. Reporting & Dashboard Requirements
*   **Dashboard View:** Main client screen rendering active orders, pending tickets, credit balance, and scheduled PM calendars.
*   **Ledger Statement:** Real-time generation of outstanding payments, grouping paid vs unpaid invoices.

---

## 18. Frontend Scope
*   **Next.js Portal Dashboard:** Customer-facing panel displaying KPIs.
*   **Catalog Page:** Searchable grid featuring clinical files and manuals.
*   **Shopping Cart:** Supports multi-branch routing drop-downs.
*   **Support Center:** List of installed devices with active warranty indicators.

---

## 19. Backend Scope
*   `CatalogService` -> Applies pricing rules and lists products.
*   `OrderService` -> Handles credit limit verification, idempotency logic, and DB transactions.
*   `ServiceDeskService` -> Manages support tickets and PM reminders.

---

## 20. Testing Strategy
*   **Unit Tests:** Verify contract price calculation, credit guard blocking, and state transitions.
*   **Integration Tests:** Verify checkout writes to orders and order lines atomically.
*   **Authorization Tests:** Validate that requests to `/api/b2b/orders/:id` reject if customerAccountId does not match.

---

## 21. Work Breakdown Structure (WBS)
*   **B2B-F-01:** Scaffold module charter, permissions, and DB migration files.
*   **B2B-D-01:** Create PostgreSQL tables (`orders`, `quotations`, `tickets`, `installed_equipment`).
*   **B2B-B-01:** Code `CatalogService` and contract pricing queries.
*   **B2B-B-02:** Code `OrderService` with credit check and idempotency checks.
*   **B2B-UI-01:** Code dashboard Next.js interface.
*   **B2B-UI-02:** Code cart checkout and multi-branch mapping pages.
*   **B2B-T-01:** Write unit and E2E integration test suites.

---

## 22. Implementation Phases
*   **Phase 1 (Week 1):** Scaffolding, database schema deployment, views configuration.
*   **Phase 2 (Week 2):** Backend services (Catalog, checkout transaction, ticketing logic).
*   **Phase 3 (Week 3):** Frontend portal interfaces (Dashboard, Catalog, Checkout, Tickets).
*   **Phase 4 (Week 4):** QA testing, performance benchmarking, UAT validation.

---

## 23. Gantt Chart & Milestones

![B2B Commerce Portal Gantt Chart Schedule](file:///C:/Users/hp/.gemini/antigravity/brain/3a0178ba-a910-4b58-8689-be5e04fa2eaf/gantt_chart.png)

```mermaid
gantt
    title B2B Commerce Portal - Four-Week Implementation Schedule (Aug 13 – Sep 10, 2026)
    dateFormat  YYYY-MM-DD
    axisFormat  %b %d
    
    section Phase 0: Foundation
    B2B-F-01: Module Scaffold   :active, b2b_f1, 2026-08-13, 2d
    B2B-F-02: DB Schema Grants  :b2b_f2, after b2b_f1, 1d
    
    section Phase 1: Database Layer
    B2B-D-01: PostgreSQL Schema :b2b_d1, 2026-08-16, 4d
    B2B-D-02: Seed Base Data    :b2b_d2, after b2b_d1, 1d
    
    section Phase 2: Backend Dev
    B2B-B-01: Catalog Price Engine :b2b_b1, 2026-08-21, 3d
    B2B-B-02: Order Checkout logic  :b2b_b2, after b2b_b1, 4d
    B2B-B-03: Multi-Branch Routing :b2b_b3, after b2b_b2, 3d
    B2B-B-04: Support Ticketing    :b2b_b4, after b2b_b3, 3d
    
    section Phase 3: Frontend Dev
    B2B-UI-01: Dashboard portal view:b2b_ui1, 2026-08-28, 3d
    B2B-UI-02: Cart & Catalog Search:b2b_ui2, after b2b_ui1, 4d
    B2B-UI-03: Multi-Branch UX     :b2b_ui3, after b2b_ui2, 3d
    B2B-UI-04: Ticket logger screen :b2b_ui4, after b2b_ui3, 2d
    
    section Phase 4: Testing & Hardening
    B2B-T-01: Unit Tests & coverage :b2b_t1, 2026-09-04, 3d
    B2B-T-02: Integration Tests     :b2b_t2, after b2b_t1, 2d
    B2B-T-03: E2E Validation        :b2b_t3, after b2b_t2, 2d
    B2B-H-01: UAT Sign-off & deploy :b2b_h1, after b2b_t3, 1d
    
    section Milestones
    M1: Foundation Complete   :milestone, m1, 2026-08-15, 0d
    M2: Backend Ready         :milestone, m2, 2026-08-27, 0d
    M3: UI Integrated         :milestone, m3, 2026-09-03, 0d
    M4: Production Ready      :milestone, m4, 2026-09-10, 0d
```

*   **M1 (End of Week 1):** Database migrations applied, database grants validated.
*   **M2 (End of Week 2):** Backend REST services pass local integration tests.
*   **M3 (End of Week 3):** Frontend dashboard and checkout operational.
*   **M4 (End of Week 4):** UAT verified, staging deployment finalized.

---

## 24. Definition of Done
*   All 16 functional requirements verified.
*   Zero lint or file-size violations on PRs.
*   100% of state transitions write to `b2b_portal.audit_log`.
*   Test coverage on application services exceeds 85%.

---

## 25. Risks / Dependencies / Open Decisions
*   **Risk:** Finance module database view format changes. *Mitigation:* Integration test matches external DTO contracts.
*   **Decision:** Manual bank slip storage path. *Resolution:* Store in `Files` module, keep reference ID in `payment_slips`.

---

## 26. Traceability Matrix
| Req ID | Capability | Workflow | Entity | Business Rule | API | WBS Task |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **FR-CP-001** | Account portal | WF-B2B-01 | CustomerUser | — | `GET /api/b2b/profile` | B2B-F-01, B2B-B-01 |
| **FR-CP-002** | Catalog | WF-B2B-02 | Catalog | BR-B2B-04 | `GET /api/b2b/catalog` | B2B-B-01 |
| **FR-CP-003** | Ordering | WF-B2B-02 | B2BOrder | BR-B2B-02 | `POST /api/b2b/orders` | B2B-B-02, B2B-UI-02 |
| **FR-CP-004** | RFQ Flow | WF-B2B-03 | B2BQuotation | — | `POST /api/b2b/quotations` | B2B-B-02 |
| **FR-CP-008** | Support tickets| WF-B2B-04 | SupportTicket| — | `POST /api/b2b/tickets` | B2B-B-02 |
| **FR-CP-012** | Contract prices| WF-B2B-02 | Catalog | — | `GET /api/b2b/catalog` | B2B-B-01 |
| **FR-CP-014** | Credit visibility| WF-B2B-02 | B2BOrder | BR-B2B-01 | `GET /api/b2b/billing/statements` | B2B-B-02 |
| **FR-CP-016** | Multi-branch | WF-B2B-02 | B2BOrderLine | — | `POST /api/b2b/orders` | B2B-B-02, B2B-UI-02 |

---

## 27. Planning Gate Checklist
*   [x] Scope defined and mapped.
*   [x] All 8 B2B workflows detailed.
*   [x] State machines catalogued.
*   [x] Data dependencies and view references mapped.
*   [x] Permissions matrix finalized.
*   [x] Traceability matrix completed.

---
*End of Document.*
