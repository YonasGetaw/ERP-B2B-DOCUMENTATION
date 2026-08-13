# HEMIN BUSINESS PLC
## ERP Programme

### B2B Commerce Portal Module
#### Implementation Plan

---

**Document Reference:** IMP-PLAN/B2B/001  
**Version:** 1.0 - Release 1 (MVP)  
**Date Prepared:** August 2026  

---

**Technology Stack:**
- NestJS + TypeScript (API)
- Next.js App Router (Frontend)
- PostgreSQL 16
- Prisma ORM
- BullMQ

---

**Status:** Ready for Planning-Gate Review and Implementation Authorization  
**Framework Baseline:** Foundation Architecture and Planning Framework v2.1

---

**Module Owner:** Senior Developer (B2B Commerce Domain)  
**Kickoff Date:** [TBD]  
**Target Completion:** 4-Week Sprint

---

*Confidential — for internal review by Solution Architects, Technical Leads, Project Managers, and ERP Supervisors*


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


## 1. Executive Summary & Overview

### 1.1 Executive Summary

This document defines the complete, implementation-ready plan for the **B2B Commerce Portal Module** of the Hemin Business PLC ERP system. The primary goal of this solution is to empower B2B customers with a comprehensive digital self-service platform. It facilitates digital customer ordering, product discovery, quotation requests, contract pricing, order tracking, and customer support services.

The plan is structured to enable a cross-functional engineering team to execute the development within a **4-week sprint**. Every section strictly complies with the **Foundation Architecture and Planning Framework v2.1**, ensuring that the module adheres to the established NestJS/Next.js technology stack, modular monolith design, and strict database schema ownership (`b2b_portal.*`).

---

### 1.2 Project Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Programme Name** | Hemin Business PLC — Enterprise ERP |
| **Module Team** | B2B Commerce Engineering Team |
| **Release Scope** | Release 1 (MVP) |
| **Target Completion** | 4-Week Sprint |
| **Technology Stack** | NestJS + TypeScript (API) · Next.js App Router (Frontend) · PostgreSQL 16 · Prisma ORM · BullMQ |
| **Architecture Pattern** | Modular Monolith with strict schema-per-module ownership |

---

### 1.3 Module Overview

| **Attribute** | **Details** |
|---------------|-------------|
| **Module Name** | `b2b_commerce_portal` |
| **Business Purpose** | To provide a centralized digital hub for customers to manage their accounts, discover products, request quotations, place multi-branch orders, track deliveries, view financials, and submit support tickets independently. |
| **Primary Users** | B2B Customers, Customer Branch Managers, Procurement Officers |
| **Secondary Users** | Internal Sales Team, Customer Support Representatives, Finance (Read-only) |
| **Owned PostgreSQL Schema** | `b2b_portal.*` |
| **Upstream Dependencies** | Identity (users/roles), Organization (branches), Inventory (product catalog & availability), Finance (credit limits & invoices), Sales (approved contract pricing) |
| **Published Events** | `b2b.order.placed`, `b2b.quotation.requested`, `b2b.ticket.created` |

---

### 1.4 Business Objectives

The B2B Commerce Portal module must enable the business to achieve the following:

| **ID** | **Objective** | **Supporting FRs** |
|--------|---------------|-------------------|
| **BO-01** | Digitize the B2B purchasing experience to reduce manual order entry and sales team administrative load | FR-CP-003, FR-CP-013, FR-CP-016 |
| **BO-02** | Provide real-time transparency into product availability, standard catalogs, and customer-specific contract pricing | FR-CP-002, FR-CP-011, FR-CP-012, FR-CP-015 |
| **BO-03** | Empower customers with self-service account management, including financial visibility and multi-branch controls | FR-CP-001, FR-CP-006, FR-CP-009, FR-CP-010, FR-CP-014 |
| **BO-04** | Streamline communication for quotations, digital payments, and post-sales support/ticketing | FR-CP-004, FR-CP-005, FR-CP-007, FR-CP-008 |

---

### 1.5 Module Scope

**✅ In-Scope Capabilities (Release 1):**

| **Category** | **Capabilities** |
|--------------|------------------|
| **Account & Identity** | Secure customer login, branch management, user role provisioning |
| **Discovery & Pricing** | Digital product catalog, availability checks, customer-specific catalogs, contract pricing |
| **Transactions** | Online ordering, multi-branch ordering, repeat order functionality, quotation requests |
| **Finance & Tracking** | Order status tracking, invoice/statement access, credit visibility, digital payment initiation |
| **Support** | Customer support ticketing, comprehensive account dashboard |

**❌ Excluded Capabilities (Release 1):**

| **Excluded Capability** | **Reason** |
|-------------------------|------------|
| Warehouse Fulfillment | Owned by Inventory module (picking, packing, dispatching) |
| GL Journal Entries | Owned by Finance module (direct accounting ledger entries) |
| Mobile Application | Deferred to Release 2 (native iOS/Android app) |

---

### 1.6 Module Charter

| **Attribute** | **Details** |
|---------------|-------------|
| **Module Name** | B2B Commerce Portal |
| **Module Owner** | Senior Developer (B2B Commerce Domain) |
| **Backup Owner** | Named Backup — to be assigned before Day 2 of implementation sprint |
| **Included Capabilities** | All 16 Functional Requirements (FR-CP-001 through FR-CP-016) |
| **Owned Data** | `b2b_portal.users`, `b2b_portal.orders`, `b2b_portal.quotation_requests`, `b2b_portal.tickets`, `b2b_portal.catalogs` |
| **Consumed External Data** | User/role identities (Identity module), Branch data (Organization module), Product catalog (Inventory module), Credit limits (Finance module), Contract pricing (Sales module) |
| **Published Services / Events** | `PortalOrderService.submitOrder()` · Events: `b2b.order.placed`, `b2b.quotation.requested`, `b2b.ticket.created` |
| **Required Permissions** | See Section 3.6 — Permission Catalogue |
| **Non-Functional Requirements** | Order submission p95 < 500ms; Product catalog query cursor-paginated above 200 rows; Dashboard loads within 2 seconds |
| **Major Risks** | Concurrent order submissions; product availability staleness; credit limit synchronization |


## 2. Business Capabilities & Workflows

### 2.1 Business Capability Map

The B2B Commerce Portal is organized into four core capability clusters:

---

**■ CUSTOMER ACCOUNT MANAGEMENT**

| **Capability** | **FR Reference** |
|----------------|------------------|
| Self-Service Access | FR-CP-001 |
| User & Branch Management | FR-CP-009 |
| Account Dashboard | FR-CP-010 |

**■ CATALOG & PRICING**

| **Capability** | **FR Reference** |
|----------------|------------------|
| Digital Product Catalog | FR-CP-002 |
| Customer-Specific Catalogs | FR-CP-011 |
| Contract Pricing | FR-CP-012 |
| Product Availability Check | FR-CP-015 |

**■ ORDERING & QUOTATION**

| **Capability** | **FR Reference** |
|----------------|------------------|
| Online Ordering | FR-CP-003 |
| Quotation Requests | FR-CP-004 |
| Repeat Orders | FR-CP-013 |
| Multi-Branch Ordering | FR-CP-016 |

**■ FINANCE, TRACKING & SUPPORT**

| **Capability** | **FR Reference** |
|----------------|------------------|
| Order Status Tracking | FR-CP-005 |
| Invoice, Statement & Credit Visibility | FR-CP-006, FR-CP-014 |
| Digital Payment Support | FR-CP-007 |
| Customer Support Ticketing | FR-CP-008 |

---

### 2.2 Functional Decomposition

#### 2.2.1 Customer Account Management

| **FR** | **Description** | **Key Functions** |
|--------|-----------------|-------------------|
| **FR-CP-001** | Customer Self-Service Portal | Provide secure, authenticated customer access to B2B account services via the Next.js frontend |
| **FR-CP-009** | User and Branch Management | Support the administration of customer user roles and map them to specific customer branches |
| **FR-CP-010** | Customer Account Dashboard | Provide a unified dashboard displaying active orders, open support tickets, and high-level financial status |

#### 2.2.2 Catalog & Pricing Management

