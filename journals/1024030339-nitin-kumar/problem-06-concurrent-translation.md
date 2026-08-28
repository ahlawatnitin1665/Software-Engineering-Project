# Problem 6 : Translation takes too long

## Problem

Suppose there are 100 chunks.

Sequential processing:

```
Chunk 1 → API
          ↓
Chunk 2 → API
          ↓
Chunk 3 → API
          ↓
...
```

This can be slow.

## How we fixed it

We use **concurrent translation**.

The project uses Python `asyncio` + `Semaphore` with a
maximum of 8 simultaneous requests.

```
             Translation Queue
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
    Chunk 1       Chunk 2      Chunk 3
       ↓            ↓            ↓
      API          API          API

up to 8 requests at once
```

## Viva answer

"Translation could become slow if every chunk was
translated sequentially.  We solved this using
asynchronous processing with an asyncio semaphore, which
allows up to 8 translation requests to run concurrently
while preventing too many requests from being sent to the
API."