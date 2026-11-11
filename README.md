# Test apps for comparing documentation tools

Five small web apps hosted as static pages on GitHub Pages at
https://snehilvukkusila-ops.github.io/ so that documentation tools can be pointed at a public URL.
Only synthetic probe apps and two open-source apps (MIT) are here. There are no keys, no answer files and no secrets.

| App | Base URL | Kind | Routes |
|---|---|---|---|
| Probe Pages | `/probe-pages/` | synthetic | 10 |
| Probe Fields | `/probe-fields/tasks` (root `/probe-fields/` is a stub) | synthetic | 4 (+ root stub) |
| Probe Guide | `/probe-guide/goals` (root `/probe-guide/` is a stub) | synthetic | 4 (+ root stub) |
| shadcn-admin | `/shadcn-admin/` | open source, MIT | 31 |
| TailAdmin React | `/tailadmin/` | open source, MIT | 17 |

## How each part was made

### Probe apps (`probe-pages`, `probe-fields`, `probe-guide`)
Each is a set of static HTML pages. They were mirrored page by page from a local run of the fixture server
(`serve.py`, `--profile healthy`), saved at `<app>/<route>/index.html`. The pages use absolute links such as
`/probe-pages/team`, which work because this site is published at the domain root. Each page's headings, nav, buttons and
form fields were compared with the live server using Playwright and are identical.

### shadcn-admin (upstream: https://github.com/satnaing/shadcn-admin, MIT, see `shadcn-admin/LICENSE`)
Built from the upstream source in a temporary copy with `npm install --force`, then
`npx vite build --base=/shadcn-admin/`. One patch to `src/main.tsx` so TanStack Router knows the sub-path:

```diff
 const router = createRouter({
   routeTree,
   context: { queryClient },
   defaultPreload: 'intent',
   defaultPreloadStaleTime: 0,
+  basepath: '/shadcn-admin',
 })
```

### TailAdmin React (upstream: https://github.com/TailAdmin/free-react-tailwind-admin-dashboard, MIT, see `tailadmin/LICENSE.md`)
Built from the upstream source in a temporary copy with `npm install --force`, then `npx vite build --base=/tailadmin/`. Patches:

```diff
-    <BrowserRouter>
+    <BrowserRouter basename="/tailadmin">
```

and, after the build, one text replacement in the built JavaScript bundle: the quoted asset paths
`"/images/`, `'/images/` and `` `/images/ `` became the same with `/tailadmin/images/`. The source hard-codes those
paths as strings that Vite does not rewrite. The apps behave the same; only the paths are prefixed.

### Deep links on GitHub Pages
GitHub Pages answers 404 for unknown paths and has no SPA fallback. So a copy of the built `index.html` is stored at
`<app>/<route>/index.html` for every known route of both single-page apps (listed in `spa-routes.json`), so deep links
return HTTP 200. Anything else returns the root `404.html`.

## Differences from the original apps
- Everything lives under a path prefix (`/shadcn-admin/`, `/tailadmin/`).
- Probe apps: the live server answers every declared route with the same static page, so nothing changes except the
  server. Forms are plain HTML with no submit target. The two `GET /api/goals` and `/api/goals/owners` calls that the Probe Guide pages fire are snapshots of the server's
  `{"ok": true}` replies, stored at `/api/goals/` and `/api/goals/owners/` at the site root (so absolute `/api/...` paths work);
  they are served as `text/html`. `/login` is not mirrored (no page calls it). The catch-all route `/*` is mirrored at `/probe-pages/not-found/`; other unknown paths return a real 404.
  `/probe-fields/` and `/probe-guide/` answer 404 on the live server; here they are small stub pages.
- Extensionless links (`/probe-pages/team`) get a 301 to the trailing-slash form from GitHub Pages.
- shadcn-admin: `/terms` is not a route of the app; it was seen by the crawler and renders the app's own not-found page (still HTTP 200 here).
- shadcn-admin: no Clerk key is set, so the `/clerk/*` pages show the app's "Set your Clerk API key" screen. Some avatar
  images the upstream app references at `/avatars/...` are not in its `public/` folder and 404, as upstream.
- TailAdmin: the Videos page embeds YouTube, which may be blocked or unavailable.