| **FR** | **Description** | **Key Functions** |
|--------|-----------------|-------------------|
| **FR-CP-002** | Digital Product Catalog | Provide a searchable, categorized digital product catalog surfacing approved product specifications |
| **FR-CP-011** | Customer-Specific Catalogs | Dynamically filter the master catalog to display only the products approved for a specific customer profile |
| **FR-CP-012** | Contract Pricing Management | Automatically apply pre-negotiated customer contract pricing to catalog items overriding standard pricing |
| **FR-CP-015** | Product Availability Check | Provide real-time stock checks by referencing the Inventory module's published views |

#### 2.2.3 Ordering & Quotation Management

| **FR** | **Description** | **Key Functions** |
|--------|-----------------|-------------------|
| **FR-CP-003** | Online Ordering | Allow authenticated customers to build a cart and place formal orders through the portal |
| **FR-CP-016** | Multi-Branch Ordering | Support consolidated ordering across multiple branches, allowing line-item delivery routing per branch |
| **FR-CP-004** | Quotation Request | Enable customers to request customized formal quotations online, routing requests directly to the Sales module |
| **FR-CP-013** | Repeat Order Functionality | Allow customers to duplicate a historical order into a new cart to expedite recurring replenishment |

#### 2.2.4 Finance, Tracking & Support

| **FR** | **Description** | **Key Functions** |
|--------|-----------------|-------------------|
| **FR-CP-005** | Order Status Tracking | Provide real-time visibility into order processing and delivery status |
| **FR-CP-006** | Invoice & Statement Access | Allow customers to download invoices, account statements, and view outstanding balances |
| **FR-CP-014** | Customer Credit Visibility | Display the customer's approved credit limit and current available credit balance |
| **FR-CP-007** | Digital Payment Support | Support digital payment initiation via secure third-party payment gateways |
| **FR-CP-008** | Customer Support Ticketing | Allow customers to raise, update, and track service requests directly within the portal |

---

### 2.3 Domain Glossary

| **Term** | **Definition** |
|----------|----------------|
| **B2B Portal** | The customer-facing digital ecosystem where business clients interact autonomously with Hemin Business PLC |
| **Customer-Specific Catalog** | A constrained view of the master product catalog, restricted to items approved for sale to a specific customer |
| **Contract Pricing** | Specialized, pre-negotiated pricing tiers assigned to a customer account that supersede standard list prices |
| **Portal User** | An authenticated user representing a customer organization with specific role-based permissions |
| **Branch Manager** | A portal user role restricted to ordering and tracking for their assigned branch only |
| **Multi-Branch Order** | A single consolidated order containing line items destined for different customer branch locations |

---

### 2.4 Entity Catalogue

These entities belong exclusively to the `b2b_portal` PostgreSQL schema.

| **Entity** | **Description** | **Key Invariant** |
|------------|-----------------|-------------------|
| **PortalUser** | Customer user account linked to a verified customer in the core CRM | Must link to exactly one verified customer account; requires secure session authentication |
| **PortalOrder** | An order placed via the portal by a customer | Must pass credit visibility checks before emitting `b2b.order.placed` event |
| **PortalOrderLine** | Line item within a portal order | Each line must map to a valid product and specify quantity and branch destination for multi-branch orders |
| **QuotationRequest** | Customer request for a formal quotation | Captures requested items and quantities; immutable once submitted to the Sales module |
| **QuotationRequestLine** | Line item within a quotation request | Each line must reference a valid product with specified quantity |
| **SupportTicket** | Customer support request raised via the portal | Cannot be closed without a documented resolution reason and timestamp |
| **SupportTicketMessage** | Communication thread within a support ticket | Each message must have an author (customer or support agent) and timestamp |
| **CustomerCatalog** | Customer-specific catalog view | Dynamically filtered from master catalog based on customer profile |
| **ContractPrice** | Pre-negotiated pricing for a specific customer-product combination | Must override standard catalog price when active; has validity period |
| **Cart** | Temporary shopping cart for active session | Auto-expires after 30 minutes of inactivity; converted to PortalOrder on checkout |
| **CartItem** | Item within a shopping cart | Quantity must be positive; product availability checked before checkout |

---

### 2.5 Data Ownership Matrix

The B2B Portal module adheres to strict schema isolation. Cross-module reads occur exclusively via published views or application-service calls.

| **Data Entity** | **Owner Module** | **Access Pattern** | **Purpose** |
|-----------------|------------------|-------------------|-------------|
| `b2b_portal.orders` | B2B Portal | Written by B2B Only. Read by Sales. | Order management and tracking |
| `b2b_portal.users` | B2B Portal | Written by B2B Only. Read by Identity. | Customer user accounts |
| `b2b_portal.quotation_requests` | B2B Portal | Written by B2B Only. Read by Sales. | Quotation request management |
| `b2b_portal.tickets` | B2B Portal | Written by B2B Only. Read by Support. | Customer support ticketing |
| `inventory.items` | Inventory | Read by B2B via `inventory.v_available_products` | Product catalog and availability |
| `inventory.stock_levels` | Inventory | Read by B2B via `inventory.v_available_products` | Real-time availability checks |
| `sales.contract_pricing` | Sales | Read by B2B via `sales.v_contract_pricing` | Contract pricing management |
| `finance.customer_credit` | Finance | Read by B2B via `finance.v_customer_credit` | Credit limit visibility |
| `finance.invoices` | Finance | Read by B2B via `finance.v_customer_invoices` | Invoice and statement access |
| `identity.users` | Identity | Read by B2B via `identity.v_active_users` | User authentication and roles |
| `organization.branches` | Organization | Read by B2B via `org.v_customer_branches` | Branch management and multi-branch ordering |

**Architecture Rule:** The B2B Portal module never reads or writes directly to any other module's PostgreSQL schema. All cross-module data flows through published views or application-service calls. This is enforced by PostgreSQL role grants and `dependency-cruiser` CI gate.

---

### 2.6 State Models

#### 2.6.1 B2B Order State Machine


| **Transition** | **Actor** | **Permission** | **Condition** |
|----------------|-----------|----------------|---------------|
| Draft → Checkout | Customer | `b2b.order.checkout` | Cart contains at least one item; user initiates checkout |
| Checkout → Placed | Customer | `b2b.order.place` | Delivery branches specified; Product Availability Check passes; Credit Limit Check passes |
| Placed → Confirmed | System (auto) | — | Event acknowledged by Sales/Inventory modules; Order Status Tracking updated |
| Confirmed → Shipped | System (auto) | — | Inventory confirms shipment; tracking information available |
| Shipped → Delivered | System (auto) | — | Proof of delivery confirmed |
| Draft/Checkout → Cancelled | Customer | `b2b.order.cancel` | Order not yet placed; cancellation reason provided |
| Placed → On Hold | Support Agent | `b2b.order.hold` | Credit, stock, or payment issue identified |
| On Hold → Placed/Confirmed | Support Agent | `b2b.order.release` | Issue resolved; order processing resumes |

---

#### 2.6.2 Support Ticket State Machine

| **Transition** | **Actor** | **Permission** | **Condition** |
|----------------|-----------|----------------|---------------|
| Draft → Submitted | Customer | `b2b.quotation.submit` | All required fields populated; items and quantities specified |
| Submitted → Under_Review | System (auto) | — | Quotation request routed to Sales module |
| Under_Review → Approved | Sales Module | — | Sales approves and formal quotation generated |
| Under_Review → Rejected | Sales Module | — | Sales rejects with reason provided |

---

### 2.7 Business Rules

| **Rule ID** | **Rule** | **Enforcement** |
|-------------|----------|-----------------|
| **BR-B2B-01** | **Contract Pricing Precedence:** If a customer has an active contract price for an item, it must completely override the standard catalog price | Applied during catalog query; validated before order placement |
| **BR-B2B-02** | **Credit Limit Enforcement:** A PortalOrder cannot transition to Placed if the order total exceeds the available balance shown in Customer Credit Visibility | Synchronous credit check during checkout; order blocked if limit exceeded |
| **BR-B2B-03** | **Multi-Branch Constraints:** During Multi-Branch Ordering, every line item in the cart must be mapped to a valid, pre-registered branch location | Checkout validation; branch mapping required per line item |
| **BR-B2B-04** | **Product Availability:** A PortalOrder cannot be placed if any line item has insufficient available stock | Real-time availability check during checkout; stock reserved on order placement |
| **BR-B2B-05** | **Customer-Specific Catalog:** Customers can only view and order products approved for their customer profile | Catalog query filtered by customer catalog permissions |
| **BR-B2B-06** | **Order Immutability:** Once an order reaches Confirmed state, it cannot be modified by the customer | State guard restricts updates on confirmed orders; changes require support assistance |
| **BR-B2B-07** | **Idempotent Order Submission:** Duplicate order submissions must be prevented using Idempotency-Key headers | Idempotency key validation on all order submissions |
| **BR-B2B-08** | **Support Ticket Resolution:** A support ticket cannot be closed without a documented resolution reason | Validation on ticket close; resolution reason required |

