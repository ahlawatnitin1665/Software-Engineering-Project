# Week 3 : Route guard kicks me out on page refresh

## Error:

After logging in and landing on a protected page, a single
hard refresh (F5) bounced straight back to `/login` -- even
though the JWT was still valid and unexpired.

> Navigating to `/student/dashboard` after refresh redirects
> to `/login`.  `localStorage` still has a valid token.

## Relevant Context

We added JWT auth in Week 2, so the router now wraps
protected pages in a guard component.  The token lives in
`localStorage`, but the *current user* (profile + role)
only exists in an in-memory auth store that is populated by
a `GET /auth/me` call fired once on app boot.

On refresh the in-memory store starts empty, and the guard
read `user == null` *synchronously* during the very first
render -- before the bootstrapped `/auth/me` request had
resolved.

## Key Observation

The guard was treating "still loading" the same as "not
logged in":

1. `store.user` is `null` at first render, so the guard
   decided "anonymous" and redirected.
2. The `/auth/me` bootstrap *would* have succeeded (valid
   token), but it completed too late to matter.
3. The same bug makes the whole app flash a login page on
   every load even for real sessions.

Authentication state has three phases (loading, authed,
anonymous), not two -- the guard was only handling the
last two.

## Solution

Make the guard wait for the session restore to finish
before it decides anything.

``` tsx
// router/RequireAuth.tsx
function RequireAuth({ allowRoles, children }: Props) {
  const { isReady, user } = useAuth();

  if (!isReady) return <PageLoader />;   // session restoring
  if (!user) return <Navigate to="/login" replace />;
  if (allowRoles && !allowRoles.includes(user.role))
    return <Navigate to="/" replace />;
  return children;
}
```

`isReady` flips to `true` only after the `/auth/me`
bootstrap resolves (or fails cleanly).  The guard renders a
loader in the meanwhile, so the redirect decision is always
made against the *final* session state.

**Because** a guard that redirects during async bootstrap
flags transient loading as a real logout -- the decision
must be deferred until the restore completes.

## Files touched

+ `code/frontend/src/auth/AuthProvider.tsx` (add `isReady`)
+ `code/frontend/src/router/RequireAuth.tsx`
+ `code/frontend/src/services/auth.ts`