# Week 5 : Approved event still shows as "pending"

## Error:

A society admin approved an event proposal, the request
succeeded, and the table on screen did not change.  Both
the public event feed and the proposer's "my proposals"
list kept showing the old, pending state.

> PATCH /api/society/proposals/42/approve  200
> { "success": true }
>
> ...but the UI still rendered the pre-approval row.

## Relevant Context

Server state is managed with **TanStack Query**.  Three
pieces are involved:

+ `useQuery(["proposals"])` -- the moderation queue
+ `useQuery(["events"])` -- the public feed
+ `useMutation(["approve", id])` -- calls the PATCH

The mutation resolved fine, so the server really did
update the row -- the UI was just serving a cached
snapshot that predated the change.

## Key Observation

TanStack Query knows nothing about backend writes.  A
successful mutation does **not** auto-refetch the queries
that read the table it just changed.  The cache is marked
stale only when the default `staleTime` (0) expires and a
re-render/re-focus triggers a refetch -- so the update
appeared to "never happen" on screen.

The fix is to make the cache state follow the server
state *programmatically*: invalidate the affected queries
when the mutation succeeds.

## Solution

Invalidate every query key the server write can affect,
in the mutation's `onSuccess`:

``` tsx
// hooks/useApproveProposal.ts
const queryClient = useQueryClient();

const approve = useMutation({
  mutationFn: (id) => api.patch(`/society/proposals/${id}/approve`),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["proposals"] });
    queryClient.invalidateQueries({ queryKey: ["events"] });
  },
});
```

`invalidateQueries` marks the keys stale and triggers a
background refetch, so the moderation queue and the public
feed converge on the new server truth.  An optional
`optimisticUpdater` makes the row flip instantly instead of
after the refetch.

**Because** client caches only update when told to --
write-then-read consistency across two query keys is the
caller's responsibility, not the server's.

## Files touched

+ `code/frontend/src/hooks/useApproveProposal.ts`
+ `code/frontend/src/pages/society/ModerationQueue.tsx`
+ `code/frontend/src/services/events.ts`