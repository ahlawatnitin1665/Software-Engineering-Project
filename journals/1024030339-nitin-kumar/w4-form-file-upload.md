# Week 4 : Event cover image never reaches the backend

## Error:

Creating an event with a cover image "worked" but the
uploaded file never arrived -- `req.file` was always
`undefined` server-side.

> POST /api/society/events  200
> { coverImage: undefined, title: "Face-off", ... }
>
> multer: "MulterError: Unexpected field" (when named)

## Relevant Context

The event form is built with `react-hook-form` + a `zod`
schema.  The cover image is selected through a hidden
`<input type="file">`, stored in form state, and submitted
together with the text fields.  On submit we handed the
resolved form values straight to axios:

``` tsx
// before
onSubmit: (values) => {
  await api.post("/society/events", values);
}
```

## Key Observation

axios serialises a plain JavaScript object as
`Content-Type: application/json`, and **multer only ever
parses `multipart/form-data`**.  So even though the file
was sitting in the form state, the JSON body had no file
and no real binary stream.

Two traps compounded it:

1. The file never made it out of form state and into the
   body (`JSON.stringify` drops `File` objects into `{}`).
2. Trying to "force" multipart by setting the `Content-Type`
   header manually breaks the multipart *boundary* -- the
   boundary must come from the browser, not from us.

## Solution

Stop passing resolved values directly.  Build the
`FormData` explicitly, append every field (converted from
form state), and let axios set the multipart boundary:

``` tsx
// after
onSubmit: ({ title, date, coverImage }) => {
  const fd = new FormData();
  fd.append("title", title);
  fd.append("date", date);
  if (coverImage) fd.append("coverImage", coverImage); // File
  return api.post("/society/events", fd); // no Content-Type header!
}
```

The backend zod schema validates the *text* fields on the
parsed body, and multer (`upload.single("coverImage")`)
handles the file separately.

**Because** a JSON body cannot carry a binary file, and a
hand-written `Content-Type` strips the boundary the server
needs to split fields -- server and client must agree on
multipart, and only the browser can produce it correctly.

## Files touched

+ `code/frontend/src/pages/society/CreateEventForm.tsx`
+ `code/frontend/src/services/api.ts` (no manual header)
+ `code/backend/src/modules/society/events.controller.ts`