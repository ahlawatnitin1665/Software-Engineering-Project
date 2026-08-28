# Problem 5 : Keeping track of each piece of text

## Problem

Imagine:

```
Chunk 1 → Heading
Chunk 2 → Paragraph
Chunk 3 → Table
Chunk 4 → Paragraph
```

After translation, we need to know which translated text
belongs to which original location.

## How we fixed it

We assign a **unique chunk ID**.

For example:

```
Chunk ID: C001
Page: 1
Type: Heading
Position: (100, 80, 500, 120)
Text: "Introduction"
```

Then:

```
C001 → translated text
```

The same ID remains associated with the chunk through the
pipeline.  Maintaining the chunk ID from extraction through
rendering is essential for reconstruction.

## Viva answer

"To maintain traceability, every extracted chunk receives
a unique chunk ID.  This ID stays associated with the
chunk during processing, so we can later identify where
the translated chunk belongs in the original document."