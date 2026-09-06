# Week 1 : Vite dev proxy and the CORS wall

## Error:

While wiring the frontend and backend together for the
first time, browser based API calls failed.

> Access to XMLHttpRequest at 'http://localhost:5000/api/...'
> from origin 'http://localhost:5173' has been blocked by
> CORS policy: No 'Access-Control-Allow-Origin' header is
> present on the requested resource.

## Relevant Context

The project is a monorepo with two dev servers:

+ **frontend**  : Vite dev server on `:5173` (React 19)
+ **backend**   : Express API on `:5000`

The API client (`axios`) was configured with a base URL of
`http://localhost:5000/api`, so every request crossed
origins (`5173` → `5000`).  The Express app used `cors()`
with no explicit origin and `helmet`, and the browser
rejected the response before any code ran.

## Key Observation

Two separate problems were compounding each other:

1. A hard-coded absolute base URL makes the frontend
   depend on a fixed port and forces CORS to be handled
   for every environment.
2. Relying on the default `cors()` behaviour means the
   backend is open to *any* origin, which is wrong for a
   course project where we later deploy to a known
   `FRONTEND_URL`.

The simpler pattern for local development is to **let
Vite proxy `/api` to the backend**, so the browser only
ever talks to same-origin `:5173` and CORS is not even
triggered in development.

## Solution

In `vite.config.ts`, proxy the API prefix:

``` typescript
server: {
  proxy: { '/api': 'http://localhost:5000' },
},
```

and set the axios base URL to the relative `/api`:

``` typescript
const api = axios.create({ baseURL: '/api' });
```

The backend still keeps `cors()` but restricted to the
configured `FRONTEND_URL`:

``` typescript
app.use(cors({ origin: process.env.FRONTEND_URL }));
```

**Because** a shared same-origin path works in production
and in dev, and CORS becomes a belt-and-suspenders
control rather than the thing that makes the app work.

## Files touched

+ `code/frontend/vite.config.ts`
+ `code/frontend/src/services/api.ts`
+ `code/backend/src/index.ts`
+ `code/backend/.env` (`FRONTEND_URL`)