# Problem 3 : How to divide the extracted document

## Problem

After extracting the text, another problem is:

How do we divide the document for translation?

A bad approach would be:

> Every 500 characters = one chunk

This can result in:

> Chunk 1: "The document contains several..."
>
> Chunk 2: "...important sections."

You could split sentences or structures incorrectly.

## How we fixed it

We created a **structure-aware chunking engine**.

Instead of fixed character lengths:

```
Heading      → Chunk 1
Paragraph    → Chunk 2
Paragraph    → Chunk 3
Table        → Chunk 4
List item    → Chunk 5
```

Each chunk receives a unique chunk ID.

## Viva answer

"We also faced the problem of how to divide the document
into translation units.  Instead of using fixed-length
chunks, we implemented structure-aware chunking.
Paragraphs, headings, list items and table blocks are
treated as separate chunks, and each chunk is given a
unique ID."