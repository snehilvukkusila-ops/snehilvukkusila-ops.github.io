# Team docs site (generated)

Gladiator's Doc Writer writes this branch when someone presses "Publish to GitHub
Pages". The site at the root of the branch is ready to serve as it is: there is
nothing to install, build or run. Do not edit the branch by hand: the next push
is refused if the branch head is not a commit Doc Writer recorded. Other folders
on this branch, if there are any, are written by Doc Writer too.

## Turn the site on (first time only)

1. In this repository on GitHub, open Settings, then Pages.
2. Under "Build and deployment", set Source to "Deploy from a branch".
3. Set Branch to `gh-pages` and the folder to `/ (root)`, then select Save.

After that, every push from Doc Writer updates the site by itself.

## The site's files

- `index.html` and one `.html` file per page
- `assets/` - the style sheet and the theme toggle script
- `screenshots/` - the screenshots the pages show
- `llms.txt`, `llms-full.txt` - the pages for language models
- `404.html`, `.nojekyll` - for GitHub Pages