---

### 2.8 Workflow Catalogue

#### 2.8.1 Workflow: Online B2B Ordering (FR-CP-003, 015, 016)

**Purpose:** Enable authenticated customers to browse products, build a cart, and place orders with real-time availability and credit validation.

**Trigger:** Customer logs into the portal and initiates order creation.

**Actor:** Customer (Primary), System (Background)

**Inputs:**
- Product selection with quantities
- Branch delivery mapping (multi-branch orders)
- Customer credit limit data (read from Finance)

**Outputs:**
- Order placed in system
- `b2b.order.placed` event emitted
- Order Status Tracking initialized

**Business Rules:** BR-B2B-01, BR-B2B-02, BR-B2B-03, BR-B2B-04, BR-B2B-07

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Portal User authenticates and accesses dashboard | Customer | Authenticated session |
| 2 | Customer browses Digital Product Catalog with Customer-Specific filters | Customer | Filtered product catalog |
| 3 | Customer adds items to cart; Product Availability Check executed | Customer | Cart with availability status |
| 4 | Customer selects delivery destinations; Multi-Branch Ordering applied | Customer | Branch-mapped line items |
| 5 | Customer reviews and submits order | Customer | Cart → Checkout state |
| 6 | System validates against Customer Credit Visibility limits | System (auto) | Credit check passed/failed |
| 7 | System reserves stock and transitions order to Placed | System (auto) | Order in Placed state |
| 8 | System emits `b2b.order.placed` event to Sales and Inventory | System (auto) | Event delivered |
| 9 | Order Status Tracking initialized; Customer receives confirmation | System (auto) | Order tracking available |

**Exceptions:**

- **Stock insufficient:** Customer notified of unavailable items; order submission blocked
- **Credit limit exceeded:** Order blocked; customer sees available credit and can adjust cart
- **Branch invalid:** Validation fails; customer must correct branch mapping

---

#### 2.8.2 Workflow: Customer Account Access & Management (FR-CP-001, 009)

**Purpose:** Provide secure, authenticated access to the portal with role-based permissions.

**Trigger:** Customer attempts to access portal features.

**Actor:** Customer, Customer Branch Manager

**Inputs:**
- Login credentials
- Customer user role
- Branch association

**Outputs:**
- Authenticated session
- Role-based dashboard view
- Branch-scoped data access

**Business Rules:** BR-B2B-05

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Customer visits portal login page | Customer | Login screen |
| 2 | Customer enters credentials | Customer | Authentication request |
| 3 | System validates credentials against Identity module | System (auto) | Authentication success/failure |
| 4 | System loads customer profile and role permissions | System (auto) | User context loaded |
| 5 | System applies branch scope (if Branch Manager role) | System (auto) | Scoped data access |
| 6 | System renders Account Dashboard | System (auto) | Personalized dashboard |

---

#### 2.8.3 Workflow: Quotation Request (FR-CP-004)

**Purpose:** Enable customers to request formal quotations for complex or bulk orders.

**Trigger:** Customer needs a formal quotation for a planned purchase.

**Actor:** Customer, System

**Inputs:**
- Product selections and quantities
- Required delivery date
- Special requirements (if any)

**Outputs:**
- Quotation Request created
- `b2b.quotation.requested` event emitted
- Customer receives confirmation of request

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Customer accesses Quotation Request form | Customer | Request form |
| 2 | Customer selects products, specifies quantities, and enters requirements | Customer | Completed request |
| 3 | Customer submits quotation request | Customer | Request in Draft state |
| 4 | System validates request and transitions to Submitted | System (auto) | Request Submitted |
| 5 | System emits `b2b.quotation.requested` event to Sales module | System (auto) | Event delivered |
| 6 | Customer receives confirmation and tracking reference | System (auto) | Confirmation notification |

---

#### 2.8.4 Workflow: Customer Support Ticketing (FR-CP-008)

**Purpose:** Allow customers to raise, update, and track service requests directly within the portal.

**Trigger:** Customer encounters an issue requiring support assistance.

**Actor:** Customer, Support Agent (Internal)

**Inputs:**
- Issue description
- Order reference (if applicable)
- Priority level
- Attachments (if any)

**Outputs:**
- Support Ticket created
- Customer receives ticket reference
- Support agent assigned

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Customer accesses Support section | Customer | Support dashboard |
| 2 | Customer creates new support ticket | Customer | Ticket in New state |
| 3 | Customer provides issue details, order reference, and attachments | Customer | Completed ticket |
| 4 | System assigns ticket to appropriate support queue | System (auto) | Ticket routed |
| 5 | Support agent reviews and assigns ticket | Support Agent | Ticket In_Progress |
| 6 | Support agent resolves issue and adds resolution notes | Support Agent | Ticket Resolved |
| 7 | Customer reviews resolution; confirms or reopens | Customer | Ticket Closed or Reopened |

---

#### 2.8.5 Workflow: Repeat Order (FR-CP-013)

**Purpose:** Enable customers to quickly reorder from their order history.

**Trigger:** Customer needs to place a reorder for items previously purchased.

**Actor:** Customer

**Inputs:**
- Historical order reference
- Quantity adjustments (optional)
- Updated delivery dates

**Outputs:**
- New cart created with historical items
- Checkout-ready cart

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Customer accesses Order History | Customer | Historical orders list |
| 2 | Customer selects a previous order to repeat | Customer | Order details displayed |
| 3 | Customer reviews and adjusts quantities if needed | Customer | Updated line items |
| 4 | Customer confirms repeat order creation | Customer | New cart created |
| 5 | Customer proceeds to checkout | Customer | Standard checkout flow |

---

#### 2.8.6 Workflow: Financial Visibility (FR-CP-006, 014)

**Purpose:** Provide customers with visibility into invoices, statements, and credit status.

**Trigger:** Customer views Financial section of dashboard.

**Actor:** Customer (Read-only)

**Inputs:**
- Customer authentication
- Account selection

**Outputs:**
- Invoice list and download
- Statement of account
- Credit limit and available balance

**Business Rules:** BR-B2B-02

**Process Steps:**

| **Step** | **Action** | **Actor** | **Output** |
|----------|------------|-----------|------------|
| 1 | Customer navigates to Financial section | Customer | Financial dashboard |
| 2 | System queries Finance module for invoice data | System (auto) | Invoice list |
| 3 | System queries Finance module for credit data | System (auto) | Credit visibility |
| 4 | Customer views invoices, statements, and credit status | Customer | Financial information displayed |
| 5 | Customer downloads invoice or statement if needed | Customer | PDF download |

## 3. Architecture & Integration

### 3.1 Integration Requirements

The B2B Portal module reads external data exclusively through published database views or controlled application-service calls. All outbound integrations use NestJS EventEmitter2 for Release 1, migrating to a message broker (BullMQ/Redis) for Release 2.

#### 3.1.1 Outbound Events (B2B Portal → Other Modules)

| **Event** | **Trigger** | **Consumer Module** | **Payload** |
|-----------|-------------|---------------------|-------------|
| `b2b.order.placed` | Order successfully placed | Sales, Inventory | `{ order_id, customer_id, branch_id, lines: [{product_id, quantity, price}], total, order_date }` |
| `b2b.quotation.requested` | Quotation request submitted | Sales | `{ quote_req_id, customer_id, items: [{product_id, quantity}], required_by_date, special_requirements }` |
| `b2b.ticket.created` | Support ticket raised | Customer Service | `{ ticket_id, customer_id, issue_type, description, priority, order_reference? }` |

#### 3.1.2 Inbound Data Consumed (Other Modules → B2B Portal)

