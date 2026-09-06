# Week 2 : Stale cache after login/role switch

## Error:

After signing out and signing back in as a *different
role*, the dashboard still showed the previous role's
data for a few seconds (or until a manual refresh).

## Relevant Context

The frontend uses TanStack Query.  The initial query
configuration was:

``` typescript
new QueryClient({
  defaultOptions: {
    queries: {
      retry: 1,
      refetchOnWindowFocus: false,
      staleTime: 30_000,
    },
  },
});
```

A 30-second `staleTime` means queries are served from
cache without refetching.  When the user changes, the
previous user's data is still in the cache under the same
query keys (e.g. `["dashboard"]`, `["events"]`), so the
wrong content flashes on screen.

## Key Observation

Query keys were scoped to *features*, not to the *user*.
The `staleTime` we added to reduce network churn
inadvertently cached identity-specific data across users.
The cache has no idea that a different user is now in
charge of the same key.

## Solution

Two changes together:

1. **Namespace keys by user**: include the user id (or
   role) in every protected query key, e.g.
   `["dashboard", user.id]`.
2. **Clear on identity change**: on login/logout, remove
   all queries so nothing crosses the boundary:

``` typescript
async function afterAuthChange(queryClient: QueryClient) {
  queryClient.removeQueries();
}
```

We kept a small `staleTime` for the public landing
queries (safe to reuse) but removed it for role-scoped
queries.

**Because** cached data is only valid as long as the
identity that produced it is still the current identity;
changing users must invalidate everything at once.

## Files touched

+ `code/frontend/src/store/authContext.tsx`
+ `code/frontend/src/App.tsx` (QueryClient config)
+ `code/frontend/src/pages/student/*` (query keys)
+ `code/frontend/src/pages/society/*`
+ `code/frontend/src/pages/admin/*`