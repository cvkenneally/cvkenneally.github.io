# Connor Kenneally — Personal Site

Personal site and resume, built with Vue 3 + Vite and deployed via GitHub Pages.

## Local development

```bash
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173).

## Build

```bash
npm run build
```

Output is in `dist/`. Preview the production build locally:

```bash
npm run preview
```

## Deployment (GitHub Pages)

The site is deployed automatically when you push to `main` using GitHub Actions. Ensure **Pages** is enabled in the repo:

1. **Settings → Pages**
2. Under **Build and deployment**, set **Source** to **GitHub Actions**.

After the workflow runs, the site is available at **https://cvkenneally.github.io**.

**If the live page is blank:** GitHub is probably serving the repo source instead of the built site. In **Settings → Pages**, set **Source** to **GitHub Actions** (not "Deploy from a branch"). The workflow builds the app and uploads the `dist/` folder; only that artifact should be used for the site.

## Editing resume content

Update `src/data/resume.js` to change your name, contact info, experience, education, and skills. The rest of the site will reflect those changes.