| **Source Module** | **Data Consumed** | **Access Method** | **Purpose** |
|-------------------|-------------------|-------------------|-------------|
| **Inventory** | Product catalog, stock levels | `inventory.v_available_products` | Digital Product Catalog & Availability Check |
| **Sales** | Contract pricing | `sales.v_contract_pricing` | Contract Pricing Management |
| **Finance** | Invoices, statements, credit limits | `finance.v_customer_financials` | Invoice Access & Credit Visibility |
| **Identity** | User roster, active users | `identity.v_active_users` | Secure Portal Login & User Management |
| **Organization** | Branches, customer structure | `org.v_customer_branches` | Branch Management & Multi-Branch Ordering |

#### 3.1.3 Integration Architecture Rules

- The B2B Portal module never reads or writes directly to another module's PostgreSQL schema
- All inbound data is accessed via published views exposed by the owning module
- All outbound integration uses NestJS EventEmitter2 (Release 1) with a plan to migrate to BullMQ in Release 2
- Future MCP tool integrations will invoke `B2BPortalService` application-service methods — never direct database access

---

### 3.2 Module Dependencies
b2b_portal ── reads from ──> identity (users, roles)
│
├── reads from ──> organization (branches)
│
├── reads from ──> inventory (product catalog & availability)
│
├── reads from ──> sales (contract pricing)
│
└── reads from ──> finance (credit limits & invoices)

inventory ── consumes ──> b2b.order.placed (stock reservation)
sales ── consumes ──> b2b.order.placed (order processing)
sales ── consumes ──> b2b.quotation.requested (quotation handling)
customer_service ── consumes ──> b2b.ticket.created (support routing)


| **Dependency** | **Direction** | **Type** | **Contract** |
|----------------|---------------|----------|--------------|
| Identity module | Inbound | Read (published view) | `identity.v_active_users` |
| Organization module | Inbound | Read (published view) | `org.v_customer_branches` |
| Inventory module | Inbound | Read (published view) | `inventory.v_available_products` |
| Sales module | Inbound | Read (published view) | `sales.v_contract_pricing` |
| Finance module | Inbound | Read (published view) | `finance.v_customer_financials` |
| Inventory module | Outbound | Event consumer | `b2b.order.placed` → stock reservation |
| Sales module | Outbound | Event consumer | `b2b.order.placed` → order processing |
| Sales module | Outbound | Event consumer | `b2b.quotation.requested` → quotation handling |
| Customer Service module | Outbound | Event consumer | `b2b.ticket.created` → support routing |

**Circular Dependency Check:** No circular dependency exists. B2B Portal consumes from Identity, Organization, Inventory, Sales, and Finance (read-only). Inventory, Sales, and Customer Service consume B2B Portal events — event-based, non-circular.

---

### 3.3 Architecture Alignment

This module fully complies with the **Foundation Architecture and Planning Framework v2.1**. The following table maps each mandatory architecture rule to its enforcement in the B2B Portal module.

| **Architecture Rule (Framework §5)** | **B2B Portal Module Implementation** |
|--------------------------------------|--------------------------------------|
| **File-size limits** (domain/app ≤ 450 lines; controllers ≤ 250; tests ≤ 600) | Enforced by CI `check:file-size` ESLint rule; no exemptions currently required |
| **Dependency direction:** Interface → Application → Domain | NestJS module structure enforces this; `b2bController` injects `b2bPortalService` (application), never domain providers directly |
| **Module must not read/write another module's tables** | PostgreSQL role grants on `b2b_portal.*` schema only; `dependency-cruiser` CI rule forbids cross-schema imports |
| **Reusable modules must not import company-specific code** | `dependency-cruiser` rule: `modules/` → `customer-config/` forbidden |
| **Frontend components must not contain authorization logic** | Every Next.js Server Action calls `requirePermission()` server-side; missing call fails CI |
| **Controllers must not implement business workflows directly** | `b2bController` calls application service only; custom ESLint rule enforces this |
| **Named module owner and backup** | Recorded in `CODEOWNERS`; GitHub requires their review to merge |
| **Per-module AGENTS.md committed before any agent-ready issue is opened** | `modules/b2b-portal/AGENTS.md` committed at Day 1 of implementation sprint |
| **Audit log on every state transition** | `b2b_portal.audit_log` table; every state transition writes append-only row: `actor_id`, `actor_type`, `correlation_id`, `previous_state`, `new_state` |
| **Idempotency on critical commands** | `B2BPortalService.submitOrder()` requires `Idempotency-Key` header; repeated key returns original result |
| **Background jobs via BullMQ** | Quotation PDF generation, order confirmation emails, support ticket notifications — all via BullMQ queues |
| **Observability: OpenTelemetry, correlation ID** | `b2b_order_placed_total` metric; correlation ID propagates from HTTP request → worker job → audit row |

---

### 3.4 Repository Structure

Following the established monorepo modular monolith layout:
hemin-erp/
├── modules/
│ └── b2b-portal/
│ ├── CHARTER.md & AGENTS.md
│ ├── domain/
│ │ ├── order.entity.ts
│ │ ├── quotation-request.entity.ts
│ │ ├── support-ticket.entity.ts
│ │ └── user.entity.ts
│ ├── application/
│ │ ├── catalog.service.ts
│ │ ├── order.service.ts
│ │ ├── quotation.service.ts
│ │ ├── support.service.ts
│ │ ├── customer.service.ts
│ │ └── dashboard.service.ts
│ ├── infrastructure/
│ │ ├── order.repository.ts
│ │ ├── quotation.repository.ts
│ │ ├── support.repository.ts
│ │ └── user.repository.ts
│ ├── interfaces/
│ │ ├── catalog.controller.ts
│ │ ├── order.controller.ts
│ │ ├── quotation.controller.ts
│ │ ├── support.controller.ts
│ │ ├── customer.controller.ts
│ │ └── dashboard.controller.ts
│ ├── events/
│ │ ├── order-placed.event.ts
│ │ ├── quotation-requested.event.ts
│ │ └── ticket-created.event.ts
│ └── authorization/
│ └── b2b.permissions.ts
├── packages/
│ ├── contracts/
│ │ └── b2b-portal/
│ │ ├── create-order.dto.ts
│ │ ├── create-quotation-request.dto.ts
│ │ ├── create-support-ticket.dto.ts
│ │ ├── order-placed.event.schema.ts
│ │ └── quotation-requested.event.schema.ts
│ └── database/
│ └── schema/
│ └── b2b/ # Prisma schema files for b2b_portal.*
│ ├── schema.prisma
│ └── migrations/
└── apps/
└── web/
└── app/
└── (b2b-portal)/
├── dashboard/
├── catalog/
├── orders/
├── quotations/
├── support/
├── financial/
└── profile


---

### 3.5 API Boundaries (REST Endpoints)

All endpoints are prefixed with `/api/b2b`. All requests pass through the NestJS `PermissionsGuard`.

#### 3.5.1 Catalog Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `GET` | `/catalogs` | Fetch digital product catalog with customer-specific filters | `b2b.catalog.read` |
| `GET` | `/catalogs/:id` | Get product detail with pricing | `b2b.catalog.read` |
| `GET` | `/catalogs/:id/availability` | Check real-time stock availability | `b2b.catalog.read` |
| `GET` | `/catalogs/search` | Search products by keyword/category | `b2b.catalog.read` |

#### 3.5.2 Order Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `POST` | `/orders` | Submit a new online order | `b2b.order.create` |
| `GET` | `/orders` | List customer orders | `b2b.order.read` |
| `GET` | `/orders/:id` | Get order detail with tracking | `b2b.order.read` |
| `POST` | `/orders/:id/cancel` | Cancel order (draft state only) | `b2b.order.cancel` |
| `POST` | `/orders/:id/repeat` | Create repeat order from historical order | `b2b.order.create` |
| `GET` | `/orders/:id/tracking` | Get real-time order tracking status | `b2b.order.read` |

#### 3.5.3 Quotation Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `POST` | `/quotations` | Submit a quotation request | `b2b.quotation.create` |
| `GET` | `/quotations` | List customer quotation requests | `b2b.quotation.read` |
| `GET` | `/quotations/:id` | Get quotation request detail | `b2b.quotation.read` |
| `PUT` | `/quotations/:id` | Update quotation request (draft only) | `b2b.quotation.update` |

#### 3.5.4 Financial Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `GET` | `/finance/invoices` | List customer invoices | `b2b.finance.read` |
| `GET` | `/finance/invoices/:id/download` | Download invoice PDF | `b2b.finance.read` |
| `GET` | `/finance/statements` | Get account statements | `b2b.finance.read` |
| `GET` | `/finance/credit` | Get credit limit and available balance | `b2b.finance.read` |
| `POST` | `/finance/payment` | Initiate digital payment | `b2b.finance.payment` |

