# Problem 4 : Finding the original position of text

## Problem

This is very important for the project.

Suppose the PDF has:

```
             Original PDF

       ┌────────────────────┐
       │     HEADING        │
       │                    │
       │ Paragraph here     │
       │                    │
       │              IMAGE │
       │                    │
       │       TABLE        │
       └────────────────────┘
```

After translation, we need to know:

Where was each piece of text originally located?

Otherwise, we cannot put the translated text back in the
correct location.

## How we fixed it

We used **PyMuPDF (fitz)** for PDF layout analysis.

It extracts things such as:

+ bounding box
+ page number
+ font
+ alignment
+ background box

PyMuPDF is used for bounding-box, background-box, font and
alignment extraction.

### What is a bounding box?

A bounding box tells us the rectangular region occupied by
an element.

For example:

```
(x1, y1) ─────────────── (x2, y1)
   │                         │
   │       Paragraph         │
   │                         │
(x1, y2) ─────────────── (x2, y2)
```

## Viva answer

"After extracting the text, we needed to know where each
text element was located in the original PDF.  We solved
this using PyMuPDF, which helps us extract the bounding
box, page number, font and alignment of the elements.
This layout information can later be used for
reconstruction."