# Week 1 : Dialog rendered unstyled

## Error:

The first UI components (a base-ui `Dialog` from the
shadcn/ui-style component set) rendered as a plain,
borderless white box -- no padding, no theme colours, and
the close button sitting on the raw content.

## Relevant Context

We scaffolded the design system with Tailwind CSS and the
base-ui primitives.  The `DialogContent` component mixes
theme tokens with utility classes:

``` typescript
className={cn(
  "fixed ... rounded-xl bg-popover p-4 text-sm ...",
  className
)}
```

The classes `bg-popover`, `text-popover-foreground` and
the ring colour are **not Tailwind utility classes** --
they are the names of CSS variables that the theme must
define.

## Key Observation

The tokens existed as names but had no values.  The theme
uses the following indirection:

``` css
--popover: hsl(var(--popover));        /* background */
--popover-foreground: ...
```

If `--popover` is never set, the browser's `transparent`
fallback wins and every dialog looks unstyled.  This is
exactly the failure we saw -- the *structure* rendered
fine, the *styling* was missing.

## Solution

Define the CSS variables in the theme layer so the token
names resolve to the campus palette:

``` css
:root {
  --background: 228 39% 96%;   /* ivory   */
  --foreground: 226 7% 14%;    /* near-navy */
  --popover: 228 39% 96%;
  --popover-foreground: 226 7% 14%;
  --primary: 245 100% 68%;     /* violet  */
  --coral: 8 100% 68%;         /* accent  */
}
```

and reference them through the Tailwind config token
extensions.  We also created small editorial tokens
(`ivory`, `navy`, `violet`, `coral`, `mint`, `lavender`)
so the rest of the UI can stay consistent.

**Because** a design-system variable is only useful if the
theme it points at actually exists; the palette must ship
with the component library.

## Files touched

+ `code/frontend/src/index.css` (`:root` variables)
+ `code/frontend/tailwind.config.*`
+ `code/frontend/src/components/ui/dialog.tsx`
+ `code/frontend/src/components/ui/button.tsx`