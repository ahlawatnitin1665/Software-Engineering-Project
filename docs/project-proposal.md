# Project Proposal: CampusConnect

A unified campus event and society management platform for Thapar University.

## 1. Introduction

Managing the activities of many student societies across a large campus is difficult. Societies propose events, an authority has to review each proposal, published events have to reach students, and students have to register for what they want to attend. Without a single shared system, this work is spread across emails, notice boards and paper forms, which is slow and creates confusion.

This project proposes **CampusConnect**, a full-stack web platform that brings the whole cycle into one place: a society drafts and submits an event proposal, the governing authority reviews and approves it, the approved event is published on a public campus index, and students discover and register for it. Every actor has their own interface and the platform connects them.

---

## 2. Problem Statement

A university campus is not a single system. Each society keeps its own records and each event is planned in an ad-hoc way. As a result:

- Students cannot find what is happening across the whole campus in one place.
- Authorities have no structured way to review, approve or reject event proposals.
- There is no reliable record of who is implementing an event, what it needs, or who signed up for it.
- Registration for events is manual and there is no capacity or attendance tracking.

The core question this project tries to answer is:

> **Can one platform take an event idea from a society proposal all the way to a registerable, campus-wide event — with approval, publishing, discovery and registration, while keeping each role separate and secure?**

---

## 3. Proposed Approach

CampusConnect is a role-based web application with three kinds of users.

| Role | Responsibility |
| :--- | :--- |
| **ADMIN** | Reviews proposals, approves / rejects / requests changes, manages categories and societies, monitors activity |
| **SOCIETY** | Drafts and submits proposals, publishes approved events, manages members, registrations and notifications |
| **USER (student)** | Discovers events on the campus index, registers and cancels, saves events for later |

The flow of an event idea through the system:

```text
Society drafts proposal
          |
          v
Society submits proposal
          |
          v
Admin reviews
   |-------> Approve -> Event created and published
   |
   |-------> Request changes -> Society edits and re-submits
   |
   |-------> Reject -> Proposal closed with a reason
```

Students never need an account to read the public catalogue — they only log in when they want to register or participate.

---

## 4. System Flow

```text
        SOCIETY                             ADMIN
           |                                  |
           v                                  v
   Draft / edit proposal ----------------> Review queue
           |                                  |
           v                                  v
   Submit proposal                       Approve / Reject / Changes
           |                                  |
           \----------------  <---------------/
                            |
                            v
                     Approved event
                            |
                            v
                    PUBLIC CAMPUS INDEX
                            |
                  +---------+---------+
                  |                   |
                  v                   v
             Event detail        Category / society
             (everyone)          directory (everyone)
                  |
                  v
            Student registers  -----------------> Registration + capacity tracking
                            |
                            v
                     Notifications
```

The public read side (events, categories, societies) has no login wall. The write side (proposals, registrations, reviews) is protected by role-based access.

---

## 5. Architecture Overview

```text
                 +-----------------------------------------------+
                 |                 React SPA (Vite)               |
                 |  Public pages | Student | Society | Admin     |
                 +----------------------+------------------------+
                                        |
                                       /api  (HTTP + JWT)
                                        |
                 +----------------------v------------------------+
                 |             Express API (TypeScript)          |
                 |  Auth  | Public | Student | Society | Admin  |
                 |  uploads | validation (zod) | rate limits    |
                 +----------------------+------------------------+
                                        |
                                   Prisma ORM
                                        |
                 +----------------------v------------------------+
                 |              SQLite (Prisma schema)           |
                 |  Users, Societies, Proposals, Events,        |
                 |  Registrations, Notifications, ActivityLogs   |
                 +-----------------------------------------------+
```

The API is split into public, student, society and admin modules. All routes validate input with `zod`, passwords are hashed with `bcrypt`, and sessions use JWTs (short-lived access token plus refresh token).

---

## 6. Module Breakdown

### Authentication and Authorization
- JWT access + refresh tokens, `ADMIN` / `SOCIETY` / `USER` roles.
- Bcrypt password hashing, protected route middleware, `?next=` deep-linking after login.

### Public module
- Upcoming events, featured event, event detail, categories, society directory and full society profiles.
- Event listing with category / society filters; category catalogue with per-society event lists.
- All available without login.

### Admin module
- Proposal review queue (approve / reject / request changes with comments).
- Event, society, user and registration management; dashboard statistics and activity log.

### Society module
- Proposal lifecycle: draft, submit, edit after feedback, publish approved events.
- Event management, member management, per-event registration lists, notifications.

### Student module
- Event search/filter, individual event page with registration state, register and cancel, saved events, registration history, notifications.

### Notifications and uploads
- In-app notifications triggered on every lifecycle event; poster image uploads with MIME whitelist and size cap.

---

## 7. Example Scenario

Consider the ACM Computing Chapter proposing "Game Jam 48".

1. The society signs in as a **SOCIETY** user, drafts the proposal (title, date, venue, budget, objectives, coordinator) and submits it.
2. The proposal appears in the **admin** review queue. The admin approves it with an optional comment.
3. An event is created from the approved proposal and published.
4. The event appears on the public landing page, under the Hackathons category, and on the society's public profile.
5. A student opens the event page, sees the capacity and live registration count, and registers. The society sees the registration; the student gets a confirmation.
6. If the admin had requested changes instead, the proposal would return to the society for revision and the version number would increment.

---

## 8. Project Objectives

1. Build a single platform covering the full proposal → review → publish → register cycle.
2. Provide separate, secure interfaces for admin, society and student roles.
3. Make the entire read-side (events, categories, societies) public without login.
4. Track registrations, capacity and attendees per event.
5. Notify the right people at every lifecycle step.
6. Support a realistic campus dataset (57 TIET societies across 11 categories).
7. Validate and harden the API (zod validation, helmet, rate limiting, restricted CORS).
8. Cover the system with automated integration tests.

---

## 9. Expected Outcome

A working full-stack prototype that demonstrates the complete event lifecycle on a real campus dataset, divided into an 8-week delivery plan (see `Readme.md`). The prototype includes:

- Public editorial-style marketing site with event and society discovery.
- Role-based dashboards for admin, society and student.
- End-to-end proposal approval flow with notifications.
- Registration, capacity and attendance tracking.
- A passing integration test suite and production-ready build.

---

## 10. Scope

The platform is scoped to campus event and society management. It targets Thapar University's society structure and is seeded with the real TIET society list.

Included: proposals, reviews, events, registrations, saved events, memberships, notifications, activity logs, categories and the public discovery experience.

Out of scope for this project: payments, ticket sales, live streaming, and mobile applications. The API is PostgreSQL-ready (via Prisma) but is run on SQLite for the prototype.