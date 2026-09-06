![Tiet Logo](assets/tiet-logo.svg){ .tiet-logo }

**UCS503: Software Engineering (Project)**  
**TIET Patiala**  
**2026-27 ODD**

# CampusConnect

A unified campus event and society management platform for Thapar
University.

**Author(s)**:

+ `(Nitin Kumar)` `<nkumar_be24@thapar.edu>`, Roll No. `1024030339`
+ `(Mohan Arora)` `<marora_be24@thapar.edu>`, Roll No. `1024031087`
+ `(Prateek Malpani)` `<pmalpani_be24@thapar.edu>`, Roll No. `1024030340`

## Idea

A campus runs on hundreds of society events, but there is no single system
that takes an event idea from a society proposal all the way to a
registerable, campus-wide event.  CampusConnect connects the three actors on
one platform:

+ **Societies** draft and submit event proposals, then publish the approved
  events.
+ **Administration** reviews each proposal -- approve, reject, or request
  changes -- and manages categories, societies, users, and activity.
+ **Students** discover everything happening across campus on a public
  index, and register for events.

The read-side of the platform (events, categories, society directory and
society profiles) is fully public.  Login is required only to write:
proposals, reviews and registrations.

## Status

**Work in progress -- Week 2 of 8.**  The project is being
developed in short weekly iterations and this repository
tracks the work as it happens.  Only the weeks that are
actually complete appear in the journals below.

## Planned 8-week outline

| Week | Theme | Status |
| :--- | :--- | :--- |
| 1 | Repository & architecture | &#10003; done |
| 2 | Core backend & auth | &#10003; done |
| 3 | Admin module | planned |
| 4 | Society module | planned |
| 5 | Student module | planned |
| 6 | Public experience & data | planned |
| 7 | Depth & hardening | planned |
| 8 | Testing & delivery | planned |

Each completed week is detailed as a ticket-style journal
entry under `journals/<roll-no>-<name>/`, where every team
member logs their own error-relevant-context-solution
write-up for the week.

## Source

The implementation lives in `code/` and is being pushed as
the project progresses:

```
code/
 +-frontend/    React 19 + TypeScript + Vite SPA
 +-backend/     Node.js + Express + TypeScript + Prisma API
```

Setup and run instructions are in `code/README.md`.
During the current iteration only the scaffold and the
design/tech decisions are tracked; the full source will be
committed as development continues.

## Reports

+ `project-proposal/main.tex` -- Project Proposal (LaTeX).
+ `project-report-prototype-stage/` -- Report at the prototype stage.
+ `project-report-final/` -- Final report.