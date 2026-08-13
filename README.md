# STARCHAIN DIGITAL HEALTH COMMERCE ECOSYSTEM
## B2B Commerce Portal Module — Implementation Plan
**Client:** Starchain Medical (Addis Ababa, Ethiopia)  
**Prepared By:** HEMIN Business PLC  
**Document Reference:** IMP-PLAN/B2B-PORTAL/001  
**Version:** 1.0 — Release 1  
**Date Prepared:** August 13, 2026  
**Technology Stack:** NestJS (API) · Next.js App Router (Frontend) · PostgreSQL 16 · Prisma · BullMQ  
**Status:** Ready for Planning-Gate Review and Implementation Authorization  
**Framework Baseline:** Aligned to Foundation Architecture and Planning Framework v2.1  
**Module Owner:** Senior Developer (B2B Commerce Domain)  
**Kickoff Date:** Monday, August 17, 2026  
**Target Completion:** Friday, September 11, 2026 (4-week sprint)  

---

## Table of Contents
1. [Executive Summary](#1-executive-summary)
2. [Project Overview](#2-project-overview)
3. [Module Overview](#3-module-overview)
4. [Business Objectives](#4-business-objectives)
5. [Module Scope](#5-module-scope)
6. [Module Charter](#6-module-charter)
7. [Business Capability Map](#7-business-capability-map)
8. [Functional Decomposition](#8-functional-decomposition)
9. [Domain Glossary](#9-domain-glossary)
10. [Entity Catalogue](#10-entity-catalogue)
11. [Data Ownership Matrix](#11-data-ownership-matrix)
12. [State Models](#12-state-models)
13. [Business Rules](#13-business-rules)
14. [Workflow Catalogue](#14-workflow-catalogue)
15. [Integration Requirements](#15-integration-requirements)
16. [Module Dependencies](#16-module-dependencies)
17. [Architecture Alignment](#17-architecture-alignment)
18. [Repository Structure](#18-repository-structure)
19. [API Boundaries](#19-api-boundaries)
20. [Security Considerations](#20-security-considerations)
21. [Work Breakdown Structure (WBS)](#21-work-breakdown-structure-wbs)
22. [Resource Allocation](#22-resource-allocation)
23. [Four-Week Development Timeline](#23-four-week-development-timeline)
24. [Milestones](#24-milestones)
25. [Deliverables](#25-deliverables)
26. [Risks](#26-risks)
27. [Risk Mitigation](#27-risk-mitigation)
28. [Quality Assurance](#28-quality-assurance)
29. [Testing Strategy](#29-testing-strategy)
30. [Deployment Readiness](#30-deployment-readiness)
31. [References](#31-references)
32. [Appendix](#32-appendix)

---

## 1. Executive Summary
This document defines the implementation plan for the B2B Commerce Portal of Starchain Medical. It supports secure customer self-service, searchable medical catalogs with EFDA certifications, online ordering with credit/real-time stock checks, RFQ workflows, order status tracking, invoice downloads, support tickets, and multi-branch delivery mapping. 

The plan is designed for immediate developer onboarding and strictly adheres to the HEMIN ERP Foundation Framework v2.1. It is aligned with the Warehouse & Inventory pilot findings and Starchain Medical's regulatory policies.

---

## 2. Project Overview
*   **Programme Name:** Hemin Business plc — Enterprise ERP
*   **Module Name:** B2B Commerce Portal (`b2b_portal`)
*   **Release Scope:** Release 1 (MVP)
*   **Tech Stack:** NestJS, TypeScript, Next.js App Router, PostgreSQL 16, Prisma ORM, BullMQ, class-validator
*   **Pattern:** Modular Monolith with schema-per-module isolation (`b2b_portal.*`)

---

## 3. Module Overview
*   **Business Purpose:** Provide a digital, self-service channel for hospital procurement, biomedical engineers, and pharmacy managers to discover products, request quotations, place orders, track shipments, view invoices/balances, and log support tickets for installed medical equipment.
*   **Primary Users:** Hospital Procurement Officers, Biomedical Engineers, Hospital Finance Officers.
*   **Secondary Users:** Starchain Sales Agents, Service Engineers, Warehouse Managers.
*   **PostgreSQL Schema:** `b2b_portal.*`
*   **Upstream Dependencies:** Identity, Organization, Inventory, Sales, Finance.
*   **Downstream Consumers:** Warehouse & Inventory (fulfillment trigger), Finance (payment status).
*   **Published Events:** `b2b.order.placed`, `b2b.quotation.requested`, `b2b.ticket.created`.

---

## 4. Business Objectives
| ID | Business Objective | Measurable Outcome |
| :--- | :--- | :--- |
| **BO-01** | Digitize customer order entry and self-service account status. | 100% of B2B orders processed digitally; zero paper-based quotes. |
| **BO-02** | Enforce EFDA regulatory registration checks before checkout. | Zero sales of unregistered medical devices to hospitals. |
| **BO-03** | Provide accurate contract pricing overrides. | Enforce pre-negotiated discount rates on checkout automatically. |
| **BO-04** | Improve after-sales equipment service transparency. | PM alerts sent automatically; corrective service requests logged within 1 hour. |
| **BO-05** | Minimize order errors across hospital branches. | Multi-branch routing maps department and room allocations on checkout. |

---

## 5. Module Scope
### 5.1 Included Capabilities (Release 1)
*   **CRM & Self-Service Account:** Customer portal access (FR-CP-001), Account dashboard (FR-CP-010), User & branch management (FR-CP-009).
*   **Catalog & Pricing:** Digital catalog with documents (FR-CP-002), Customer-specific catalog scopes (FR-CP-011), Contract pricing overrides (FR-CP-012), Real-time stock check (FR-CP-015).
*   **Ordering & Invoicing:** Digital checkout (FR-CP-003), RFQ requests (FR-CP-004), Multi-branch ordering (FR-CP-016), Repeat orders (FR-CP-013), Credit limits (FR-CP-014), Invoice downloads (FR-CP-006), Order tracking (FR-CP-005).
*   **Service & Ticketing:** Support ticketing (FR-CP-008), Installed equipment registry (after-sales calibration history).

### 5.2 Excluded Capabilities (Release 1)
*   Automated local payment gateway settlement (deferred to manual bank slip uploads in Release 1).
*   Warehouse pick-pack-ship operations (managed in core Inventory schema).
*   Supplier Portal interfaces (planned for Release 2).

---

## 6. Module Charter
*   **Business Purpose:** Eliminate transaction chasing and provide hospital biomedical and procurement teams absolute visibility of stock, prices, orders, warranties, and maintenance calendars.
*   **Owned Data:** `b2b_portal.orders`, `b2b_portal.quotations`, `b2b_portal.tickets`, `b2b_portal.installed_equipment`, `b2b_portal.carts`, `b2b_portal.audit_log`.
*   **Consumed External Data:** identity.users, inventory.items, sales.contract_pricing, finance.customer_credit.
*   **Non-Functional Requirements:** Dashboard page load < 500ms, checkout request idempotency, audit trail logging.

---

## 7. Business Capability Map
```
B2B COMMERCE PORTAL
■ CUSTOMER SELF-SERVICE
  ├── Account Portal (FR-CP-001)
  ├── User & Branch Mapping (FR-CP-009)
  └── Financial Statements (FR-CP-006)
■ PRODUCT DISCOVERY & CATALOG
  ├── Digital Catalog & Specs (FR-CP-002)
  ├── Customer Scopes (FR-CP-011)
  └── Real-time Stock Check (FR-CP-015)
■ TRANSACTION ENGINE
  ├── Shopping Cart (FR-CP-003, FR-CP-016)
  ├── RFQ & Tender Quotes (FR-CP-004)
  └── Repeat Order Replenish (FR-CP-013)
■ AFTER-SALES SERVICE
  ├── Support Tickets (FR-CP-008)
  └── Installed Base calibration registry
```

---

## 8. Functional Decomposition

### FR-CP-001 Customer Self-Service Portal
*   **Description:** Secure portal for hospital users to manage settings, credentials, and roles (Biomedical, Procurement, Finance).
*   **Requirements:** Argon2 hashing, session-based auth, role assignment.

### FR-CP-002 Digital Product Catalog
*   **Description:** Searchable catalog grouped by clinical categories (NICU, ICU, Emergency OPD).
*   **Requirements:** View-only access to `inventory.v_available_products`, downloads for user manuals and EFDA certificates.

### FR-CP-003 Online Ordering
*   **Description:** Cart checkout converting draft lines into official order records.
*   **Requirements:** Enforce `Idempotency-Key` headers, create order in `Draft` state.

### FR-CP-004 Quotation Request Management
*   **Description:** Hospital procurement request for structured quotation (RFQ) for high-value equipment.
*   **Requirements:** Workflow moves from `Draft` to `Submitted`, routed to Sales Manager.

### FR-CP-005 Order Status Tracking
*   **Description:** Timeline visualization of order progress.
*   **Requirements:** Stages: Draft -> Checkout -> Placed -> Confirmed -> Shipped -> Delivered.

### FR-CP-006 Invoice and Statement Access
*   **Description:** Customer ledger display containing invoices, statements, and paid status.
*   **Requirements:** Integrates via `finance.v_customer_invoices` view.

### FR-CP-007 Digital Payment Support
*   **Description:** Manual bank transfer slip upload and validation tracking.
*   **Requirements:** File upload interface, verification workflow.

### FR-CP-008 Customer Support Ticketing
*   **Description:** Support ticket logging for installed equipment corrective maintenance.
*   **Requirements:** Input: Serial number, department, description, priority.

### FR-CP-009 Customer User and Branch Management
*   **Description:** Map users to specific corporate branches.
*   **Requirements:** Integrates via `org.v_customer_branches`.

### FR-CP-010 Customer Account Dashboard
*   **Description:** Overview of active orders, outstanding invoices, service tickets, and PM reminders.
*   **Requirements:** Unified REST aggregation service.

### FR-CP-011 Customer-Specific Catalogs
*   **Description:** Limit items in catalog based on customer account classification.
*   **Requirements:** Filter catalog by customer category mapping.

### FR-CP-012 Contract Pricing Management
*   **Description:** Apply contract-specific discounts.
*   **Requirements:** Read override prices from `sales.v_contract_pricing`.

### FR-CP-013 Repeat Order Functionality
*   **Description:** Clone past orders to create a new checkout cart.
*   **Requirements:** Copy previous order items to draft cart.

### FR-CP-014 Customer Credit Visibility
*   **Description:** Display credit limit and current outstanding balance.
*   **Requirements:** Reads `finance.v_customer_credit`.

### FR-CP-015 Product Availability Check
*   **Description:** Render stock levels (In Stock, Out of Stock, or Lead Time).
*   **Requirements:** Read quantities from `inventory.v_available_products`.

### FR-CP-016 Multi-Branch Ordering
*   **Description:** Dispatch items within a single order to different physical departments/branches.
*   **Requirements:** Map `branch_id` and `department_id` to individual order line items.

---

## 9. Domain Glossary
*   **Biomedical Officer:** Hospital team member responsible for testing, validating, and requesting support for medical devices.
*   **Customer User:** Hospital employee (procurement, biomedical, finance) with portal credentials.
*   **Customer Branch:** Physical hospital branch or clinic under a parent healthcare system.
*   **PM Visit:** Scheduled calibration and maintenance check required to keep equipment warranties active.
*   **Credit Status:** Read-only metric indicating whether a hospital has defaulted or exceeded its credit limit.

---

## 10. Entity Catalogue
### 10.1 Core Entities
*   **B2BOrder:** Main order registry (`b2b_portal.orders`). Must have `customer_id` and unique transaction code.
*   **B2BOrderLine:** Order line items (`b2b_portal.order_lines`). Requires `product_id`, `quantity`, `price`, `branch_id`, and `department_id`.
*   **B2BQuotation:** Quotation record requested by customer (`b2b_portal.quotations`).
*   **SupportTicket:** Corrective maintenance log (`b2b_portal.tickets`).
*   **InstalledEquipment:** Register of medical devices deployed at customer locations (`b2b_portal.installed_equipment`).
*   **Cart:** Temporary storage of items before checkout (`b2b_portal.carts`).

---

## 11. Data Ownership Matrix
| Data Entity | Owner Module | Readable By | Write Access | Cross-Module Interface |
| :--- | :--- | :--- | :--- | :--- |
| `b2b_portal.orders` | B2B Portal | Sales, Finance, Inventory | B2B Portal | Published view `b2b_portal.v_orders` |
| `b2b_portal.tickets` | B2B Portal | Service, Sales | B2B Portal | Internal REST Endpoint |
| `inventory.items` | Inventory | B2B Portal (Read-only) | Inventory | `inventory.v_available_products` |
| `finance.customer_credit`| Finance | B2B Portal (Read-only) | Finance | `finance.v_customer_credit` |

---

## 12. State Models
### 12.1 B2B Order State Machine
```
Draft ──> Checkout ──> Placed ──> Confirmed ──> Shipped ──> Delivered
  │          │                                                 ▲
  └── (Cancel) ────────────────────────────────────────────────┘
```

### 12.2 B2B Quotation State Machine
```
Draft ──> Submitted ──> Under_Review ──> Approved ──> Converted_To_Order
                          │
                          └──> Rejected
```

### 12.3 Support Ticket State Machine
```
Open ──> Assigned ──> Diagnosed ──> Resolved ──> Closed
```

---

## 13. Business Rules
*   **BR-B2B-01 (Credit Guard):** Block order checkout if total order value exceeds limit retrieved from `finance.v_customer_credit`.
*   **BR-B2B-02 (Idempotency):** Every POST request on checkout and confirmation endpoints must have a unique `Idempotency-Key` header.
*   **BR-B2B-03 (Audit Trail):** Write an append-only row to `b2b_portal.audit_log` on all order, quotation, and ticket status changes.
*   **BR-B2B-04 (EFDA Verify):** Only products with a valid EFDA license can be placed in order line items.
*   **BR-B2B-05 (Lock Order):** Confirmed orders cannot be edited or deleted. Changes require a new revision.

---

## 14. Workflow Catalogue
### 14.1 Workflow: Order Checkout
*   **Trigger:** Customer user clicks "Place Order" in cart.
*   **Actor:** Customer Procurement Officer
*   **Steps:**
    1. Read cart items and target delivery branches per line.
    2. Invoke credit limit validation via `finance.v_customer_credit`.
    3. Check stock levels on `inventory.v_available_products`.
    4. Call `B2BOrderService.checkout()` with `Idempotency-Key`.
    5. Save order in `Placed` state. Emit event `b2b.order.placed`.

---

## 15. Integration Requirements
### 15.1 Outbound Events (EventEmitter2)
*   `b2b.order.placed`: Consumed by Inventory to reserve stock, and Finance to check invoices.
*   `b2b.ticket.created`: Consumed by Service module to allocate biomedical engineers.

---

## 16. Module Dependencies
`b2b_portal` ──> `identity` (authentication & permissions check)  
`b2b_portal` ──> `inventory` (catalog details & stock count)  
`b2b_portal` ──> `finance` (credit limit & invoice tracking)

---

## 17. Architecture Alignment
*   **File-Size Rules:** Domain/Service logic ≤ 450 lines; UI ≤ 350 lines; Controllers ≤ 250 lines; Tests ≤ 600 lines.
*   **Schema Isolation:** All B2B database writes target the `b2b_portal` schema exclusively. 
*   **Separation of Concerns:** Business logic inside services, not controllers or UI files.

---

## 18. Repository Structure
```
modules/
└── b2b_portal/
    ├── domain/
    │   ├── order/
    │   │   ├── b2b-order.entity.ts
    │   │   └── order-line.entity.ts
    │   └── ticket/
    │       └── ticket.entity.ts
    ├── application/
    │   ├── catalog.service.ts
    │   ├── order.service.ts
    │   └── service-desk.service.ts
    ├── infrastructure/
    │   └── order.repository.ts
    └── interfaces/
        ├── catalog.controller.ts
        ├── order.controller.ts
        └── ticket.controller.ts
```

---

## 19. API Boundaries
| Method | Endpoint | Description | Permission |
| :--- | :--- | :--- | :--- |
| **GET** | `/api/b2b/catalog` | Search catalog items | `b2b.catalog.read` |
| **POST**| `/api/b2b/orders` | Place online order | `b2b.order.create` |
| **GET** | `/api/b2b/orders/:id`| Get order tracking status| `b2b.order.read` |
| **POST**| `/api/b2b/tickets` | Log support ticket | `b2b.ticket.create` |

---

## 20. Security Considerations
*   **Permissions:** `b2b.order.create`, `b2b.order.read`, `b2b.catalog.read`, `b2b.ticket.create`.
*   **Scope Isolation:** Every query verifies the user's `customer_id` context to prevent cross-customer data leakage.

---

## 21. Work Breakdown Structure (WBS)
*   **WBS 1:** DB Schema & Scaffolding (Week 1)
*   **WBS 2:** Backend API Services & Business Rules (Week 2)
*   **WBS 3:** Frontend Next.js Interface (Week 3)
*   **WBS 4:** E2E, Idempotency & Performance Testing (Week 4)

---

## 22. Resource Allocation
*   **Senior Developer:** Design boundaries, security reviews.
*   **Backend Developer:** API controllers, DB transactions, BullMQ.
*   **Frontend Developer:** Next.js pages, responsive portal UI.
*   **QA Engineer:** Test scripts, security testing, UAT.

---

## 23. Four-Week Development Timeline
*   **W1 (Aug 17 - Aug 21):** Setup schema migrations and API contracts.
*   **W2 (Aug 24 - Aug 28):** Implement services (checkout logic, credit guard, idempotency interceptor).
*   **W3 (Aug 31 - Sep 04):** Build frontend Next.js dashboard, cart, ticket creation.
*   **W4 (Sep 07 - Sep 11):** Run unit, integration, auth, and performance test suites.

---

## 24. Milestones
*   **M1 (Aug 21):** Schema and contract definitions frozen.
*   **M2 (Aug 28):** Backend endpoint services smoke-tested.
*   **M3 (Sep 04):** Frontend portal interfaces completed.
*   **M4 (Sep 11):** UAT sign-off and deployment to staging.

---

## 25. Deliverables
*   `b2b_portal` PostgreSQL tables.
*   `catalog`, `order`, and `ticket` API controllers.
*   Next.js customer dashboard and checkout interfaces.
*   Full Jest test suites.

---

## 26. Risks
*   **R-01:** Customer credit check delays.
*   **R-02:** Concurrency overselling during high order volumes.

---

## 27. Risk Mitigation
*   **M-01:** Cache credit status values for 10 minutes; execute synchronous DB double-check only on checkout.
*   **M-02:** Enforce PostgreSQL optimistic locking on item quantities.

---

## 28. Quality Assurance
*   Lint gates block commits exceeding the framework's file-size limits.
*   Strict coverage target of 85% on application services.

---

## 29. Testing Strategy
*   **Unit Tests:** Guard logic (valid EFDA, negative quantities blocked).
*   **Integration Tests:** Post order checkout details verify database writes.
*   **Idempotency Tests:** Send identical order payload twice; assert single write.

---

## 30. Deployment Readiness
*   Validation check that database grants for published views (`inventory.v_available_products`) are initialized in the target environment.

---

## 31. References
*   HEMIN ERP Foundation and Planning Framework v2.1.
*   Starchain Medical Device Distribution Guidelines.

---

## 32. Appendix

### 32.1 Sample NestJS Service: Order Service
```typescript
import { Injectable, BadRequestException } from '@nestjs/common';

@Injectable()
export class B2BOrderService {
  async checkout(orderDto: any, idempotencyKey: string): Promise<any> {
    // 1. Validate Idempotency
    const existingOrder = await this.findOrderByIdempotency(idempotencyKey);
    if (existingOrder) return existingOrder;

    // 2. Check Credit Limit
    const credit = await this.checkCustomerCredit(orderDto.customerId);
    if (credit.available < orderDto.totalAmount) {
      throw new BadRequestException('CREDIT_LIMIT_EXCEEDED');
    }

    // 3. Process Transaction
    return await this.dbTransaction(orderDto, idempotencyKey);
  }

  private async findOrderByIdempotency(key: string) { return null; }
  private async checkCustomerCredit(id: string) { return { available: 500000 }; }
  private async dbTransaction(dto: any, key: string) { return { success: true, orderId: 'b2b-102' }; }
}
```

### 32.2 Sample DTO: CreateOrderDto
```typescript
import { IsString, IsArray, ArrayMinSize, ValidateNested } from 'class-validator';
import { Type } from 'class-transformer';

export class CreateOrderLineDto {
  @IsString()
  productId: string;

  @IsString()
  branchId: string;

  @IsString()
  departmentId: string;
}

export class CreateB2BOrderDto {
  @IsString()
  customerId: string;

  @IsArray()
  @ArrayMinSize(1)
  @ValidateNested({ each: true })
  @Type(() => CreateOrderLineDto)
  lines: CreateOrderLineDto[];
}
```

### 32.3 PostgreSQL Schema Excerpt
```sql
CREATE TABLE b2b_portal.orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL,
    order_number VARCHAR(100) UNIQUE NOT NULL,
    status VARCHAR(50) DEFAULT 'draft',
    idempotency_key VARCHAR(255) UNIQUE NOT NULL,
    total_amount NUMERIC(15,2) NOT NULL,
    created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE b2b_portal.order_lines (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID REFERENCES b2b_portal.orders(id) ON DELETE CASCADE,
    product_id UUID NOT NULL,
    quantity INTEGER NOT NULL CHECK (quantity > 0),
    branch_id UUID NOT NULL,
    department_id UUID NOT NULL
);
```
