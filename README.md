# Queue Kiosk

A **Django-based digital queue management system** for banks, hospitals, government offices, telecom centers, and other service counters. Customers take a token from a kiosk (or other channels), staff call the next person from a counter desk, and a public display keeps the waiting area updated in real time.

> **Note:** Source code is private for now. This repository is a project overview for portfolio use. Feel free to open an issue or contact me if you'd like a demo walkthrough.

---

## Overview

Queue Kiosk replaces paper tokens and shouted numbers with a structured digital workflow:

1. **Customer** selects a service and priority at the kiosk and receives a token number (e.g. `A-001`).
2. **Counter staff** call the next waiting token, start service, complete, skip, or mark no-show.
3. **Lobby display** shows currently called / serving tokens and queue pressure for the branch.
4. **Customer** can look up token status and submit multi-dimensional feedback after completion.

The system is designed around **organizations → branches → services → counters**, so one deployment can support multiple branches and service lines.

---

## Screens & Roles

| Screen | Purpose |
|--------|---------|
| **Dashboard** | Branch summary (waiting / called / serving / completed), recent tokens, token lookup, service totals |
| **Kiosk** | Customer-facing token generation by service and priority |
| **Counter** | Operator console to call next, serve, complete, skip, or no-show |
| **Display** | Public waiting-area board for live token announcements |
| **Token detail** | Full lifecycle of a single token |
| **Feedback** | Post-service ratings (overall, staff, service, waiting time) + comments |
| **Admin** | Configure organizations, branches, services, counters, devices, and users |

---

## Core Features

- **Token generation** — Sequential, service-prefixed token numbers with optional daily limits and kiosk enable/disable per service
- **Token calling** — Counter staff call the next eligible token with announcement text
- **Service lifecycle** — Status flow: Waiting → Called → Serving → Completed (plus Skipped / No Show / Cancelled / Transferred)
- **Priority queueing** — Emergency, VIP, pregnant, elderly, differently abled, and normal, ordered fairly with generation time as tie-breaker
- **Customer feedback** — Ratings and comments only after a token is completed
- **Status history** — Every status change is recorded for audit and reporting

---

## Domain Model (high level)

- **Organization & Branch** — Multi-tenant style structure with branch hours and contact details
- **Service categories & services** — Categorized services with token prefixes and kiosk flags
- **Counters & assignments** — Counters linked to services; staff can be assigned to counters
- **Queue tokens** — Source (kiosk / mobile / website / SMS / staff), priority, status, timestamps, current counter, served-by user
- **Token calls** — Call attempts and announcement text per counter
- **Devices** — Kiosk / display / other device registry for branch hardware
- **Notifications** — Channel and status models for future SMS / email / app alerts
- **Subscribers** — Optional customer identity linked to tokens and feedback

---

## Tech Stack

| Layer | Choice |
|-------|--------|
| Backend | Python, Django |
| Database | SQLite (dev) / PostgreSQL-ready |
| UI | Server-rendered Django templates |
| Phone fields | `django-phonenumber-field` + `phonenumbers` |
| Deploy examples | nginx, uWSGI (config samples prepared) |

---

## Architecture Snapshot

```text
Customer (Kiosk / Web)
        │
        ▼
   Queue Engine  ──►  Token + Status History
        │
   ┌────┴────┐
   ▼         ▼
Counter    Display
 Desk       Board
   │
   ▼
Feedback & Analytics-ready events
```

The queue engine owns business rules such as:

- Creating the next sequence number safely under concurrency
- Enforcing service/branch and kiosk availability rules
- Selecting the next token by priority rank, then arrival time
- Preventing invalid status transitions (e.g. feedback only when completed)

---

## What Makes This Project Interesting

- **Real operational workflow**, not just CRUD — call desk, lobby display, and kiosk are separate UX surfaces on the same data model
- **Priority-aware fair queueing** with explicit ranks and history
- **Branch-centric multi-service design** suitable for real institutions
- **Extensible foundation** for notifications, multi-channel token sources, analytics, and CRM-style subscriber links

---

## Roadmap / Planned Extensions

- Real-time updates (WebSocket / SSE) on display and counter screens
- SMS / email notifications when a token is about to be called
- Analytics dashboards (wait time, service duration, satisfaction)
- Multilingual kiosk and feedback UI
- Offline-tolerant device mode with later sync
- Mobile / web self-service token channels beyond the physical kiosk

---

## Author

**Shahed Mohammad Hridoy** ([@shahedpy](https://github.com/shahedpy))