#### 3.5.5 Support Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `POST` | `/tickets` | Raise a customer support request | `b2b.ticket.create` |
| `GET` | `/tickets` | List customer support tickets | `b2b.ticket.read` |
| `GET` | `/tickets/:id` | Get ticket detail with messages | `b2b.ticket.read` |
| `POST` | `/tickets/:id/messages` | Add message to ticket | `b2b.ticket.update` |
| `POST` | `/tickets/:id/close` | Close resolved ticket | `b2b.ticket.close` |

#### 3.5.6 Customer Management Endpoints

| **Method** | **Endpoint** | **Description** | **Permission Required** |
|------------|--------------|-----------------|------------------------|
| `GET` | `/profile` | Get customer profile | `b2b.profile.read` |
| `PUT` | `/profile` | Update customer profile | `b2b.profile.update` |
| `GET` | `/branches` | List customer branches | `b2b.profile.read` |
| `GET` | `/dashboard` | Get account dashboard data | `b2b.dashboard.view` |

---

### 3.6 Security Considerations & RBAC

#### 3.6.1 Permission Catalogue

All permissions follow the pattern `b2b.<entity>.<action>`. Roles are composed from these atomic permissions.

| **Permission** | **Scope** | **Granted To** |
|----------------|-----------|----------------|
| `b2b.catalog.read` | Customer | All authenticated customers |
| `b2b.order.create` | Customer | All authenticated customers |
| `b2b.order.read` | Customer (own orders) | All authenticated customers |
| `b2b.order.cancel` | Customer (own orders, draft only) | All authenticated customers |
| `b2b.quotation.create` | Customer | All authenticated customers |
| `b2b.quotation.read` | Customer (own requests) | All authenticated customers |
| `b2b.quotation.update` | Customer (own requests, draft only) | All authenticated customers |
| `b2b.finance.read` | Customer | All authenticated customers |
| `b2b.finance.payment` | Customer | All authenticated customers |
| `b2b.ticket.create` | Customer | All authenticated customers |
| `b2b.ticket.read` | Customer (own tickets) | All authenticated customers |
| `b2b.ticket.update` | Customer (own tickets) | All authenticated customers |
| `b2b.ticket.close` | Customer (own tickets) | All authenticated customers |
| `b2b.profile.read` | Customer | All authenticated customers |
| `b2b.profile.update` | Customer | All authenticated customers |
| `b2b.dashboard.view` | Customer | All authenticated customers |
| `b2b.branch.order` | Branch Manager (assigned branch only) | Customer Branch Managers |
| `b2b.branch.view` | Branch Manager (assigned branch only) | Customer Branch Managers |
| `b2b.admin.override` | Company | Internal Administrators |

#### 3.6.2 Roles

| **Role** | **Permissions Composite** | **Description** |
|----------|--------------------------|-----------------|
| **customer_user** | All `b2b.*` permissions (except `b2b.branch.*` and `b2b.admin.*`) | Standard customer user with full portal access |
| **branch_manager** | All `customer_user` permissions + `b2b.branch.order` + `b2b.branch.view` | Branch-scoped manager with ordering and tracking for assigned branch only |
| **procurement_officer** | All `customer_user` permissions (read/write) | Customer procurement specialist with full ordering capabilities |
| **internal_admin** | All `b2b.*` permissions + `b2b.admin.override` | Internal administration for support and escalation |

#### 3.6.3 Separation of Duties

| **Workflow Step** | **Creator** | **Approver** | **Rule** |
|-------------------|-------------|--------------|----------|
| Order Submission | Customer | System (auto) | Automated validation; no manual approval |
| Quotation Request | Customer | Sales Team | Sales handles quotation externally |
| Support Ticket | Customer | Support Agent | Support agent assigns and resolves |

#### 3.6.4 Tenant & Data Isolation

- **Customer Isolation:** A logged-in portal user can only view data associated with their specific `customer_id`
- **Branch Restrictions:** Users with the `branch_manager` role are restricted strictly to their assigned branch ID for ordering and tracking
- **Data Scope Enforcement:** Every query is scoped by `customer_id` and/or `branch_id`; enforced server-side in `requirePermission()` calls

#### 3.6.5 Audit Requirements

Every state transition in the B2B Portal module writes an append-only audit row to `b2b_portal.audit_log`:

```sql
CREATE TABLE b2b_portal.audit_log (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    entity_type VARCHAR(50) NOT NULL,  -- 'order', 'quotation', 'ticket'
    entity_id UUID NOT NULL,
    actor_id UUID REFERENCES identity.users(id),
    actor_type VARCHAR(20) NOT NULL,   -- 'customer', 'integration', 'agent'
    correlation_id UUID NOT NULL,
    previous_state VARCHAR(50),
    new_state VARCHAR(50) NOT NULL,
    changed_at TIMESTAMP DEFAULT now(),
    notes TEXT
);

CREATE INDEX idx_audit_log_entity ON b2b_portal.audit_log(entity_type, entity_id);
CREATE INDEX idx_audit_log_correlation ON b2b_portal.audit_log(correlation_id);
```
3.6.6 Authentication & Session Management
Session Auth: Argon2-based session authentication for human customers

Service Accounts: Signed service-account tokens for integrations and future AI agents

Session Timeout: 30 minutes of inactivity; auto-expires for security

Multi-Factor Authentication: Planned for Release 2 (enhancement)

3.7 Published Events (Detailed Schemas)
3.7.1 b2b.order.placed

interface OrderPlacedEvent {
    order_id: string;
    customer_id: string;
    customer_name: string;
    branch_id: string;
    lines: Array<{
        product_id: string;
        product_name: string;
        quantity: number;
        unit_price: number;
        total_price: number;
        branch_destination?: string;
    }>;
    total_amount: number;
    currency: string;  // 'USD' or 'ETB'
    order_date: string;  // ISO 8601
    payment_method: string;
    special_instructions?: string;
}

3.7.2 b2b.quotation.requested
interface QuotationRequestedEvent {
    quote_req_id: string;
    customer_id: string;
    customer_name: string;
    items: Array<{
        product_id: string;
        product_name: string;
        quantity: number;
        unit_of_measure: string;
    }>;
    required_by_date: string;  // ISO 8601
    delivery_location: string;
    special_requirements?: string;
    created_at: string;  // ISO 8601
}

3.7.3 b2b.ticket.created
interface TicketCreatedEvent {
    ticket_id: string;
    customer_id: string;
    customer_name: string;
    issue_type: string;  // 'order_issue', 'product_issue', 'billing', 'technical', 'other'
    priority: string;    // 'low', 'medium', 'high', 'critical'
    subject: string;
    description: string;
    order_reference?: string;
    attachments?: string[];
    created_at: string;  // ISO 8601
}

3.8 Background Jobs (BullMQ)

Queue Name	Job	Trigger	Description
b2b.order-notifications	SendOrderConfirmation	On order placement	Email notification with order summary; 3 retries with backoff
b2b.order-notifications	SendOrderTrackingUpdate	On order status change	Email notification with updated tracking info
b2b.quotation-pdf	GenerateQuotationPdf	On quotation submission	Async PDF generation via BullMQ; 3 retries with backoff
b2b.ticket-notifications	SendTicketConfirmation	On ticket creation	Email notification with ticket reference
b2b.ticket-notifications	SendTicketEscalation	On ticket priority escalation	Alert to support team
b2b.cleanup	CleanupExpiredCarts	Hourly cron	Remove carts older than 30 minutes
b2b.dashboard-update	RefreshDashboardCache	Every 5 minutes	Update cached dashboard data


## 4. Project Management & Execution

### 4.1 Work Breakdown Structure (WBS)

#### Initiative: B2B Commerce Portal Module

---

**WBS-1: Week 1 — Planning, Architecture & Design**

