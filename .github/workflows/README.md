This folder contains GitHub Actions workflows for the Hugo site.

The `deploy.yml` workflow builds the site and deploys `public/` to the `gh-pages` branch using `peaceiris/actions-gh-pages`.

To enable deployment:
1. Create a deploy key or set up a PAT with repo permissions.
2. Add the private deploy key to the repository secrets as `DEPLOY_KEY` (or configure `GITHUB_TOKEN` and adjust the workflow to use it).
3. Push to `main`—the workflow will build and publish to `gh-pages`.
