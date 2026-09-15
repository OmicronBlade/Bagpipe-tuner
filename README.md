# Pìob — Chanter & Drone Tuner

A client-side Great Highland Bagpipe tuner. Everything (pitch detection, note
matching, storage) runs in the browser — nothing is uploaded anywhere.

## Files

- `index.html` — the app (structure, styles, and all logic)
- `manifest.json` — makes it installable as a home-screen app
- `sw.js` — minimal offline cache, needed for a reliable PWA install
- `icon.svg` — app icon

Keep all four files together in the same folder.

## Running it — why this matters

Browsers only grant microphone access on a **secure context**: an `https://`
page, or `http://localhost`. Opening `index.html` directly from disk
(`file://…`) will *not* work in most browsers, and a plain `http://` address
will not work either.

**Quickest way to test locally:**

```bash
cd bagpipe-tuner
python3 -m http.server 8000
```

Then open `http://localhost:8000` in your browser — localhost counts as
secure, so the microphone will work.

**To use it on your phone / share it with your band:**, put the folder on
any static HTTPS host. Free options that work with zero config:

- **GitHub Pages** — push the folder to a repo, enable Pages in repo
  settings, done.
- **Netlify / Vercel** — drag-and-drop the folder onto their web dashboard.
- **Cloudflare Pages** — same idea, connect a repo or drag-and-drop.

Any of these gives you an `https://…` URL you can open on your phone and
install to the home screen.

## Permission behavior to expect

- The mic permission prompt only appears after you tap **"Start tuning"** —
  by design, browsers block sites from requesting the microphone on page
  load.
- **Safari (iOS/macOS)** shows a one-time system dialog. If you tap "Don't
  Allow", there's no second prompt — you have to go to
  **Settings → Safari → Microphone** (Safari browser) or
  **Settings → [app name]** (if installed to the home screen) and enable it
  manually.
- **Installing to the home screen** (Share → "Add to Home Screen") is
  optional but gives you a full-screen, app-like tuner. Microphone access
  generally works fine in installed iOS web apps, but it's worth testing
  after install — Apple's audio APIs have had version-to-version quirks in
  standalone PWAs. Keep the regular Safari tab as a fallback if you hit one.

## Notes on the tuning intervals

The default per-note offsets (in the "Advanced" panel in-app) use a common
modern-convention just-intonation scale for the Highland pipe chanter:
harmonic sevenths (7/4, 7/8) for both Gs, a just fourth (4/3) for D, and so
on, referenced against your Low A. Real chanters, reeds, and personal taste
vary — every offset is editable in the app, and there's a toggle to fall
back to plain equal temperament if you'd rather tune that way.
