# JVA Music — Lessons Page

A static, single-file landing page for drum lessons with Jacobo Vega-Albela. Designed to live on GitHub Pages and be embedded into [jva-music.com/lessons](https://www.jva-music.com/lessons) via an iframe.

Visual identity is shared with the [EPK site](https://jvegaalbela-png.github.io/website/) (same color tokens, same `Cormorant Garamond` + `Inter` type pairing) so the two pages read as siblings.

---

## Local preview

No build step. Two options:

```bash
# Option 1: just open it
open index.html

# Option 2: serve over http (better for testing the iframe height postMessage)
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploying to GitHub Pages

1. Create a public GitHub repo (e.g. `lessons`).
2. Push `main`:
   ```bash
   git remote add origin git@github.com:USERNAME/REPO.git
   git push -u origin main
   ```
3. In repo **Settings → Pages**, set **Source: Deploy from branch**, **Branch: main**, **Folder: /** (root).
4. Wait ~1 minute. Page will be live at `https://USERNAME.github.io/REPO/`.

## Embedding in Squarespace

> **Note:** iframes inside Squarespace **Code Blocks** require a Business plan or higher.

Add a **Code Block** to the `/lessons` page on Squarespace and paste the snippet below. Replace `USERNAME/REPO` with your actual GitHub Pages path. The parent-side script listens for `postMessage` events from the iframe and resizes it so there's no scroll-within-a-scroll.

```html
<iframe id="jva-lessons"
        src="https://USERNAME.github.io/REPO/"
        style="width:100%; border:0; display:block;"
        loading="lazy"
        title="Drum Lessons"></iframe>
<script>
  window.addEventListener('message', function(e) {
    if (e.data && e.data.type === 'jva-iframe-height') {
      document.getElementById('jva-lessons').style.height = e.data.height + 'px';
    }
  });
</script>
```

The iframe page posts `{ type: 'jva-iframe-height', height: <number> }` on load, on resize, after fonts load, and whenever a `<details>` (FAQ) toggles.

## Customization

Everything you'll typically want to tweak lives in two places at the top of `index.html`:

1. **CSS custom properties** — the `:root` block defines every color, font, max-width, and section padding. Change `--orange` to recolor primary CTAs, `--cobalt` to recolor links, `--cream` to change the page background. They're the same tokens used on the EPK site, so visual parity is automatic.

2. **`PRIMARY_CTA_URL`** — defined once at the top of the `<script>` near the bottom of `index.html`. Every primary CTA on the page has a `data-cta` attribute and gets its `href` set from this constant on page load. To swap from the prefilled `mailto:` to a Tally form, Calendly link, or Formspree endpoint, change just that one string. The HTML hrefs include the same mailto as a no-JS fallback, so update those too if you keep JS-disabled support as a hard requirement.

Contact info (email, phone) is hardcoded in the hero, footer, and `tel:` link — search for `jacobovamusic@gmail.com` and `5858024247` to find both.

## Notes on iframe behavior

- No internal nav header — Squarespace already has one.
- No `position: sticky` or `position: fixed` — they bind to the iframe viewport, not the parent page.
- All outbound links use `target="_blank" rel="noopener"`.
- `overflow-x: hidden` on `body` as a safety net against horizontal scroll on narrow viewports.
- Hero `min-height` is content-driven (no `vh` units that would feed back into the auto-resize loop).
- Page is fully usable with JS disabled. The only thing JS adds is the auto-resize message and the footer year.

## Files

```
index.html       single-file landing page (HTML + inline CSS + vanilla JS)
assets/check.svg standalone copy of the credibility-chip checkmark (also inlined in the hero)
README.md        this file
```
