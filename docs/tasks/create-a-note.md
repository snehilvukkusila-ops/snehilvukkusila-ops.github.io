---
title: "Create a note"
description: "Write and save a new note."
audience: end-user
---

# Create a note

1. Press **New note**.
2. Type a title, then the body.
3. Press **Save**. The note is saved with the id `\{note_id\}` shown in the address bar.

![The new note form](/screenshots/create/1.png)

To call the API, send the header `Authorization: Bearer <QUILLPOST_TOKEN>` where <QUILLPOST_TOKEN> is your own placeholder value.

```bash
curl -H 'Authorization: Bearer <QUILLPOST_TOKEN>' https://quillpost.example.invalid/notes/{note_id}
```