| **ID** | **Task** | **Owner** | **Duration** |
|--------|----------|-----------|--------------|
| WBS-1.1 | Business Process Validation for all 16 FRs | Senior Developer | 1 day |
| WBS-1.2 | Capability Mapping & Entity Identification | Senior Developer | 1 day |
| WBS-1.3 | State Machine Design (Order, Quotation, Ticket) | Senior Developer | 1 day |
| WBS-1.4 | Entity-Relationship Diagram (ERD) | Backend Developer | 1 day |
| WBS-1.5 | Permission Catalogue & Separation-of-Duties Matrix | Senior Developer | 1 day |
| WBS-1.6 | API Contract Drafts (DTOs, event schemas) | Backend Developer | 1 day |
| WBS-1.7 | Repository Structure & Module Charter | Senior Developer | 0.5 day |
| WBS-1.8 | UI Wireframes (Dashboard, Catalog, Ordering, Support) | Frontend Developer | 2 days |
| WBS-1.9 | Architecture Review & Stakeholder Walkthrough | Senior Developer | 0.5 day |
| WBS-1.10 | Week 1 Deliverables Gate (M1) | All | 0.5 day |

---

**WBS-2: Week 2 — Backend Development (NestJS)**

| **ID** | **Task** | **Owner** | **Duration** |
|--------|----------|-----------|--------------|
| WBS-2.1 | PostgreSQL Schema & Prisma Migrations | Backend Developer | 1 day |
| WBS-2.2 | NestJS Module Scaffolding (domain/application/infrastructure/interfaces/events/authorization) | Backend Developer | 0.5 day |
| WBS-2.3 | Domain Entities & Repository Interfaces | Backend Developer | 1 day |
| WBS-2.4 | CatalogService (product catalog, availability, customer-specific catalogs) | Backend Developer | 1 day |
| WBS-2.5 | OrderService (cart, checkout, order placement, multi-branch, repeat orders) | Backend Developer | 2 days |
| WBS-2.6 | QuotationService (create, submit, status tracking) | Backend Developer | 1 day |
| WBS-2.7 | SupportService (ticket creation, messaging, status management) | Backend Developer | 1 day |
| WBS-2.8 | CustomerService (profile, branches, user management) | Backend Developer | 1 day |
| WBS-2.9 | DashboardService (aggregated customer data) | Backend Developer | 1 day |
| WBS-2.10 | Permission Guards & RBAC Implementation | Backend Developer | 1 day |
| WBS-2.11 | Audit Logging Middleware | Backend Developer | 0.5 day |
| WBS-2.12 | BullMQ Job Definitions (notifications, PDF, cleanup, cache) | Backend Developer | 1 day |
| WBS-2.13 | Event Publishers (order.placed, quotation.requested, ticket.created) | Backend Developer | 0.5 day |
| WBS-2.14 | Week 2 Deliverables Gate (M2) | All | 0.5 day |

---

**WBS-3: Week 3 — Frontend Development (Next.js)**

| **ID** | **Task** | **Owner** | **Duration** |
|--------|----------|-----------|--------------|
| WBS-3.1 | Next.js App Router Scaffolding (b2b-portal section) | Frontend Developer | 0.5 day |
| WBS-3.2 | Authentication Flow (login, session management, protected routes) | Frontend Developer | 1 day |
| WBS-3.3 | Customer Account Dashboard Page | Frontend Developer | 1 day |
| WBS-3.4 | Product Catalog Pages (list, detail, search, filters) | Frontend Developer | 1.5 days |
| WBS-3.5 | Shopping Cart & Checkout Pages | Frontend Developer | 1.5 days |
| WBS-3.6 | Multi-Branch Ordering UI (branch selection per line item) | Frontend Developer | 1 day |
| WBS-3.7 | Order History & Tracking Pages | Frontend Developer | 1 day |
| WBS-3.8 | Quotation Request Pages (create, list, detail) | Frontend Developer | 1 day |
| WBS-3.9 | Financial Pages (invoices, statements, credit visibility) | Frontend Developer | 1 day |
| WBS-3.10 | Support Pages (ticket creation, list, detail, messaging) | Frontend Developer | 1 day |
| WBS-3.11 | Customer Profile & Branch Management Pages | Frontend Developer | 0.5 day |
| WBS-3.12 | API Integration (React Query, error handling, loading states) | Frontend Developer | 2 days |
| WBS-3.13 | Responsive UI Polish & Micro-interactions | Frontend Developer | 1 day |
| WBS-3.14 | Week 3 Deliverables Gate (M3) | All | 0.5 day |

---

**WBS-4: Week 4 — Testing, Integration & Deployment**

| **ID** | **Task** | **Owner** | **Duration** |
|--------|----------|-----------|--------------|
| WBS-4.1 | Unit Tests - Domain Logic & State Machines | QA + Backend | 1 day |
| WBS-4.2 | Integration Tests - API Endpoints (supertest + Testcontainers) | QA + Backend | 1 day |
| WBS-4.3 | Authorization Tests - Permission & Scope Enforcement | QA | 0.5 day |
| WBS-4.4 | Idempotency Tests - Order Submission | QA | 0.5 day |
| WBS-4.5 | End-to-End Tests - Critical Flows | QA | 1 day |
| WBS-4.6 | Performance Tests - Dashboard p95 < 500ms | QA | 0.5 day |
| WBS-4.7 | Bug Fixes & Code Review | All | 1 day |
| WBS-4.8 | CI/CD Pipeline Setup (GitHub Actions) | DevOps | 1 day |
| WBS-4.9 | Database Seed Data & Migration Validation | DevOps | 0.5 day |
| WBS-4.10 | Cross-Module Integration Test (Order → Inventory event) | Backend + DevOps | 0.5 day |
| WBS-4.11 | Documentation - AGENTS.md, CHARTER.md, API docs | Senior Developer | 0.5 day |
| WBS-4.12 | Stakeholder UAT & Demo | Senior Developer | 0.5 day |
| WBS-4.13 | MVP Sign-Off (M4) | All | 0.5 day |

---

### 4.2 Resource Allocation

| **Role** | **Responsibility** | **Allocation** |
|----------|-------------------|----------------|
| **Senior Developer (Module Owner)** | Architecture review, state machine design, PR reviews (order submission, authorization logic, event contracts), Day-5 planning gate | 100% — Weeks 1–4 |
| **Backend Developer 1** | Catalog, Order, Quotation services; database schema; Prisma migrations; permission guards | 100% — Weeks 2–4 |
| **Backend Developer 2** | Support, Customer, Dashboard services; BullMQ jobs; event publishers; audit logging | 100% — Weeks 2–4 |
| **Frontend Developer 1** | Dashboard, Catalog, Cart, Checkout, Multi-Branch Ordering | 100% — Weeks 1 (wireframes), 3–4 |
| **Frontend Developer 2** | Orders, Quotations, Financial, Support, Profile pages | 100% — Weeks 1 (wireframes), 3–4 |
| **QA Engineer** | Test strategy, unit/integration/E2E/performance testing, UAT coordination | 100% — Week 1 (test planning), 4 (execution) |
| **DevOps (shared)** | CI/CD pipeline, database migrations, staging deployment | 50% — Weeks 1, 4 |

**Team Size Assumption:** 2 backend developers, 2 frontend developers, 1 QA, 1 senior developer/owner, shared DevOps. If the team is smaller, extend Weeks 2–3 by treating backend and frontend tasks as sequential rather than overlapping.

---

### 4.3 Four-Week Development Timeline

#### Phase Summary

| **Phase** | **Dates** | **Owner(s)** | **Key Deliverable** |
|-----------|-----------|--------------|---------------------|
| **Week 1 - Planning & Architecture** | [TBD] | Senior + Backend + Frontend leads | ERD, state machines, permission catalogue, wireframes, architecture review, stakeholder gate (M1) |
| **Week 2 - Backend Development** | [TBD] | Backend x2 / Senior review | All NestJS services, Prisma migrations, permission guards, BullMQ jobs, event publishers (M2) |
| **Week 3 - Frontend Development** | [TBD] | Frontend x2 / API integration | All Next.js pages, Customer Dashboard live, responsive UI, API integration complete (M3) |
| **Week 4 - Testing, Integration & Deployment** | [TBD] | QA + DevOps / All teams | Full test suite, CI/CD pipeline, cross-module integration, UAT sign-off, MVP complete (M4) |

---

#### Week-by-Week Overview

