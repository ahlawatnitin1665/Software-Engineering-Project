# Week 1 : Schema edits not reflected in the database

## Error:

After adding two new models to `schema.prisma`, querying
them at runtime failed with a validation error while
`prisma db push` reported success.

> Error: P1012 error: The provided query violates the
> required constraints...  The client is out of sync with
> the database schema.

## Relevant Context

The Prisma setup uses a SQLite database (`dev.db`) driven
by `schema.prisma`.  We had just defined the `Event`,
`Registration`, `Notification`, `ActivityLog` and
`SavedEvent` models and run `npx prisma db push`, which
reported that the database was in sync.

## Key Observation

`prisma db push` synchronises the **database**, but the
TypeScript **client** the application imports
(`@prisma/client`) is a generated artifact.  Editing the
schema does not regenerate the client -- every new model,
relation and field is invisible to the running server
until the client is generated again.  The two sides,
database and client, can move out of sync independently.

## Solution

Make the sequence explicit in every environment:

``` shell
npx prisma db push        # sync the database schema
npx prisma generate       # regenerate the client
```

and encode it in a single run-script so nobody does one
without the other:

``` json
{ "db:sync": "prisma db push && prisma generate" }
```

We also added a `verify.ts` script that runs after
seeding and reads back counts through the client -- so a
stale client fails the verification loudly instead of
failing silently at runtime.

**Because** a validated schema and a generated client are
two different artifacts; verification must exercise the
client, not just the database.

## Files touched

+ `code/backend/prisma/schema.prisma` (7 models, enums)
+ `code/backend/package.json` (`db:sync` script)
+ `code/backend/prisma/verify.ts`
+ `code/backend/prisma/seed.ts`