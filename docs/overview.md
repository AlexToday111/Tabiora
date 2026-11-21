# Project Overview

**Design stage.** Tabiora, formerly DineFlow, is the Restaurant Service System described in the course materials.

## Problem and product goals

Customers need to find a restaurant, reserve a table, and plan dishes before arrival. During the visit, they need to reach staff and settle the bill. Tabiora uses a smartphone-accessible interface to coordinate these tasks with restaurant staff. Staff need a consistent view of reservations, pre-orders, and calls. Tabiora can relay a requested ready time. Any guarantee depends on the restaurant agreement.

## Roles and responsibilities

| Role | Goal | Recorded scope | Proposed allocation requiring validation |
| --- | --- | --- | --- |
| Customer | Plan a visit and obtain service | Search/filter restaurants, view information, floor plans and menus, book, pre-order, prepay, call a waiter, and pay the bill | Ownership checks for access to personal bookings/orders; identity mechanism TBD |
| Waiter | Respond to in-restaurant requests | Receive waiter-call notifications as restaurant staff (FR-014) | Acknowledge and resolve assigned calls; read relevant pre-orders for the assigned restaurant |
| Manager | Coordinate restaurant operations | Restaurant staff collectively manage reservations and pre-orders (FR-012–FR-013) | Manage restaurant-scoped reservations, pre-orders, and availability; menu editing only if explicitly agreed |

P-STAFF proposes restaurant-specific Waiter and Manager roles. NFR-006 requires authorization for management. See the [permission matrix](requirements/functional-requirements.md#proposed-staff-permissions) for dual roles and access boundaries.

## Main business workflows

Customers discover a restaurant and book a table, then may pre-order and prepay. During a visit they can call a waiter or settle the separate restaurant bill. Current occupancy and personalization are Should Have. Average occupancy, dish advice, additional orders, favorites, and ratings are Could Have. [Use cases](requirements/use-cases.md)

## Product boundaries

Tabiora coordinates digital bookings, orders, payments, and calls. The restaurant handles seating, preparation, and service. Procurement, payroll, kitchen scheduling, and guarantees of dish acceptance are outside the current specification.

The Payment Provider and Restaurant/POS are external collaborators. AC-001 and AC-005 govern restaurant integration. AC-002 recommends external card processing. Authority, bill ownership, cancellation, no-show, refund, and food refusal policies remain in the [decision register](requirements/open-questions.md).

## Release scope and project scale

Must Have requirements define the candidate MVP. Prototype simulations leave those priorities intact. Should Have and Could Have features remain later candidates, and FR-027 blacklist management is excluded initially. [Functional requirements](requirements/functional-requirements.md)

## Target businesses and planning scale

The proposed audience is independent table-service restaurants and table-service branches of small chains. Reservations, pre-orders, and waiter calls drive that choice. Each restaurant is a separate business context (AC-004). Counter-service workflows are a possible future adaptation. Chain-wide administration remains unspecified.

**P-SCALE is illustrative.** Validate the assumptions before release planning or load-test acceptance.

| Dimension | Illustrative pilot assumption | Validation needed |
| --- | --- | --- |
| Geography | One region in one country; actual region/country TBD | Initial service area and restaurant operating times |
| Businesses | About 20 table-service restaurants, independent businesses and/or small-chain branches | Restaurant count, grouping, and onboarding variation |
| Tables | About 30 tables per restaurant; approximately 600 total | Real floor plans, capacities, and turnover |
| Customer population | About 2,000 customer accounts | Active-customer and retention estimates |
| Concurrent use | 100 active customer sessions plus 40 staff sessions across the platform | Session activity, device/connectivity mix, and peak pattern |
| Daily volume | About 500 reservation requests and 300 pre-order creations per day | Accepted/rejected mix, cancellations, and per-restaurant distribution |
| Peak write workload | Short test bursts of 2 reservation requests/second and 2 pre-order creations/second across the platform | Burst duration and mix; proposed test burst is one minute, not a sustained-demand estimate |

Treat sessions, daily volume, and peak rates as separate workload dimensions. Test same-table contention independently of aggregate traffic. Search, menu, payment, and call rates remain TBD.

International expansion remains a future scenario. The [expansion assessment](architecture/README.md#geographical-expansion-assessment) covers software constraints, including China as an instructor example.