| **Week** | **Dates** | **Focus** | **Milestone** |
|----------|-----------|-----------|---------------|
| **Week 1** | [TBD] | FR analysis, capability mapping, state machines, permission catalogue, API contracts, wireframes, architecture gate | M1 |
| **Week 2** | [TBD] | PostgreSQL/Prisma schema, all NestJS services, permission guards, audit logging, BullMQ jobs, event publishers, REST controllers | M2 |
| **Week 3** | [TBD] | All Next.js pages, Customer Dashboard, API integration, responsive UI, multi-branch ordering UI | M3 |
| **Week 4** | [TBD] | Unit/integration/auth/idempotency/E2E/performance tests, cross-module integration, CI/CD, bug fixes, UAT, MVP sign-off | M4 |

---

### 4.4 Milestones

| **Milestone** | **Target Date** | **Definition of Done** |
|---------------|-----------------|------------------------|
| **M1 — Planning Complete** | End of Week 1 | ERD finalized; state machines documented; permission catalogue approved; wireframes completed; architecture gate passed; stakeholder walkthrough complete |
| **M2 — Backend Complete** | End of Week 2 | All NestJS services implemented; Prisma migrations applied to staging DB; permission guards active; BullMQ jobs defined; events publishable; local smoke tests passing |
| **M3 — Frontend Complete** | End of Week 3 | All Next.js pages functional; API integration complete; Customer Dashboard live; responsive UI validated; no critical blockers |
| **M4 — MVP Sign-Off** | End of Week 4 | Full test suite passing (unit, integration, authorization, idempotency, E2E, performance); CI/CD pipeline green; cross-module integration validated; stakeholder UAT approved; documentation complete |

---

### 4.5 Deliverables

| **#** | **Deliverable** | **Owner** | **Due** |
|-------|-----------------|-----------|---------|
| D-01 | Module Charter & AGENTS.md | Senior Developer | Week 1 |
| D-02 | Business Process Validation Report | Senior Developer | Week 1 |
| D-03 | Entity-Relationship Diagram (ERD) | Backend Developer 1 | Week 1 |
| D-04 | Permission Catalogue & Separation-of-Duties Matrix | Senior Developer | Week 1 |
| D-05 | State Machine Specifications (all 3 state machines) | Senior Developer | Week 1 |
| D-06 | API Contract Drafts (DTOs, event schemas) | Backend Developer 2 | Week 1 |
| D-07 | UI Wireframes (all page areas) | Frontend Developers | Week 1 |
| D-08 | PostgreSQL Prisma Schema (b2b_portal.*) | Backend Developer 1 | Week 2 |
| D-09 | Prisma Migration Scripts + Seed Data | Backend Developer 1 | Week 2 |
| D-10 | NestJS Application Services (all 6 services) | Backend Developers | Week 2 |
| D-11 | REST Controllers with DTO Validation | Backend Developers | Week 2 |
| D-12 | Permission Guards & RBAC Implementation | Backend Developer 1 | Week 2 |
| D-13 | BullMQ Job Workers (7 job types) | Backend Developer 2 | Week 2 |
| D-14 | Event Publishers (3 event types) | Backend Developer 1 | Week 2 |
| D-15 | Audit Logging Middleware | Backend Developer 2 | Week 2 |
| D-16 | Next.js Frontend — Authentication Flow | Frontend Developer 1 | Week 3 |
| D-17 | Next.js Frontend — Customer Dashboard | Frontend Developer 1 | Week 3 |
| D-18 | Next.js Frontend — Product Catalog | Frontend Developer 1 | Week 3 |
| D-19 | Next.js Frontend — Shopping Cart & Checkout | Frontend Developer 1 | Week 3 |
| D-20 | Next.js Frontend — Multi-Branch Ordering | Frontend Developer 1 | Week 3 |
| D-21 | Next.js Frontend — Order History & Tracking | Frontend Developer 2 | Week 3 |
| D-22 | Next.js Frontend — Quotation Pages | Frontend Developer 2 | Week 3 |
| D-23 | Next.js Frontend — Financial Pages | Frontend Developer 2 | Week 3 |
| D-24 | Next.js Frontend — Support Pages | Frontend Developer 2 | Week 3 |
| D-25 | Next.js Frontend — Profile Pages | Frontend Developer 2 | Week 3 |
| D-26 | Unit Test Suite | QA + Backend | Week 4 |
| D-27 | Integration Test Suite | QA + Backend | Week 4 |
| D-28 | Authorization Test Suite | QA | Week 4 |
| D-29 | E2E Test Suite (critical flows) | QA | Week 4 |
| D-30 | Performance Test Results (p95 benchmarks) | QA | Week 4 |
| D-31 | CI/CD GitHub Actions Pipeline | DevOps | Week 4 |
| D-32 | Cross-Module Integration Validation Report | Backend + DevOps | Week 4 |
| D-33 | UAT Sign-Off Record | Senior Developer + Stakeholders | Week 4 |
| D-34 | Final Implementation Documentation | Senior Developer | Week 4 |


## 5. Quality, Risk & Readiness

### 5.1 Risks & Risk Mitigation

| **Risk ID** | **Risk** | **Probability** | **Impact** | **Severity** | **Mitigation Strategy** | **Owner** | **Contingency** |
|-------------|----------|-----------------|------------|--------------|------------------------|-----------|-----------------|
| **R-B2B-01** | Real-time catalog/stock availability checks slow down the portal UI | Medium | High | High | Utilize efficient caching mechanisms and materialized views for `inventory.v_available_products` | Backend Developer 1 | Implement Redis cache with 5-minute TTL; fallback to stale data with warning |
| **R-B2B-02** | Customer places an order that exceeds their credit limit due to stale data | Medium | High | High | Enforce a strict, synchronous credit check immediately before placing the order | Backend Developer 1 | Sync credit check with Finance module; block order if limit exceeded |
| **R-B2B-03** | `b2b.order.placed` event fails to reach Sales or Inventory modules | Low | High | High | Implement BullMQ dead-letter queues and automated retry mechanisms with exponential backoff | Backend Developer 2 | Manual requeue via admin endpoint; monitoring alert on dead-letter queue |
| **R-B2B-04** | Concurrent order submissions on the same product cause stock overselling | Medium | High | High | Optimistic row versioning with stock reservation during checkout; CONFLICT error on oversell | Backend Developer 1 | Reserve stock at checkout; release if order not confirmed within 15 minutes |
| **R-B2B-05** | Customer-specific catalog/pricing data is incorrect or stale | Medium | Medium | Medium | Synchronize with Sales module contract pricing views; cache with TTL | Backend Developer 1 | Manual refresh endpoint for administrators |
| **R-B2B-06** | Multi-branch order line items mapped to invalid or closed branches | Medium | Medium | Medium | Validate all branch mappings against Organization module before order submission | Backend Developer 1 | Clear error messaging with invalid branch identification |
| **R-B2B-07** | Poor customer adoption of self-service portal | Medium | High | High | Design intuitive UI; conduct UAT with pilot customers; provide training materials | Frontend Developer + Senior Developer | Onboarding support; feedback collection; iterative improvements |
| **R-B2B-08** | Payment gateway integration fails during high-volume periods | Low | Medium | Medium | Implement payment gateway with retry logic; fallback to manual payment processing | Backend Developer 2 | Manual payment processing workflow; customer notification of alternative |
| **R-B2B-09** | Database schema migration failure during deployment | Low | High | High | Each Prisma migration includes a down-migration; dry-run on staging snapshot | DevOps | Rollback procedure documented; backup taken before deployment window |
| **R-B2B-10** | Permission boundary violation — customer accessing another customer's data | Low | High | High | Customer-scoped permissions enforced server-side in `requirePermission()` calls; CI contract test checks this | Backend Developer 1 | Authorization tests specifically cover this scenario |
| **R-B2B-11** | Frontend UI performance degradation on large product catalog (>10,000 items) | Medium | Medium | Medium | Cursor-based pagination; React Query infinite scroll; cached product data | Frontend Developer | Static cached catalog snapshot with 15-minute refresh |
| **R-B2B-12** | Stakeholder availability for UAT in final week | Medium | High | High | Schedule UAT walkthrough before final bug-fix day; prepare demo environment with realistic seed data | Senior Developer | Async UAT via screen recording and written sign-off if stakeholder unavailable in person |

---

### 5.2 Quality Assurance

#### 5.2.1 QA Standards

All quality assurance activities for the B2B Portal module comply with the **Foundation Architecture and Planning Framework v2.1 Definition of Done**:

