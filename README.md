# Jekyll Website

Content for two GitHub repos, to publish to *two* websites, a dev and a prod GitHub Pages (GHP) website.

Commit only to the dev website repo. GitHub Actions (GHA) will be used when the dev website looks acceptable to copy the repo contents over to the prod website repo.

## Dev GHP website

This assumes your dev website is "dev.example.com".

### Dev website setup

1. Set up a CNAME record in your "example.com" zone so that "dev.example.com" points at `<ORG>.github.io`.
2. In the dev repo, create a file named `CNAME` whose contents are your CNAME entry, "dev.example.com".
3. In the dev repo Settings tab, under Pages, enable the repo for GitHub Pages. Enter "dev.example.com" for the Custom domain name, and under "Build and deployment," choose GitHub Actions. Do not create a new GitHub action, use `.github/workflows/jekyll.yml`.

### Updates to dev website

Every commit to the `main` branch will automatically run `.github/workflows/jekyll.yml`, which is the Action titled "Deploy Jekyll site to Pages". 

## Prod GHP website
### Prod website setup

1. If desired (and you likely do), create a DNS A Record for your domain apex, "example.com". Use the following values: 185.199.111.153, 185.199.109.153, 185.199.110.153, 185.199.108.153.
2. Create a DNS CNAME Record for "www.example.com" pointing at `<ORG>.github.io`.
3. In the prod repo, under the Settings tab and the Pages menu item, enable Pages. Use "GitHub Actions" for the Build and deployment source, but don't create a new GHA. We'll use `.github/workflows/manual.yml` to force push the dev repo's contents into this repo when we're ready to publish to prod. Enter "www.example.com" under the Custom domain.

### Updates to prod website

When the dev website looks good, you can manually run `.github/workflows/manual.yml`, the GHA named "Manually Update from Dev" found under the Actions tab in the prod repo. (It is also available in the dev repo, but don't run it there)  Once the GHA is complete, the new update to the main branch will automatically trigger the "Deploy Jekyll site to Pages" and the prod website should be updated.
