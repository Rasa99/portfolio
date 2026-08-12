# Rasa Salih — Portfolio

Live at **https://rasasalih.vercel.app**

3D modeler and CAD drafter — armored vehicle design, CNC part nesting, and DXF preparation for laser and plasma cutting.

## This repo is the website

Every file here is served as-is. There is no build step on Vercel, no framework, no dependencies. Pushing to `main` redeploys the live site automatically.

The site is **flat on purpose** — every asset sits in the root rather than in `crops/`, `uploads/` and `_ds/`. That started as a requirement of Vercel's dashboard upload (which flattens filenames) and was kept because it removes a whole category of broken-path problems.

## Updating it

The HTML is generated from a Claude Design project, not edited by hand. Edit the design, then rebuild:

```bash
python "../Relocation Job Search/build_portfolio.py"
```

Then publish:

```bash
git add -A && git commit -m "update" && git push
```

Vercel picks it up from there. See [DEPLOY.md](DEPLOY.md) for the full setup, including what to change if the site's address ever changes.

## What the build script guards against

It refuses to build if any of these break, because each one has actually happened:

- **Fusion 360 appears anywhere.** The design file listed it three times; it is not a tool I claim.
- **Unresolved `{{ }}` bindings survive.** The source is a `.dc.html` needing a runtime — a stray binding would render as literal text on the page.
- **A nested asset path survives** the flat build (would 404).
- **Two images collide** on filename once flattened (one would silently overwrite the other).

The scroll-reveal also carries an unconditional 2-second fallback, so if `IntersectionObserver` ever fails the page degrades to "no animation" rather than "no content".
