# ERP-B2B-DOCUMENTATION
# HEMIN BUSINESS PLC
## ERP Programme
### B2B Commerce Portal Module
#### Implementation Plan

---

## Table of Contents

1. [Executive Summary & Overview](#1-executive-summary--overview)
   - 1.1 Executive Summary
   - 1.2 Project Overview
   - 1.3 Module Overview
   - 1.4 Business Objectives
   - 1.5 Module Scope
   - 1.6 Module Charter

2. [Business Capabilities & Workflows](#2-business-capabilities--workflows)
   - 2.1 Business Capability Map
   - 2.2 Functional Decomposition
   - 2.3 Domain Glossary
   - 2.4 Entity Catalogue
   - 2.5 Data Ownership Matrix
   - 2.6 State Models
   - 2.7 Business Rules
   - 2.8 Workflow Catalogue

3. [Architecture & Integration](#3-architecture--integration)
   - 3.1 Integration Requirements
   - 3.2 Module Dependencies
   - 3.3 Architecture Alignment
   - 3.4 Repository Structure
   - 3.5 API Boundaries (REST Endpoints)
   - 3.6 Security Considerations & RBAC

4. [Project Management & Execution](#4-project-management--execution)
   - 4.1 Work Breakdown Structure (WBS)
   - 4.2 Resource Allocation
   - 4.3 Four-Week Development Timeline
   - 4.4 Milestones
   - 4.5 Deliverables

5. [Quality, Risk & Readiness](#5-quality-risk--readiness)
   - 5.1 Risks & Risk Mitigation
   - 5.2 Quality Assurance
   - 5.3 Testing Strategy
   - 5.4 Deployment Readiness

---

## 1. Executive Summary & Overview

### 1.1 Executive Summary

This document defines the complete, implementation-ready plan for the B2B Commerce Portal Module of the Hemin Business PLC ERP system. The primary goal of this solution is to empower B2B customers with a comprehensive digital self-service platform. It facilitates digital customer ordering, product discovery, quotation requests, contract pricing, order tracking, and customer support services.

The plan is structured to enable a cross-functional engineering team to execute the development within a 4-week sprint. Every section strictly complies with the Foundation Architecture and Planning Framework v2.1, ensuring that the module adheres to the established NestJS/Next.js technology stack, modular monolith design, and strict database schema ownership (b2b_portal.*).

### 1.2 Project Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Programme Name** | Hemin Business PLC — Enterprise ERP |
| **Module Team** | B2B Commerce Engineering Team |
| **Release Scope** | Release 1 (MVP) |
| **Target Completion** | 4-Week Sprint |
| **Technology Stack** | NestJS + TypeScript (API) • Next.js App Router (Frontend) • PostgreSQL 16 • Prisma ORM • BullMQ |
| **Architecture Pattern** | Modular Monolith with strict schema-per-module ownership |

### 1.3 Module Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Module Name** | b2b_commerce_portal |
| **Business Purpose** | To provide a centralized digital hub for customers to manage their accounts, discover products, request quotations, place multi-branch orders, track deliveries, view financials, and submit support tickets independently. |
| **Primary Users** | B2B Customers, Customer Branch Managers, Procurement Officers |
| **Secondary Users** | Internal Sales Team, Customer Support Representatives, Finance (Read-only) |
| **Owned PostgreSQL Schema** | b2b_portal.* |
| **Upstream Dependencies** | Identity (users/roles), Organization (branches), Inventory (product catalog & availability), Finance (credit limits & invoices), Sales (approved contract pricing) |
| **Published Events** | b2b.order.placed, b2b.quotation.requested, b2b.ticket.created |

### 1.4 Business Objectives

The B2B Commerce Portal module must enable the business to achieve the following:

- **BO-01:** Digitize the B2B purchasing experience to reduce manual order entry and sales team administrative load (FR-CP-003, FR-CP-013, FR-CP-016).
- **BO-02:** Provide real-time transparency into product availability, standard catalogs, and customer-specific contract pricing (FR-CP-002, FR-CP-011, FR-CP-012, FR-CP-015).
- **BO-03:** Empower customers with self-service account management, including financial visibility and multi-branch controls (FR-CP-001, FR-CP-006, FR-CP-009, FR-CP-010, FR-CP-014).
- **BO-04:** Streamline communication for quotations, digital payments, and post-sales support/ticketing (FR-CP-004, FR-CP-005, FR-CP-007, FR-CP-008).

### 1.5 Module Scope

**In-Scope Capabilities (Release 1):**

- **Account & Identity:** Secure customer login, branch management, and user role provisioning.
- **Discovery & Pricing:** Digital product catalog, availability checks, customer-specific catalogs, and contract pricing.
- **Transactions:** Online ordering, multi-branch ordering, repeat order functionality, and quotation requests.
- **Finance & Tracking:** Order status tracking, invoice/statement access, credit visibility, and digital payment initiation.
- **Support:** Customer support ticketing and comprehensive account dashboard.

**Excluded Capabilities (Release 1):**

- **Warehouse Fulfillment:** The actual picking, packing, and dispatching of the B2B orders (Owned by the Inventory module).
- **GL Journal Entries:** Direct accounting ledger entries for digital payments (Owned by the Finance module).
- **Mobile Application:** A native iOS/Android mobile app is deferred to Release 2.

### 1.6 Module Charter

| **Attribute** | **Details** |
|---------------|-------------|
| **Module Name** | B2B Commerce Portal |
| **Module Owner** | Senior Developer (B2B Commerce Domain) |
| **Included Capabilities** | All 16 Functional Requirements (FR-CP-001 through FR-CP-016) |
| **Owned Data** | b2b_portal.users, b2b_portal.orders, b2b_portal.quotation_requests, b2b_portal.tickets, b2b_portal.catalogs |

---

## 2. Business Capabilities & Workflows

### 2.1 Business Capability Map

**■ CUSTOMER ACCOUNT MANAGEMENT**

- Self-Service Access (FR-CP-001)
- User & Branch Management (FR-CP-009)
- Account Dashboard (FR-CP-010)

**■ CATALOG & PRICING**

- Digital Product Catalog (FR-CP-002)
- Customer-Specific Catalogs (FR-CP-011)
- Contract Pricing (FR-CP-012)
- Product Availability Check (FR-CP-015)

**■ ORDERING & QUOTATION**

- Online Ordering (FR-CP-003)
- Quotation Requests (FR-CP-004)
- Repeat Orders (FR-CP-013)
- Multi-Branch Ordering (FR-CP-016)

**■ FINANCE, TRACKING & SUPPORT**

- Order Status Tracking (FR-CP-005)
- Invoice, Statement & Credit Visibility (FR-CP-006, FR-CP-014)
- Digital Payment Support (FR-CP-007)
- Customer Support Ticketing (FR-CP-008)

### 2.2 Functional Decomposition

#### 2.2.1 Customer Account Management

- **FR-CP-001 (Customer Self-Service Portal):** Provide secure, authenticated customer access to B2B account services via the Next.js frontend.
- **FR-CP-009 (User and Branch Management):** Support the administration of customer user roles and map them to specific customer branches.
- **FR-CP-010 (Customer Account Dashboard):** Provide a unified dashboard displaying active orders, open support tickets, and high-level financial status.

#### 2.2.2 Catalog & Pricing Management

- **FR-CP-002 (Digital Product Catalog):** Provide a searchable, categorized digital product catalog surfacing approved product specifications.
- **FR-CP-011 (Customer-Specific Catalogs):** Dynamically filter the master catalog to display only the products approved for a specific customer profile.
- **FR-CP-012 (Contract Pricing Management):** Automatically apply pre-negotiated customer contract pricing to catalog items overriding standard pricing.
- **FR-CP-015 (Product Availability Check):** Provide real-time stock checks by referencing the Inventory module's published views.

#### 2.2.3 Ordering & Quotation Management

- **FR-CP-003 (Online Ordering):** Allow authenticated customers to build a cart and place formal orders through the portal.
- **FR-CP-016 (Multi-Branch Ordering):** Support consolidated ordering across multiple branches, allowing line-item delivery routing per branch.
- **FR-CP-004 (Quotation Request):** Enable customers to request customized formal quotations online, routing requests directly to the Sales module.
- **FR-CP-013 (Repeat Order Functionality):** Allow customers to duplicate a historical order into a new cart to expedite recurring replenishment.

#### 2.2.4 Finance, Tracking & Support

- **FR-CP-005 (Order Status Tracking):** Provide real-time visibility into order processing and delivery status.
- **FR-CP-006 (Invoice & Statement Access):** Allow customers to download invoices, account statements, and view outstanding balances.
- **FR-CP-014 (Customer Credit Visibility):** Display the customer's approved credit limit and current available credit balance.
- **FR-CP-007 (Digital Payment Support):** Support digital payment initiation via secure third-party payment gateways.
- **FR-CP-008 (Customer Support Ticketing):** Allow customers to raise, update, and track service requests directly within the portal.

### 2.3 Domain Glossary

- **B2B Portal:** The customer-facing digital ecosystem where business clients interact autonomously.
- **Customer-Specific Catalog:** A constrained view of the master product catalog, restricted to items approved for sale to a specific customer.
- **Contract Pricing:** Specialized, pre-negotiated pricing tiers assigned to a customer account that supersede standard list prices.

### 2.4 Entity Catalogue

These entities belong exclusively to the b2b_portal PostgreSQL schema.

- **PortalUser:** Must link to exactly one verified customer account in the core CRM; requires secure session authentication.
- **PortalOrder:** An order placed via the portal must pass credit visibility checks before emitting the b2b.order.placed event.
- **QuotationRequest:** Captures requested items and quantities; immutable once submitted to the Sales module.
- **SupportTicket:** Cannot be closed without a documented resolution reason and timestamp.

### 2.5 Data Ownership Matrix

The B2B Portal module adheres to strict schema isolation. Cross-module reads occur exclusively via published views or application-service calls.

| **Entity** | **Owned By** | **Access Pattern** |
|------------|--------------|-------------------|
| b2b_orders | b2b_portal | Written by B2B Only. Read by Sales. |
| items | inventory | Read by B2B via inventory.v_available_products. |
| customer_credit | finance | Read by B2B via finance.v_customer_credit. |

### 2.6 State Models

**B2B Order State Machine**

- **Draft (Cart) → Checkout:** Cart contains at least one item and user initiates checkout.
- **Checkout → Placed:** Delivery branches specified, and Product Availability Check passes.
- **Placed → Confirmed:** Event acknowledged by Sales/Inventory modules. Portal updates Order Status Tracking.

**Support Ticketing State Machine**

- **New → In_Progress:** Internal support agent assigns and opens the ticket.
- **In_Progress → Resolved:** Issue is addressed; resolution notes are attached.
- **Resolved → Closed:** Customer accepts resolution, or auto-closes after 7 days.

### 2.7 Business Rules

- **BR-B2B-01 (Contract Pricing Precedence):** If a customer has an active contract price for an item, it must completely override the standard catalog price.
- **BR-B2B-02 (Credit Limit Enforcement):** A PortalOrder cannot transition to Placed if the order total exceeds the available balance shown in Customer Credit Visibility.
- **BR-B2B-03 (Multi-Branch Constraints):** During Multi-Branch Ordering, every line item in the cart must be mapped to a valid, pre-registered branch location.

### 2.8 Workflow Catalogue

**Workflow: Online B2B Ordering (FR-CP-003, 015, 016)**

1. Portal User browses Digital Product Catalog and applies Customer-Specific filters.
2. User adds items to cart and executes Product Availability Check.
3. User selects delivery destinations, utilizing Multi-Branch Ordering if applicable.
4. User submits order; Order state moves to Placed.
5. System validates against Customer Credit Visibility limits.
6. System emits b2b.order.placed event to Sales and Inventory modules; Order Status Tracking is initialized.

---

## 3. Architecture & Integration

### 3.1 Integration Requirements

The B2B module reads external data exclusively through published database views or controlled application-service calls. All outbound integrations use NestJS EventEmitter2 for Release 1, migrating to a message broker (BullMQ/Redis) for Release 2.

**Outbound Events:**

- **b2b.order.placed** → Sent to Sales & Inventory. Payload: `{ order_id, customer_id, branch_id, lines, total }`
- **b2b.quotation.requested** → Sent to Sales. Payload: `{ quote_req_id, customer_id, items, required_by_date }`
- **b2b.ticket.created** → Sent to Customer Service. Payload: `{ ticket_id, customer_id, issue_type, description }`

**Inbound Data Consumed:**

- `inventory.v_available_products` → Used for Digital Product Catalog & Availability Check.
- `sales.v_contract_pricing` → Used for Contract Pricing Management.
- `finance.v_customer_financials` → Used for Invoice Access & Credit Visibility.
- `identity.v_active_users` → Used for Secure Portal Login.

### 3.2 Module Dependencies

- `b2b_portal` ➔ reads from identity, inventory, sales, and finance.
- `sales` ➔ consumes b2b.order.placed and b2b.quotation.requested events.
- `inventory` ➔ consumes b2b.order.placed for stock reservation.

*(No circular dependencies exist. B2B reads via views and publishes events asynchronously.)*

### 3.3 Architecture Alignment

- **File-size limits:** Enforced by CI check file-size ESLint rule.
- **Dependency direction:** NestJS module structure ensures controllers inject ApplicationServices, never domain providers directly.
- **No cross-schema table access:** PostgreSQL role grants on b2b_portal.* schema only; dependency-cruiser CI rule forbids cross-schema imports.
- **Frontend authorization logic:** Every Next.js Server Action in the portal calls requirePermission() server-side; enforced by CI contract tests.
- **Background jobs via BullMQ:** Statement generation and email notifications handled asynchronously.

### 3.4 Repository Structure

Following the established monorepo modular monolith layout:

```plaintext
hemin-erp/
├── modules/b2b-portal/
│   ├── CHARTER.md & AGENTS.md
│   ├── domain/               # Pure business logic (Models)
│   ├── application/          # OrderService, CatalogService, SupportService
│   ├── infrastructure/       # Prisma repositories
│   ├── interfaces/           # REST Controllers
│   └── events/ & authorization/
├── packages/contracts/b2b-portal/ # Shared DTOs
├── packages/database/schema/b2b/  # Prisma schema files
└── apps/web/app/(b2b-portal)/     # Next.js App Router frontend pages
```
# B2B Commerce Portal Module - Implementation Plan

[![NestJS](https://img.shields.io/badge/NestJS-8.0+-red.svg)](https://nestjs.com/)
[![Next.js](https://img.shields.io/badge/Next.js-14.0+-black.svg)](https://nextjs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-blue.svg)](https://www.postgresql.org/)
[![Prisma](https://img.shields.io/badge/Prisma-5.0+-blueviolet.svg)](https://www.prisma.io/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Module Overview](#module-overview)
- [Business Objectives](#business-objectives)
- [Module Scope](#module-scope)
- [Security & RBAC](#security--rbac)
- [Project Execution](#project-execution)
  - [Work Breakdown Structure](#work-breakdown-structure)
  - [Resource Allocation](#resource-allocation)
  - [Development Timeline](#development-timeline)
  - [Milestones](#milestones)
  - [Deliverables](#deliverables)
- [Quality & Risk Management](#quality--risk-management)
  - [Risk Mitigation](#risk-mitigation)
  - [Quality Assurance](#quality-assurance)
  - [Testing Strategy](#testing-strategy)
  - [Deployment Readiness](#deployment-readiness)
- [Architecture & Integration](#architecture--integration)
- [Getting Started](#getting-started)
- [Contributing](#contributing)

---

## 🚀 Project Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Programme** | Hemin Business PLC — Enterprise ERP |
| **Module** | B2B Commerce Portal |
| **Team** | B2B Commerce Engineering Team |
| **Release** | Release 1 (MVP) |
| **Timeline** | 4-Week Sprint |
| **Tech Stack** | NestJS + TypeScript, Next.js App Router, PostgreSQL 16, Prisma ORM, BullMQ |
| **Architecture** | Modular Monolith with strict schema-per-module ownership |

---

## 📦 Module Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Module Name** | `b2b_commerce_portal` |
| **Purpose** | Centralized digital hub for B2B customers to manage accounts, discover products, request quotations, place orders, track deliveries, view financials, and submit support tickets. |
| **Primary Users** | B2B Customers, Customer Branch Managers, Procurement Officers |
| **Secondary Users** | Internal Sales Team, Customer Support Representatives, Finance (Read-only) |
| **Owned Schema** | `b2b_portal.*` |
| **Upstream Dependencies** | Identity (users/roles), Organization (branches), Inventory (product catalog & availability), Finance (credit limits & invoices), Sales (contract pricing) |
| **Published Events** | `b2b.order.placed`, `b2b.quotation.requested`, `b2b.ticket.created` |

---

## 🎯 Business Objectives

The B2B Commerce Portal module enables the business to achieve:

| **ID** | **Objective** | **Supporting FRs** |
|--------|---------------|-------------------|
| **BO-01** | Digitize the B2B purchasing experience to reduce manual order entry and sales team administrative load | FR-CP-003, FR-CP-013, FR-CP-016 |
| **BO-02** | Provide real-time transparency into product availability, standard catalogs, and customer-specific contract pricing | FR-CP-002, FR-CP-011, FR-CP-012, FR-CP-015 |
| **BO-03** | Empower customers with self-service account management, including financial visibility and multi-branch controls | FR-CP-001, FR-CP-006, FR-CP-009, FR-CP-010, FR-CP-014 |
| **BO-04** | Streamline communication for quotations, digital payments, and post-sales support/ticketing | FR-CP-004, FR-CP-005, FR-CP-007, FR-CP-008 |

---

## 📐 Module Scope

### ✅ In-Scope (Release 1)

- **Account & Identity:** Secure login, branch management, user role provisioning
- **Discovery & Pricing:** Digital catalog, availability checks, customer-specific catalogs, contract pricing
- **Transactions:** Online ordering, multi-branch ordering, repeat orders, quotation requests
- **Finance & Tracking:** Order tracking, invoice/statement access, credit visibility, digital payment initiation
- **Support:** Customer support ticketing, comprehensive account dashboard

### ❌ Excluded (Release 1)

- **Warehouse Fulfillment:** Picking, packing, dispatching (Owned by Inventory module)
- **GL Journal Entries:** Direct accounting ledger entries (Owned by Finance module)
- **Mobile App:** Native iOS/Android app (Deferred to Release 2)

---

## 🔐 Security & RBAC

### Tenant & Data Isolation

- **Customer Isolation:** Logged-in portal users can only view data associated with their specific `customer_id`
- **Branch Restrictions:** Users with the `branch_manager` role are restricted strictly to their assigned branch ID for ordering and tracking

### Audit Requirements

Every state transition writes an append-only row to the shared audit log capturing:

- `actor_id` - User who performed the action
- `correlation_id` - Unique identifier for the transaction
- `previous_state` - State before the transition
- `new_state` - State after the transition
- `timestamp` - When the transition occurred

---

## 📊 Project Execution

### Work Breakdown Structure

#### Week 1 — Planning, Architecture & Design
- Business Process Validation for all 16 FRs
- State Machine Design
- API Contract Drafts (DTOs)
- UI Wireframes

#### Week 2 — Backend Development (NestJS)
- PostgreSQL Schema & Prisma Migrations
- CatalogService, PricingService, OrderService, QuotationService
- Permission Guards, RBAC Implementation
- Audit Logging Middleware

#### Week 3 — Frontend Development (Next.js)
- Next.js App Router Authentication Flow
- Customer Account Dashboard & Branch Management UI
- Shopping Cart, Checkout, Multi-Branch Routing UI

#### Week 4 — Testing, Integration & Deployment
- Unit & Integration Tests (Testcontainers + Jest)
- Cross-Module Integration Validation (`b2b.order.placed`)
- Stakeholder UAT & MVP Sign-Off (M4)

---

### Resource Allocation

| **Role** | **Allocation** | **Responsibilities** |
|----------|---------------|---------------------|
| Senior Developer (Module Owner) | 100% | Architecture review, PR reviews |
| Backend Developer | 100% | Prisma migrations, NestJS Services, BullMQ jobs, Event publishers |
| Frontend Developer | 100% | Next.js portal development, responsive UI, API integration |
| QA Engineer | 100% | E2E testing, API endpoint integration tests, UAT |
| DevOps (Shared) | 50% | CI/CD pipeline, staging deployment |

---

### Development Timeline
Week 1: Capability mapping → API contracts → UI wireframes → Architecture review
Week 2: Prisma schema → NestJS services → RBAC → BullMQ jobs → REST controllers
Week 3: Next.js pages → Account Dashboard → API integration → Responsive UI
Week 4: Unit/Integration/Auth/E2E tests → Bug fixes → UAT → Deployment


---

### Milestones

| **Milestone** | **Description** | **Completion Criteria** |
|---------------|-----------------|------------------------|
| **M1** | Planning Complete | Wireframes completed, architecture gate passed |
| **M2** | Backend Complete | NestJS services implemented, Prisma migrations applied |
| **M3** | Frontend Complete | Next.js pages functional, Dashboard live |
| **M4** | MVP Sign-Off | Test suite passing, CI/CD pipeline green, UAT approved |

---

### Deliverables

| **ID** | **Deliverable** | **Description** |
|--------|----------------|-----------------|
| **D-01** | Module Charter & AGENTS.md | Project documentation and AI agent guidelines |
| **D-02** | Prisma Database Schema | `b2b_portal.*` schema & migrations |
| **D-03** | UI Wireframes | Customer portal wireframes and mockups |
| **D-04** | Web Application | Fully functional B2B Next.js web application |
| **D-05** | Test Suites | Unit, Integration, E2E tests & UAT sign-off record |

---

## ⚠️ Quality & Risk Management

### Risk Mitigation

| **Risk ID** | **Description** | **Mitigation** |
|-------------|-----------------|----------------|
| **R-B2B-01** | Real-time catalog/stock availability checks slow down portal UI | Utilize efficient caching mechanisms and materialized views for `inventory.v_available_products` |
| **R-B2B-02** | Customer places order exceeding credit limit due to stale data | Enforce strict, synchronous credit check immediately before placing order |
| **R-B2B-03** | `b2b.order.placed` event fails to reach Sales or Inventory modules | Implement BullMQ dead-letter queues and automated retry mechanisms with exponential backoff |

---

### Quality Assurance

All code merges must pass automated CI gates:

- **Static Analysis:** ESLint (file-size limits), TypeScript type checking
- **Dependency Boundaries:** `dependency-cruiser` blocks cross-module schema imports
- **Database Grants:** `check:db-grants` ensures actual roles match `grants.json`
- **Security:** `npm audit` and Trivy image scans

---

### Testing Strategy

| **Testing Type** | **Scope** |
|------------------|-----------|
| **Unit** | State guards (rejecting order placement if cart is empty); business rule validation |
| **Integration** | DB transactions (placing an order writes to DB and emits event atomically using Testcontainers) |
| **Authorization** | Enforcing tenant and branch isolation |
| **Idempotency** | Preventing duplicate order creation using Idempotency-Key headers |
| **E2E** | Full browser flow (Login → Browse Catalog → Add to Cart → Checkout → View Order Tracking) |

---

### Deployment Readiness

- [ ] All Prisma migrations include a reversible down-migration
- [ ] Dry-run migration against a staging snapshot succeeds without data loss
- [ ] Cross-module event routing verified in staging
- [ ] UAT sign-off received from internal Sales stakeholders and pilot B2B customers

---

## 🏗️ Architecture & Integration

### Integration Requirements

The B2B module reads external data exclusively through published database views or controlled application-service calls. All outbound integrations use NestJS EventEmitter2 for Release 1, migrating to a message broker (BullMQ/Redis) for Release 2.

**Outbound Events:**

| **Event** | **Target** | **Payload** |
|-----------|-----------|-------------|
| `b2b.order.placed` | Sales & Inventory | `{ order_id, customer_id, branch_id, lines, total }` |
| `b2b.quotation.requested` | Sales | `{ quote_req_id, customer_id, items, required_by_date }` |
| `b2b.ticket.created` | Customer Service | `{ ticket_id, customer_id, issue_type, description }` |

**Inbound Data:**

| **Source** | **View** | **Purpose** |
|------------|----------|-------------|
| Inventory | `inventory.v_available_products` | Product Catalog & Availability Check |
| Sales | `sales.v_contract_pricing` | Contract Pricing Management |
| Finance | `finance.v_customer_financials` | Invoice Access & Credit Visibility |
| Identity | `identity.v_active_users` | Secure Portal Login |

### Repository Structure

```plaintext
hemin-erp/
├── modules/b2b-portal/
│   ├── CHARTER.md & AGENTS.md
│   ├── domain/               # Pure business logic (Models)
│   ├── application/          # OrderService, CatalogService, SupportService
│   ├── infrastructure/       # Prisma repositories
│   ├── interfaces/           # REST Controllers
│   └── events/ & authorization/
├── packages/contracts/b2b-portal/ # Shared DTOs
├── packages/database/schema/b2b/  # Prisma schema files
└── apps/web/app/(b2b-portal)/     # Next.js App Router frontend pages
