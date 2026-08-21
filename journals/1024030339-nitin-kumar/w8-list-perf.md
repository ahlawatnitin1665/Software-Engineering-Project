# Week 8 : Search box freezes when the event feed grows

## Error:

As seeded event data grew (hundreds of cards), typing in
the search box made the page visibly stutter -- keystrokes
lagged, and every character also fired a network request.

> debounce: 200ms (added) still re-fetched per stable query
> filtered render of the full list blocked the input frame

## Relevant Context

The public events feed renders every `EventCard` in one
flat list.  Search is a controlled `<input>`; on each
change we:

1. updated a `useState` filter, which re-filtered a large
   array **synchronously inside render**, and
2. fed a `["events", { search }]` query key, so the
   *network query* re-ran on every settled keystroke.

Both costs ran in the same event loop tick as the keypress,
so the input couldn't paint between them.

## Key Observation

Two different problems were stacked on one keystroke:

* **Render cost:** filtering hundreds of objects and
  re-rendering all their cards is expensive work to do
  *on the critical path* of the text input.
* **Network cost:** tying the query key to the live search
  string turns typing into a per-key word backend call,
  even when the user is still mid-word.

The input needs to stay instant; the expensive filter and
the network call should happen *after* typing has settled.

## Solution

1. Debounce the network side by keeping the settled-search
   out of the query key until the user pauses (or use
   `placeholderData: keepPreviousData`).
2. Keep the live filtering cheap on the render path with
   `useDeferredValue`, so React renders the text field
   first and does the list computation at idle priority:

``` tsx
const deferredSearch = useDeferredValue(search);
const filtered = useMemo(
  () => events.filter((e) =>
    e.title.toLowerCase().includes(deferredSearch.toLowerCase())),
  [events, deferredSearch],
);
```

If the list still thrashes with thousands of rows, virtualise
the renderer instead of rendering every card.

**Because** keystroke latency is bounded by the heaviest
work scheduled on that frame -- deferring the filter and
throttling the query moves both off the input's critical
path.

## Files touched

+ `code/frontend/src/pages/public/EventsFeed.tsx`
+ `code/frontend/src/hooks/useEvents.ts` (keepPreviousData)
+ `code/frontend/src/components/EventCard.tsx`