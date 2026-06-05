# Lock by Citrus

**Lock by Citrus** is a security-focused SaaS web application designed to help supermarkets control, authenticate, track, and audit the movement of cash from cashier tills to the strong/storage room.

The platform reduces internal cash-handling risk by enforcing authenticated collection workflows, OTP or QR/barcode verification, real-time timers, audit logging, and escalation alerts to branch managers, headquarters, and security teams.

> Project objective: curb theft at local supermarkets by automating and monitoring the cash collection and strong-room deposit process.

---

## Table of Contents

- [Overview](#overview)
- [Core Problem](#core-problem)
- [Primary Users](#primary-users)
- [Core Features](#core-features)
- [Product Workflow](#product-workflow)
- [Technology Stack](#technology-stack)
- [Architecture Overview](#architecture-overview)
- [Security Model](#security-model)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Database Setup](#database-setup)
- [Running the App](#running-the-app)
- [Testing](#testing)
- [Development Principles](#development-principles)
- [Repository Structure](#repository-structure)
- [Deployment Notes](#deployment-notes)
- [Data Privacy](#data-privacy)
- [License](#license)

---

## Overview

Lock by Citrus manages the cash movement lifecycle inside supermarkets.

The platform is built around one high-risk operational flow:

1. A cashier requests cash collection from a till.
2. A Chief Cashier or Assistant Chief Cashier authenticates before collecting the money.
3. The collection is recorded with staff identity, amount, time, and collection metadata.
4. The collector must move the funds to the strong/storage room within a configured time window.
5. The collector authenticates again at the strong/storage room.
6. The platform stops movement timers, records the deposit event, and logs the full audit trail.
7. Alerts escalate automatically when expected actions are delayed, failed, suspicious, or unauthorized.

---

## Core Problem

In many supermarkets, the same people responsible for collecting cashier till revenue are also responsible for transporting that cash to the strong/storage room.

That creates a security gap.

Lock by Citrus addresses this by making every critical cash-handling step:

- authenticated;
- timestamped;
- traceable;
- auditable;
- time-bound;
- visible to management;
- escalated when abnormal behavior occurs.

---

## Primary Users

The platform supports supermarket and security stakeholders involved in cash handling and monitoring.

### Supermarket Users

- Branch Manager
- Chief Cashier
- Assistant Chief Cashier
- Cashier
- Headquarters user / HQ monitoring user

### Security Users

- On-site security personnel
- Security company supervisors
- External security company users

### System Users

- Platform administrator
- Super admin
- Support administrator
- Audit/review user

---

## Core Features

### 1. Cash Collection Authentication

Lock by Citrus authenticates the person collecting funds from a cashier till.

Supported authentication methods:

- OTP PIN sent by SMS
- QR code scan
- Barcode scan

The system records the collector’s identity and authentication event before cash collection proceeds.

Recorded collector details may include:

- profile photo;
- first name and last name;
- ID number;
- staff number;
- phone number;
- email address;
- date and time of authentication.

---

### 2. Cash Collection Finalization

After collecting funds from the cashier drawer, the collector must finalize the collection.

The finalization process may require:

- a second OTP PIN;
- a second QR/barcode scan;
- total amount collected;
- cashier staff ID;
- cashier identity verification;
- final collection timestamp.

This creates an auditable handover between the cashier and the collector.

---

### 3. Till-to-Strong-Room Movement Tracking

Once collection is finalized, the platform starts real-time timers.

### Timer 1: Deposit Deadline Countdown

Tracks whether the authenticated collector reaches the strong/storage room within the configured time limit.

When the deadline is missed, alerts escalate by severity.

### Timer 2: Actual Movement Duration

Tracks how long the authenticated collector takes to move from the till to the strong/storage room.

This creates operational visibility and historical movement records.

---

### 4. Strong/Storage Room Authentication

At the strong/storage room, the authenticated collector must verify access.

Supported strong-room authentication methods:

- OTP PIN entered on the door lock keypad;
- QR code scan;
- barcode scan.

The platform confirms that the person attempting access is authorized for that specific supermarket branch and door lock.

After successful authentication:

- Timer 1 stops;
- Timer 2 stops;
- the deposit event is recorded;
- the collector can access the strong/storage room.

---

### 5. Strong-Room Occupancy Timer

After the authenticated collector opens the strong/storage room, the platform starts Timer 3.

### Timer 3: Strong-Room Stay Duration

Tracks how long the authenticated user remains inside or associated with the strong/storage room access event.

When the configured time limit is exceeded, alerts escalate automatically.

---

### 6. Alert Escalation

Lock by Citrus supports real-time alert escalation.

Alert levels may include:

| Alert Level | Meaning |
|---|---|
| Yellow Alert | Early warning; a configured time threshold has been exceeded. |
| Orange Alert | Higher-risk delay or abnormal event requiring manager/security attention. |
| Red Alert | Serious security concern requiring immediate action by management and security. |
| Danger Alert | Unauthorized access attempt or critical authentication failure. |

Alerts may be sent to:

- Branch Manager;
- Headquarters;
- on-site security personnel;
- security company;
- platform administrators.

---

### 7. Unauthorized Access Detection

The system flags unauthorized access attempts, including:

- incorrect OTP attempts;
- incorrect QR/barcode scans;
- failed strong-room authentication;
- suspicious attempts where no authenticated collector is expected;
- repeated failed access attempts.

Depending on configuration, the platform may:

- flag the associated user;
- disable affected access credentials;
- notify managers;
- notify headquarters;
- notify on-site security;
- notify the security company;
- create an incident report.

---

### 8. Audit Trail

Every critical action should be recorded.

Audit records may include:

- user ID;
- staff number;
- role;
- branch;
- till/counter number;
- strong-room door lock number;
- authentication method;
- OTP/QR/barcode verification status;
- cash amount;
- cashier identity;
- collector identity;
- timestamps;
- alert level;
- incident status;
- IP/device metadata where applicable.

---

## Product Workflow

### Workflow A: Supermarkets Without Existing Authorization Functionality

For supermarkets that do not already have internal authorization functionality, Lock by Citrus can generate new unique identifiers.

Examples:

- new QR codes;
- new barcodes;
- platform-managed authentication identifiers;
- user-linked phone numbers for OTP delivery.

### Workflow B: Supermarkets With Existing Authorization Functionality

For supermarkets that already use local authorization systems, Lock by Citrus can support reusing or importing existing identifiers.

Examples:

- existing QR codes;
- existing barcodes;
- existing staff identifiers;
- existing user phone numbers.

---

## Technology Stack

The recommended stack for this SaaS web application is:

| Layer | Technology |
|---|---|
| Backend | Laravel / PHP |
| Frontend | Vue.js or React.js |
| Frontend Language | JavaScript with TypeScript |
| Styling | Tailwind CSS or Bootstrap 5 |
| Database | MySQL or PostgreSQL |
| Authentication | Laravel Sanctum or Laravel Passport |
| API Style | REST or GraphQL |
| SMS/OTP | Configurable SMS provider |
| QR/Barcode | QR and barcode generation/scanning libraries |
| Queue Processing | Laravel Queues |
| Scheduler | Laravel Scheduler |
| Realtime Events | WebSockets, broadcasting, polling, or notification service |

### Not Recommended

jQuery is not recommended for this project because the app should be built using modern, component-based frontend architecture.

---

## Architecture Overview

Recommended architecture:

```text
+-----------------------+
| Frontend Web Client   |
| Vue/React + TS        |
+----------+------------+
           |
           | REST / GraphQL API
           |
+----------v------------+
| Laravel Backend API   |
| Auth, Workflows, RBAC |
+----------+------------+
           |
           +----------------------+
           |                      |
+----------v----------+  +--------v---------+
| Database            |  | Queue Workers    |
| MySQL/PostgreSQL    |  | Alerts, SMS, Jobs|
+----------+----------+  +--------+---------+
           |                      |
+----------v----------+  +--------v---------+
| Audit Logs          |  | External Services|
| Events, Incidents   |  | SMS, Door Locks  |
+---------------------+  +------------------+
