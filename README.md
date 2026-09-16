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

## If it's not picking up the note

The app applies a high-pass + low-pass filter to cut rumble and reed buzz,
then an adjustable gain stage before analysis (the browser's own
auto-gain is deliberately left off, since it can fight the pitch
tracker). If detection still feels weak or you have to stand very close:

- Watch the **input level bar** under "Start tuning" while you play — it
  should sit comfortably past halfway. If it barely moves, raise
  **mic sensitivity** (1×–10×) until it does.
- Very reverberant or noisy rooms hurt any pitch detector — a quieter
  room or standing a bit closer than you'd like still helps even with
  gain turned up, since gain boosts noise along with signal.
- If you're on a laptop, an external/USB mic will usually track much
  better than a built-in one at a distance.

## Setting Low A

Tap **"Use current pitch"** while tuning, then play a steady Low A. It
gives you 5 seconds to get positioned and start playing, then samples
for 3 seconds and takes the median of what it heard — steadier than a
single instantaneous reading. You can cancel mid-countdown or
mid-sample by tapping the button again (it becomes "Cancel").

## Notes on the tuning intervals

The default per-note offsets (in the "Advanced" panel in-app) use a common
modern-convention just-intonation scale for the Highland pipe chanter:
harmonic sevenths (7/4, 7/8) for both Gs, a just fourth (4/3) for D, and so
on, referenced against your Low A. Real chanters, reeds, and personal taste
vary — every offset is editable in the app.

The Advanced panel also has an **intonation style** picker with four presets
(they differ mainly in how Low/High G and D are tuned — the other notes are
documented consistently across sources):

- **Modern** (default) — harmonic 7th Gs, just fourth D.
- **Just intonation** — both Gs on the simpler just minor seventh instead of
  the harmonic 7th; a gentler, less-flat G.
- **MacNeill** — from Seumas MacNeill's 1950s survey of working pipers; a
  notably *sharp* D and G, the opposite direction from the modern convention.
- **Equal temperament** — everything at 0¢, for cross-checking against an
  ordinary chromatic tuner.

Picking a style just loads its numbers into the editable rows below — you
can still hand-tweak any individual note afterward.

There's also a **minimum hold time** slider (default 300ms): a note has to
be the closest match continuously for that long before it's written into
the reference tables and the whole-chanter view. This keeps short or
misidentified blips — grace notes, transients while switching notes — from
skewing the average. The live gauge above still responds instantly; the
hold time only gates what gets *recorded*.

## Reference tone playback

Tap ▶ next to any note or drone in the tables to hear it played back — a
small additive synth (a few harmonics at falling amplitude) rather than a
plain sine, so it has a bit more character to tune against by ear. There's
a shared volume slider and a Stop button under the Drones table. Starting
the microphone (or tapping a different ▶) stops whatever's currently
playing. While a tone is playing, live detection pauses — otherwise the
mic could pick the tone up through the speaker and mistake it for an
actual note.

There's a checkbox in Advanced settings ("Show reference tone playback")
to hide the ▶ buttons, volume slider, and Stop button entirely if you'd
rather keep the tables simpler — the underlying tone engine is unused
either way until you turn it back on.

## Trend sparklines & CSV export

Each row in the Chanter/Drone tables now has a small trend graph showing
that note's recent readings over the session (throttled to roughly one
point every 400ms, capped at the last 40). Changing your Low A, the
intonation style, or an individual interval clears recorded readings and
sparklines, since old cents values were measured against a target that no
longer applies.

The temperature log has an **Export CSV** button once you've saved at
least one snapshot — one row per snapshot, oldest first, with a column per
note (blank where that note wasn't played during that snapshot). Good for
charting drift against temperature in a spreadsheet over a season.

## Whole-chanter tuning: adjustable "in tune" band

The Whole-chanter tuning card has a **Tuned band** slider (±1 to ±20
cents, default ±5) and a labeled scale (-40 to +40¢) above the rows. The
shaded green band on the scale and on each row's track shows exactly how
wide "in tune" currently is — widen it if you want a more forgiving read,
narrow it for a stricter one. This isn't cosmetic to just this card: it's
the same threshold used everywhere else in the app — the main gauge's
green zone, the color of the "Last" chips in the tables, and the trend
sparklines all use it too, so it stays one consistent definition of
"tuned" throughout.
