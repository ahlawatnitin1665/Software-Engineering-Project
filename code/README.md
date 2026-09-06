# Code — Status

**Status: in progress — Week 2 of 8.**

The implementation is being scaffolded during the
current iteration.  The full source is **not** committed
to this repository yet; it will be pushed in a later
week as the project progresses.

## Planned layout

```
code/
 +-frontend/    React 19 + TypeScript + Vite SPA
 |   +-src/     (public / student / society / admin areas)
 +-backend/     Node.js + Express + TypeScript + Prisma API
     +-src/modules/  (auth, public, student, society, admin)
     +-prisma/   (schema, seed)
     +-tests/    (Vitest + supertest integration tests)
```

## Stack decisions (Week 1–2)

* **Frontend:** React 19, TypeScript, Vite, Tailwind CSS,
  TanStack Query, React Router, zod, react-hook-form.
* **Backend:** Node.js, Express, TypeScript, Prisma ORM,
  JWT auth (access + refresh), multer uploads, zod
  validation, helmet + rate limiting + restricted CORS.
* **Database:** SQLite for the prototype
  (PostgreSQL-ready via Prisma).
* **Testing:** Vitest (backend integration via supertest;
  frontend with Testing Library).

## What has been done so far (Week 1–2)

* Repository and CI scaffolding (mkdocs GitHub Pages
  workflow, Makefile).
* Requirements and system design captured in
  `project-proposal/main.tex`.
* Prisma schema drafted (User, Society, SocietyMember,
  EventCategory, EventProposal, Event, Registration,
  Notification, ActivityLog, SavedEvent) and the database
  seeded with the TIET society list.
* Development environment: Vite dev proxy

&rarr; CORS, JWT authentication and the role-based route
guard are the active task for Week 3.

## Run (once available)

``` shell
make -C code/backend dev    # backend API on :5000
make -C code/frontend dev   # frontend on :5173
```