# Problem 2 : Scanned PDF problem

## Problem

Some PDFs are not actually made of selectable text.

For example, a scanned document may basically be:

```
PDF
 ↓
Image of page
 ↓
No actual text layer
```

So normal text extraction cannot read it.

## How we fixed it

We used **OCR** as a fallback.

Tesseract / PaddleOCR are used for scanned documents.

The flow becomes:

```
              PDF
               ↓
            Docling
               ↓
        Is text available?
          ↙          ↘
        YES           NO
         ↓             ↓
   Extract text       OCR
                       ↓
                Tesseract /
                PaddleOCR
```

## Viva answer

"Another problem was scanned PDFs.  In a scanned PDF, the
page may be an image rather than actual selectable text.
In that case, normal extraction doesn't work.  We solved
this by using OCR as a fallback, specifically Tesseract or
PaddleOCR, to recognize the text from the scanned page."