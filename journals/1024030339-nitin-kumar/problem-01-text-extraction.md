# Problem 1 : Text extraction problem

## Problem

A PDF does not always contain text in a simple, clean
format.  It can contain:

+ paragraphs
+ headings
+ tables
+ lists
+ headers / footers
+ images
+ scanned pages

If we extract everything as plain text, we can lose the
structure of the document.

## How we fixed it

We used **Docling** for document structure extraction.

It supports:

+ PDF
+ DOCX
+ TXT
+ Markdown

Docling extracts the document while maintaining structural
information such as:

+ Heading
+ Paragraph
+ Table
+ List
+ Footer

Docling is used for structure extraction from
PDF / DOCX / TXT / Markdown.

## Viva answer

"One problem we faced was extracting text while preserving
the document structure.  If we simply extracted plain
text, information such as headings, tables and lists could
lose their structure.  We solved this by using Docling,
which performs structure-aware document extraction."