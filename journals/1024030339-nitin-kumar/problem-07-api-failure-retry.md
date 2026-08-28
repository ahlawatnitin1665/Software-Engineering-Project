# Problem 7 : Translation API failure

## Problem

External APIs can have problems such as:

+ Request timeout
+ API failure
+ Rate limiting
+ Malformed response

## How we fixed it

We implemented:

+ retry handling
+ timeout handling
+ chunk-level status tracking

The statuses include:

```
Waiting
   ↓
Translating
   ↓
Translated
```

or

```
Failed
```

This is documented in the report.

## Viva answer

"Another issue was that translation API requests can fail
or take too long.  We handled this using retry and timeout
mechanisms.  We also maintain the status of each chunk,
such as Waiting, Translating, Translated or Failed."