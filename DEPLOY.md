# Deploying this site

**Live at: https://rasasalih.vercel.app**

Deployed 2026-08-11 to your Vercel account (`rasa99`, team `ras-ben`) as the project **`rasasalih`**, by direct file upload — no GitHub involved.

## How it got there, and how to update it

Vercel's **https://vercel.com/new** page accepts a plain file upload ("drag and drop your project, or choose a file or a folder"). That's the whole deployment path. No CLI, no Node, no GitHub repo, no OAuth.

**This is why the site is flat.** Every asset sits in the root — no `crops/`, no `uploads/`, no `_ds/`. Vercel's upload input has no `webkitdirectory`, so files arrive as bare filenames and any nested path would 404. `build_portfolio.py` enforces this and fails the build if a subdirectory reference survives.

### To publish a change

1. Edit the design in Claude Design, then rebuild:

```bash
python "../Relocation Job Search/build_portfolio.py"
```

2. Go to https://vercel.com/rasasalih and drop the **contents** of this folder onto a new deployment — or open https://vercel.com/new and choose the files.

Select the files themselves, not the folder, and leave out `DEPLOY.md` and `.gitignore`; they aren't part of the site.

## Optional: connect GitHub for automatic deploys

Not required — the site is live without it. But pushing to GitHub and linking the repo in Vercel means every `git push` republishes automatically, which is easier than re-uploading.

This folder is already a git repo with the site committed. To connect it:

```bash
git remote add origin https://github.com/YOUR-USERNAME/portfolio.git
git branch -M main
git push -u origin main
```

Then Vercel → project `rasasalih` → Settings → Git → Connect. You'll need to be signed in to GitHub (you weren't, on this machine).

## A custom domain, later

`rasasalih.vercel.app` is free and perfectly respectable. If you ever want `rasasalih.com` (~$10–15/year), buy it anywhere, then Vercel → project → Settings → Domains → Add.

**If you change the address, one thing must be updated:** the canonical URL, social-preview links and sitemap all have `https://rasasalih.vercel.app` baked in. Change `SITE_URL` at the top of `../Relocation Job Search/build_portfolio.py`, rebuild, redeploy. Skip it and WhatsApp/email link previews point at the wrong place — exactly when someone is deciding whether to click.

## What the build guards against

`build_portfolio.py` refuses to build if any of these break:

- **Fusion 360 appears anywhere.** It was in the design file three times. It is never claimed.
- **Unresolved `{{ }}` bindings** survive — the design file is a `.dc.html` needing a runtime, and a stray binding would render as literal text on the page.
- **A nested asset path** survives the flat build (would 404 on upload).
- **Two images share a filename** once flattened (one would silently overwrite the other).

## Verified live

13 images loading, no broken links · design-system CSS applied · all 25 scroll-reveal sections showing · `og-image.jpg` 1200×630 · `robots.txt`, `sitemap.xml`, `styles.css` all 200 · no Fusion 360 anywhere · no horizontal scroll on mobile.
