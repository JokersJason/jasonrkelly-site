# JasonRKelly.com static website

This package is a no-build static website.

## Files

- `index.html` — page content and structure
- `assets/styles.css` — all visual styling
- `assets/site.js` — mobile navigation and current-year footer

## Publish

Upload the contents of this folder to the document root for the site or staging subdomain.

For the current staging setup, that means the contents should ultimately be placed in the document root used by:

`builder.jasonrkelly.com`

The browser should be able to reach `index.html` directly.

## Local preview

From PowerShell inside this folder:

```powershell
python -m http.server 8080
```

Then open:

`http://localhost:8080`

You can also simply open `index.html`, though a local web server more closely matches production behavior.

## Before production

Review these items in `index.html`:

1. Confirm the public contact email.
2. Confirm the GitHub profile URL.
3. Decide whether to keep the project names/descriptions public.
4. Add a professional photo later if desired.
5. Add project-specific links once those pages or repositories are ready.

No framework, package manager, PHP, database, or build process is required.
