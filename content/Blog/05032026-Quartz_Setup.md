---
title: Setting Up Quartz for Github Pages
date: 2025-09-15
tags:
  - blog
  - research
  - technology
  - website
  - github
---
This site is hosted on the Quartz v4 framework by jzhao. You too can setup your own digital garden by following the documentation on [Welcome to Quartz 4 | jzhao](https://quartz.jzhao.xyz/), or follow along with these steps for full setup.
1. `git clone https://github.com/jackyzha0/quartz.git`
2. `cd quartz`
3. `npm i`
4. `npm audit fix` - Remediate vulnerabilities, but may cause compatibility issues. I did not have issues.
5. `npx quartz create`
6. In `/quartz/.github/workflows/` add the file `deploy.yml` with the following contents:
   ```yaml
   name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
   ```
7. In your repository, head to "Settings" -> "Pages". In "Build and deployment", under "Source" select "GitHub Actions".
8. `git remote rm origin` - Remove the original repo `https://github.com/jackyzha0/quartz.git`
9. Create a Personal Access Token:
	1. Login to github.com
	2. Go to "Settings" -> "Developer settings" -> "Personal access tokens" -> "Fine-grained tokens"
	3. If you have already created a PAT for your Pages repository, regenerate it and note down the token. You can move on to step 9.
	4. Create a token with Repository access to your Github Pages repository.
	5. Revoke all permissions except for "**Read** access to codespaces metadata and metadata" and "**Read** and **Write** access to Dependabot alerts, actions, actions variables, administration, attestations api, code, codespaces, codespaces lifecycle admin, codespaces secrets, commit statuses, custom properties for repositories, dependabot secrets, deployments, discussions, environments, issues, merge queues, pages, pull requests, repository advisories, repository hooks, secret scanning alert dismissal requests, secret scanning alerts, secret scanning push protection bypass requests, secrets, security events, and workflows".
	6. Note down the generated token for the next step.
10. `git remote add origin https://USERNAME:PERSONALACCESSTOKEN@github.com/USERNAME/username.github.io` - Add your repository with your personal access token as the origin.
11. `npx quartz sync --no-pull` - Push your new site and workflow to your GitHub repository