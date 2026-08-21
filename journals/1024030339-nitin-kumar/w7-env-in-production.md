# Week 7 : Production build calls localhost

## Error:

`vite build` succeeded and the static bundle deployed, but
every API call hit `http://localhost:5000/...` and failed
with CORS/404 errors.  The `VITE_API_URL` value we had set
for production was silently ignored.

> frontend served on :8080
> GET http://localhost:5000/api/events  -> network error / CORS

## Relevant Context

In Week 1 we moved the API client to a relative `/api`
base URL so the Vite dev proxy could handle routing.
Later someone added an environment switch:

``` tsx
const api = axios.create({
  baseURL: process.env.VITE_API_URL ?? "http://localhost:5000/api",
});
```

The fallback `"http://localhost:5000"` is exactly the
hard-coded value we removed in Week 1.

## Key Observation

`process.env` **does not exist** in a Vite SPA.  Vite only
statically replaces `import.meta.env.VITE_*` variables at
*build* time -- anything else is left as a bare reference
that evaluates to `undefined` in the browser bundle.

So `process.env.VITE_API_URL` was `undefined` at runtime,
the `??` fallback kicked in, and the dead localhost string
from Week 1 came back to life.  It looked like the config
was broken, but the code was reading the wrong env surface
entirely.

## Solution

Read environment through Vite's API, in exactly one place:

``` tsx
// services/api.ts
const baseURL = import.meta.env.VITE_API_URL ?? "/api";
const api = axios.create({ baseURL });
```

* Dev keeps the relative `/api` default → Vite proxy still
  works (no change needed).
* Production sets `VITE_API_URL=https://api.example.com` in
  the build environment or `.env.production`.
* Grep for `process.env` in the frontend and delete every
  occurrence -- it has no meaning in the browser.

**Because** env vars are substituted at build time by Vite
(`import.meta.env`), not read at runtime from the OS
(`process.env`); keeping the relative `/api` default means
the fallback can never reintroduce a fixed origin.

## Files touched

+ `code/frontend/src/services/api.ts`
+ `code/frontend/.env.production` (`VITE_API_URL`)
+ `code/frontend/.env.production.sample` (documented)