| **DoD Criterion** | **B2B Portal Module Implementation** |
|-------------------|--------------------------------------|
| Implementation satisfies acceptance criteria | Each issue has testable acceptance criteria; CI enforces them |
| Required tests pass | Unit, integration, authorization, idempotency, E2E — all required before merge |
| Authorization and scope enforced server-side | `requirePermission()` in every NestJS Server Action and Route Handler; CI contract test checks this |
| Audit events implemented | Every state transition writes to `b2b_portal.audit_log`; integration test asserts audit row created |
| Migration is reversible | Every Prisma migration includes a down-migration or documented exception |
| No architecture gate is bypassed | `dependency-cruiser` CI gate green; file-size limits respected |
| Code review complete; feature works in clean environment | PR requires Senior Developer approval; CI runs on clean container |

#### 5.2.2 Code Quality Gates (CI)

| **Gate** | **Tool** | **Block Condition** |
|----------|----------|---------------------|
| Formatting | Prettier | Any formatting violation |
| Static analysis | ESLint (custom rules) | File-size limit exceeded; controller injects domain provider directly |
| Type checking | `tsc --noEmit` | Any TypeScript type error |
| Dependency boundaries | `dependency-cruiser` | Cross-module schema imports; customer-config imports |
| Database grant drift | `check:db-grants` | Actual grants diverge from `contracts/data/grants.json` |
| Migration reversibility | Custom CI script | Down-migration missing without documented exception |
| Unit tests | Jest | Any failing test |
| Integration tests | Jest + Testcontainers | Any failing test |
| Authorization tests | Jest | Any failing test |
| Security audit | `npm audit` + Trivy | High-severity vulnerability |

---

### 5.3 Testing Strategy

The B2B Portal module follows the layered testing approach defined in Foundation Framework §12. The **Order Submission** and **Multi-Branch Ordering** workflows are the highest-priority test targets due to their financial adjacency, state-machine criticality, and multi-module integration requirements.

#### 5.3.1 Unit Tests

| **Test** | **What it Validates** |
|----------|----------------------|
| Order state guard rejects checkout with empty cart | Business Rule BR-B2B-04 (product availability) |
| Order state guard rejects placement with insufficient stock | Business Rule BR-B2B-04 (product availability) |
| Order state guard rejects placement exceeding credit limit | Business Rule BR-B2B-02 (credit limit enforcement) |
| Multi-branch validation rejects line item without branch mapping | Business Rule BR-B2B-03 (multi-branch constraints) |
| Contract pricing overrides standard catalog price | Business Rule BR-B2B-01 (contract pricing precedence) |
| Support ticket state guard rejects close without resolution reason | Business Rule BR-B2B-08 (ticket resolution) |
| Customer-specific catalog filters products correctly | Business Rule BR-B2B-05 (customer-specific catalog) |
| Order state guard rejects update on confirmed order | Business Rule BR-B2B-06 (order immutability) |
| Idempotency validation prevents duplicate order submissions | Business Rule BR-B2B-07 (idempotent order submission) |

#### 5.3.2 Integration Tests

| **Test** | **What it Validates** |
|----------|----------------------|
| `POST /orders` with valid payload creates order in Placed state in Postgres | API + DB |
| `POST /orders` with multi-branch lines validates all branches | API + DB + Organization module view |
| `POST /orders/id/cancel` transitions order to Cancelled and writes audit log | State machine + audit |
| `POST /orders` with idempotency key returns same result on retry | Idempotency |
| `POST /orders` emits `b2b.order.placed` event | Event publisher |
| `POST /quotations` creates quotation request and emits event | Event publisher |
| `POST /tickets` creates support ticket and emits event | Event publisher |
| Two concurrent `POST /orders` on same product: one succeeds, one receives CONFLICT | Optimistic locking |
| `GET /catalogs` returns customer-specific filtered catalog | Customer scope |
| `GET /catalogs/:id/availability` returns real-time stock | Inventory module view |

#### 5.3.3 Authorization Tests

| **Test** | **What it Validates** |
|----------|----------------------|
| Customer A cannot access Customer B's orders (`b2b.order.read` scope) | Customer-scoped permission |
| Branch Manager cannot place order for another branch | Branch-scoped permission |
| Unauthenticated request to any endpoint returns 401 | Authentication guard |
| Request with expired session token returns 401 | Token validation |
| Customer cannot access admin override endpoints | Role boundary |

#### 5.3.4 Idempotency Tests

| **Test** | **What it Validates** |
|----------|----------------------|
| `POST /orders` with same Idempotency-Key twice → returns original result, creates one order only | Order idempotency |
| Idempotency key expiry after 24 hours | Key management |

#### 5.3.5 End-to-End Tests (Critical Flows)

| **Flow** | **Steps** | **Expected Outcome** |
|----------|-----------|---------------------|
| **Customer Order Flow** | Login → Browse catalog → Add to cart → Multi-branch checkout → Submit order | Order placed; confirmation email; order in Placed state; event emitted |
| **Quotation Request Flow** | Login → Request quotation → Select products → Submit → Receive confirmation | Quotation request submitted; event emitted; customer sees request in list |
| **Support Ticket Flow** | Login → Create ticket → Submit → Receive confirmation | Ticket created; event emitted; customer sees ticket in list; can add messages |
| **Repeat Order Flow** | Login → Order history → Select order → Repeat → Review cart → Checkout | New cart created; checkout flow successful |
| **Financial View Flow** | Login → Financial dashboard → View invoices → Download statement | Invoices displayed; credit limit visible; PDF download available |

#### 5.3.6 Performance Tests

| **Endpoint** | **Target** | **Test Condition** |
|--------------|------------|-------------------|
| `GET /dashboard` | p95 < 500ms | 50 concurrent customers; 1,000+ historical orders in DB |
| `GET /catalogs` | p95 < 300ms | Cursor-paginated; 5,000+ products in catalog |
| `POST /orders` | p95 < 500ms | 20 concurrent submissions; credit and availability checks |
| `GET /orders` | p95 < 300ms | 500+ orders per customer |
| `GET /catalogs/:id/availability` | p95 < 200ms | Real-time stock check against Inventory view |

---

### 5.4 Deployment Readiness

#### 5.4.1 Pre-Deployment Checklist

| **Check** | **Owner** | **Criteria** |
|-----------|-----------|--------------|
| All Prisma migrations include a down-migration | DevOps | CI `check:migrations` gate passes |
| Dry-run migration against staging snapshot | DevOps | No data loss; schema matches expected state |
| Seed data loaded (customer types, product categories, sample data) | Backend Developer 1 | Seed script runs without error |
| All CI gates green on release branch | DevOps | No failures across all gate categories |
| BullMQ queues and Redis connection verified in staging | DevOps | All 7 queue types visible; test job processed |
| Cross-module event routing verified (B2B → Inventory, Sales, Support) | Backend + DevOps | `b2b.order.placed` received by test consumers |
| Performance benchmarks met in staging environment | QA | p95 targets achieved per Section 5.3.6 |
| Security audit: `npm audit` + Trivy image scan | DevOps | Zero high-severity findings |
| Stakeholder UAT sign-off received | Senior Developer | Written or digital approval from stakeholders |
| AGENTS.md and CHARTER.md committed to repository | Senior Developer | Files present at `modules/b2b-portal/` |

#### 5.4.2 Rollback Plan

| **Scenario** | **Rollback Action** |
|--------------|---------------------|
| Database migration failure | Execute down-migration; restore from pre-deployment backup; investigate before re-attempt |
| Application service crash on startup | Roll back to previous Docker image tag; notify DevOps team |
| Event publisher failure | Orders remain in Placed state; manual requeue via admin endpoint; investigate consumer |
| Permission grant drift detected | `check:db-grants` CI gate flags this before deployment reaches production |
| Payment gateway failure | Fallback to manual payment processing; customer notification |

#### 5.4.3 Post-Deployment Verification

| **Verification** | **Method** |
|------------------|------------|
| All API endpoints return 200 on smoke test | Automated smoke test suite (`npm run smoke-test`) |
| Customer Dashboard renders with correct data | Manual verification + automated E2E test |
| BullMQ workers processing queue | Redis queue depth monitoring |
| Audit log entries being written on state transitions | Sample state transition + DB query to `b2b_portal.audit_log` |
| OpenTelemetry traces visible in observability stack | Trace search for `b2b.order.place` span |
| Cross-module events consumed by downstream modules | Event consumer logs verified |
| Email notifications delivered | Test email inbox verified |





