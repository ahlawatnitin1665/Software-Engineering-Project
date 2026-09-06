# UCS503P Project

**UCS503 : Software Engineering : Project** — **2026-27 ODD**

**Project: CampusConnect** — a unified campus event and society management
platform for Thapar University.

This repository follows the [UCS503P project
template](https://github.com/tiet-ucs503/ucs503p-202627odd-template).  There
are 3 reports in LaTeX format, namely *a*) Project Proposal, *b*) Project
Report Prototype Stage, and *c*) Project Report Final -- each in their
respective folders.

Journals are stacked under the folder `journals`, one folder for each team
member.

The source code is contained within the folder `code`.

The documentation is under folder `docs`.

All other aspects of code organisation are left to the discretion of the
user(s).

## Status

**Work in progress — Week 2 of 8.**

This repository is updated progressively as the project is developed in
weekly iterations.  Only the completed weeks appear in the journals under
`journals/`; the full source will be committed to `code/` as the
implementation progresses.

## Team

| Member | Roll No | Email |
| :--- | :--- | :--- |
| Nitin Kumar | 1024030339 | `nkumar_be24@thapar.edu` |
| Mohan Arora | 1024031087 | `marora_be24@thapar.edu` |
| Prateek Malpani | 1024030340 | `pmalpani_be24@thapar.edu` |

## Project at a glance

CampusConnect takes a society event idea all the way from proposal to a
registerable, campus-wide event.  Three roles work on one shared platform:

* **ADMIN** reviews proposals (approve / reject / request changes), manages
  categories, societies, users, and activity.
* **SOCIETY** drafts and submits proposals, publishes approved events, and
  manages members, registrations, and notifications.
* **USER (student)** discovers events on the public campus index and
  registers and saves them.

The whole read side (events, categories, society directory) is public and
requires no login; login is only needed to participate.

## Docs

The `docs` is an organised collection of markdown (`md`) files.  The build
procedure uses the [`mkdocs`](https://google.com/search?q=mkdocs) backend.
As a result, any commit into the `master`/`main` branch of this repository
results in a CI/CD based build and deployment of the documentation including
the journals.

For a local DEV-version of the docs for viewing and testing, install the
local env and issue the following command:

``` shell
make docs
```

### Local `env` for `docs`

``` shell

```