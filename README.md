<p align="center">
  <img src="assets/images/tabiora-header.png" alt="Tabiora" width="640">
</p>

Tabiora is a planned restaurant platform that helps customers choose a restaurant, reserve a table, pre-order dishes, request waiter assistance, and pay by phone.

**Status: Design and documentation.** Application and integration implementation are pending.

## Capabilities and architecture

The candidate MVP includes restaurant discovery, table reservations, timed pre-orders and prepayment, waiter calls, in-restaurant bill payment, and staff management of incoming bookings and orders. The [functional requirements](docs/requirements/functional-requirements.md#must-have) define release priorities. The [overview](docs/overview.md#roles-and-responsibilities) covers users and the proposed pilot.

The pilot proposes one modular backend for the Customer App and Staff Interface. It uses logical ports for booking authority, payment verification, restaurant handoff, and staff notifications. See the [Architecture Map](docs/architecture/architecture-map.md).

## Documentation

| Section | Start here |
| --- | --- |
| [Project overview](docs/overview.md) | Problem, stakeholders, scope, and pilot assumptions |
| [Functional requirements](docs/requirements/functional-requirements.md) | FR priorities, acceptance checks, states, and permissions |
| [Non-functional requirements](docs/requirements/non-functional-requirements.md) | Quality attributes and verification |
| [Architectural constraints](docs/requirements/architectural-constraints.md) | Mandatory constraints and integration recommendations |
| [Use cases](docs/requirements/use-cases.md) | Role diagrams and UC-01–UC-08 |
| [Architecture and UML](docs/architecture/README.md) | Baseline, diagrams, ADRs, and deployment view |
| [Open stakeholder questions](docs/requirements/open-questions.md) | Questions, decisions, and proposals |
| [Development approach](docs/development/methodology.md) | Sprint plan and prototype validation |

## Laboratory materials

The [coursework in Google Docs](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit) is indexed below by its repository counterpart.

| Source section | Repository location |
| --- | --- |
| Topic | [Project overview](docs/overview.md) |
| Lab 2 — stakeholder questions | [Decision register](docs/requirements/open-questions.md) |
| Lab 3 — requirements and constraints | [Functional](docs/requirements/functional-requirements.md), [non-functional](docs/requirements/non-functional-requirements.md), and [architectural](docs/requirements/architectural-constraints.md) requirements |
| Lab 4 — use-case diagram | [Overview and Customer/Waiter/Manager views](docs/requirements/use-cases.md) |
| Lab 5 — lifecycle and methodology | [Development approach](docs/development/methodology.md) |
| Lab 6 — class diagram | [Domain overview and focused class models](docs/architecture/class-diagram.md) |
| Lab 7 — sequence diagram | [Reservation, pre-order/checkout exit, payment, and waiter-call scenarios](docs/architecture/sequence-diagram.md) |
| Lab 9 — architecture decisions | [ADR-001 and ADR-002](docs/architecture/README.md#architecture-decisions-and-operations) |
| Lab 10 — system architecture and design | [Architecture Map](docs/architecture/architecture-map.md) |
| Lab 11 — implementation and testing | [Deployment & Operational View](docs/architecture/deployment-operational-view.md) and planned architecture verification |

## Repository structure

```text
docs/
  overview.md                 Project context and boundaries
  requirements/               Baseline, acceptance checks, use cases, decisions
  architecture/               Baseline, map, ADRs, deployment, and UML
  development/                Planned lifecycle and methodology
assets/
  images/                     README images
  diagrams/                   Current SVGs and source-linked PNG exports
    source/                   Editable PlantUML and draw.io sources and rendering guide
```

See the [diagram source guide](assets/diagrams/source/README.md) for source/image pairs and rendering instructions.

## About the Author

[Alex Today (@AlexToday111)](https://github.com/AlexToday111) contributed to Tabiora's requirements and UML models.
