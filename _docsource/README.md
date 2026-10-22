> **Built-site push: read this first.** This branch's root is the site itself,
> already built by Doc Writer as plain static files. To publish it, in the repository's
> Settings, Pages, "Build and deployment", choose "Deploy from a branch", branch
> `gh-pages`, folder `/ (root)`. No workflow and no Node.js are needed.
>
> This folder, `_docsource/`, holds the same pages as Markdown with a Docusaurus project,
> for anyone who wants to build the site themselves. The text below was written for that
> project at the branch root: run its commands inside `_docsource/` (`cd _docsource`), and
> in the example workflow give the build job
> `defaults: { run: { working-directory: _docsource } }` and upload `_docsource/build`
> instead of `build`. Publishing such a build replaces the site Doc Writer built.

# Team docs site (generated)

This branch is written by Gladiator's Doc Writer when someone presses
"Publish to GitHub Pages". Do not edit it by hand: the next push is refused if
the branch head is not a commit Doc Writer recorded. Never run
`create-docusaurus` over this tree: it is already a complete Docusaurus project.

- `docs/` - the pages (Markdown with Docusaurus frontmatter)
- `static/` - screenshots, served from the site root (`/screenshots/...`)
- `sidebar-items.json` - the navigation, grouped by page type
- `llms.txt`, `llms-full.txt` - the pages for language models
- `package.json`, `docusaurus.config.js`, `sidebars.js`, `src/css/custom.css`,
  `.gitignore` - the build files, the same for every repository. The site address
  is worked out at build time from `GITHUB_REPOSITORY` (`/` for an
  `<owner>.github.io` repository, `/<repository>/` otherwise).

## Publish the site

1. Install Node.js 22 or later. To preview locally: `npm install && npm start`.
   `npm start` (the dev server) does not show the generated landing redirect; to see the
   site as published use `npm run build && npm run serve`.
2. In the repository's Settings, Pages, "Build and deployment", choose
   "GitHub Actions" as the Source.
3. Add a workflow to your DEFAULT branch (Doc Writer never pushes workflow files),
   for example `.github/workflows/team-docs-pages.yml`, and run it from the
   Actions tab after each publish. It must use `workflow_dispatch`: a workflow
   triggered by a push to `gh-pages` would never run, because this branch holds no
   workflow file.

```yaml
name: team-docs-pages
on: workflow_dispatch
permissions: { contents: read, pages: write, id-token: write }
concurrency: { group: pages, cancel-in-progress: false }
jobs:
  build:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@v7
        with: { ref: gh-pages }
      - uses: actions/setup-node@v6
        with: { node-version: 22 }
      - run: npm install --no-audit --no-fund
      - run: |
          mkdir -p static
          for f in llms.txt llms-full.txt; do
            if [ -f "$f" ]; then cp "$f" static/; fi
          done
      - run: npm run build
      - uses: actions/upload-pages-artifact@v5
        with: { path: build }
  deploy:
    needs: build
    runs-on: ubuntu-24.04
    environment:
      name: github-pages
      url: ${{ steps.d.outputs.page_url }}
    steps:
      - id: d
        uses: actions/deploy-pages@v5
```

The `run: |` step before the build serves `llms.txt` and `llms-full.txt` from the site
root; leave it out if you do not want them on the site.

The jobs pin `ubuntu-24.04` because the `latest` runner label moves to a newer image on
GitHub's schedule; this example was checked on 24.04.
