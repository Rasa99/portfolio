# Deploying this site

**Live at: https://rasasalih.vercel.app**

Deployed 2026-08-11 to your Vercel account (`rasa99`, team `ras-ben`) as the project **`rasasalih`**, by direct file upload — no GitHub involved.

## Updating it — the short version

**GitHub is connected. Publishing is now one command.**

```bash
git add -A && git commit -m "update" && git push
```

That's it. Vercel rebuilds and republishes automatically — verified working (commit `b10a853` auto-deployed to production on push).

Full loop after a design change:

```bash
python "../Relocation Job Search/build_portfolio.py"
git add -A && git commit -m "update" && git push
```

**Setup that's already done, so you never repeat it:**
- Repo: **https://github.com/Rasa99/portfolio** (public)
- Auth: an **SSH key** on this machine (`~/.ssh/id_ed25519`), public half registered on your GitHub account as *"Work PC - portfolio deploys"*. No password or token is stored anywhere. Revoke anytime at https://github.com/settings/keys.
- Vercel's GitHub app is installed and **scoped to the `portfolio` repo only** — not all your repositories.
- Vercel project `rasasalih` is linked to `Rasa99/portfolio`, branch `main`.

On a **new machine**, generate a fresh key and add it the same way — never copy the private key between machines:

```bash
ssh-keygen -t ed25519 -C "rasasalih99@gmail.com" -f ~/.ssh/id_ed25519 -N ""
```

Then paste `~/.ssh/id_ed25519.pub` into https://github.com/settings/ssh/new.

---

## The old manual route (no longer needed)

Vercel's **https://vercel.com/new** page accepts a plain file upload ("drag and drop your project, or choose a file or a folder"). That's the whole deployment path. No CLI, no Node, no GitHub repo, no OAuth.

**This is why the site is flat.** Every asset sits in the root — no `crops/`, no `uploads/`, no `_ds/`. Vercel's upload input has no `webkitdirectory`, so files arrive as bare filenames and any nested path would 404. `build_portfolio.py` enforces this and fails the build if a subdirectory reference survives.

### To publish a change

1. Edit the design in Claude Design, then rebuild:

```bash
python "../Relocation Job Search/build_portfolio.py"
```

2. Open https://vercel.com/new, choose the files, name the project, Deploy.

Select the files themselves, not the folder, and leave out `DEPLOY.md` and `.gitignore`; they aren't part of the site.

**⚠ The upload flow only creates NEW projects — it cannot update an existing one.** Deploying again with the name `rasasalih` fails with *"Project already exists"*, and there is no upload option inside a project's dashboard. Updating the live site therefore takes this dance:

1. Deploy the new files under a temporary name (e.g. `rasasalih-tmp`), and check it works at its own URL first.
2. Old project → Settings → Domains → **Edit** on `rasasalih.vercel.app` → **Remove**. (Renaming the project does *not* release this domain, and editing it just adds a second one — it has to be removed.)
3. New project → Settings → Domains → **Add Existing** → `rasasalih.vercel.app`.
4. Confirm https://rasasalih.vercel.app serves the new build, then delete the old project.

**Connecting GitHub (below) removes all of this** — `git push` would just republish. Worth 10 minutes next time you're at a keyboard.

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

## Mobile

The design file was authored desktop-first and shipped with **no breakpoints for the hero, the stats bar or the work cards**. On a phone the hero's image column held a hard 280px floor, which starved the text column down to ~31px and wrapped the headline to about one word per line. `build_portfolio.py` now injects a `MOBILE_CSS` block that:

- stacks the hero below 900px (the actual bug),
- drops the stats bar to 2×2 below 800px (iPad portrait is exactly 768px),
- makes work cards single-column below 760px and normalises the alternating image order,
- trims desktop section padding, which otherwise wastes a screenful on a phone,
- hides the nav links below 700px with `!important` — **the design file sets `display:flex` inline on `.navlinks`, which beat its own `display:none` media rule and pushed the Contact button ~70px off screen.**

Since this lives in the build script, re-running it after a design change reapplies everything.

## Verified live

13 images loading, no broken links · design-system CSS applied · all 25 scroll-reveal sections showing · `og-image.jpg` 1200×630 · `robots.txt`, `sitemap.xml`, `styles.css` all 200 · no Fusion 360 anywhere · **no horizontal overflow at 390px, 414px, 768px or desktop; headline renders on one line per phrase.**

*Measure overflow with `document.documentElement.clientWidth`, not `window.innerWidth` — innerWidth includes the scrollbar and reported "no overflow" while the Contact button was in fact 71px off screen.*
