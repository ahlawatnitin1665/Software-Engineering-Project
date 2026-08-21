# Week 6 : Event start time shifted by five and a half hours

## Error:

Entering `2026-10-05 14:00` as an event's start time, then
re-opening the event, showed `08:30` in the input -- and on
the calendar card the date had even moved back a full day
in some rows.

> saved:   "2026-10-05T08:30:00.000Z"
> shown:   2026-10-05 08:30   (expected 14:00)

## Relevant Context

The form uses a native `<input type="datetime-local">`,
which returns a *wall-clock* string with no timezone:
`"2026-10-05T14:00"`.  We converted it with
`new Date(value)` (parsed as **local** time, IST +05:30),
then sent `.toISOString()` to the backend, which stores
UTC.  When reading data back, the event row rendered the
stored ISO string **directly** via `value.slice(0, 16)` --
i.e. it printed UTC digits as if they were the local wall
clock.

## Key Observation

The same wall-clock value was being interpreted on two
different timezones at different layers:

1. **Write path:** `new Date("2026-10-05T14:00")` = local
   `14:00` → `.toISOString()` = `08:30Z`.
2. **Read path:** the raw `08:30Z` string was sliced and
   printed as-is, never passed back through
   `new Date(...).toLocaleTimeString()`.

Neither path is wrong on its own; mixing local and UTC
*representations* in the UI is what produced the shift.
Anything past-midnight local flipped into a different UTC
date, which is why some rows appeared a day off.

## Solution

Pick one timezone per layer and stay explicit:

* Store UTC (server already does).
* Always render through a local formatter, never by
  slicing the ISO string:

``` tsx
const fmt = new Intl.DateTimeFormat("en-IN", {
  dateStyle: "medium",
  timeStyle: "short",
});
fmt.format(new Date(event.startsAt)); // "5 Oct 2026, 2:00 pm"
```

* For re-editing, convert back with
  `date-fns`'s `toDate` handling of the ISO string, which
  localises (not string-slicing).
* On the *write* side, keep sending `toISOString()` and
  pin the zod schema to reject naive strings with no zone.

**Because** a wall-clock is meaningless without a zone
attached -- the bug was reading the UTC serialisation as if
it were already local display text.

## Files touched

+ `code/frontend/src/utils/date.ts` (shared formatters)
+ `code/frontend/src/pages/society/CreateEventForm.tsx`
+ `code/frontend/src/components/EventCard.tsx`
+ `code/backend/src/modules/events/event.schema.ts` (zod)