# Week 2 : Society counts hard-coded in tests

## Error:

The seed suddenly made the test suite fail, although no
production code had changed.

> expect(6).toBe(57)
>     at tests/app.test.ts:158

## Relevant Context

We replaced the placeholder seed data with the **real TIET
society list** -- 57 societies across 11 categories, each
with a manager account.  The database still seeded
cleanly, but the integration suite broke because it
embedded the old dataset's expectations:

+ `tests/app.test.ts` asserted `totalSocieties === 6`
  (and the admin society-pagination total `=== 6`)
+ `verify.ts` asserted `societyUsers === 6` and referred
  to a placeholder society name

## Key Observation

The failures were *data assertions*, not *behaviour
assertions*.  They were written for the demo dataset and
became wrong the moment the dataset changed.  That is a
coupling smell: tests should assert invariants ("active
societies are returned", "only this category is
returned"), not literal row counts copied from a seed.

## Solution

Update the few genuinely meaningful count assertions to
the new dataset and make the rest future-proof:

``` typescript
expect(res.body.data.length).toBe(tech.societyCount); // dynamic
expect(users.societyCount).toBe(57);                  // seed sanity
```

`verify.ts` was updated to

``` typescript
const societyUsers = await prisma.user.count({ where: { role: 'SOCIETY' } });
if (societyUsers !== 57) throw new Error(...);
```

and the society-name references moved to the canonical
"Creative Computing Society (CCS)".

**Because** a seed's job is to be stable enough for tests
to check the system, while tests should compute expected
values from the system rather than from memory.

## Files touched

+ `code/backend/prisma/seedData.ts` (57 TIET societies)
+ `code/backend/prisma/seed.ts`
+ `code/backend/prisma/verify.ts`
+ `code/backend/tests/app.test.ts` (count assertions)