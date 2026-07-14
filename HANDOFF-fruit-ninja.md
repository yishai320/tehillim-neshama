# Handoff — "Fruit Ninja" Game

Everything you need to understand, host, rebuild, and update the Fruit Ninja
arcade game. Keep this file with the project.

**Live site (yours):** https://yishai320.github.io/Fruit-ninja-clone/
**Repo (yours):** https://github.com/yishai320/Fruit-ninja-clone

(The game's source also lives in the `fruit-ninja/` folder of the
`tehillim-neshama` repo, where it was developed.)

---

## 1. What this is

A single-page Fruit Ninja clone that runs entirely in the browser: swipe with a
mouse or finger to slice fruit as it arcs across the screen. It was turned into
a **PWA** (Progressive Web App), which is what lets it be installed on a phone
and packaged into an Android APK — exactly the same way as the Tehillim app.

Built-in features: five fruit types (watermelon, apple, banana, orange,
coconut) drawn as pure vector graphics, bombs (15% of spawns — slicing one ends
the game), a fading blade trail, juice-splatter particles, fruit halves that
fly apart with spin, combo bonuses for slicing 3+ fruits in one swipe, three
lives with "X" miss indicators, synthesized sound effects (no audio files),
start / game-over screens, and offline support. Your best score is remembered
between visits.

---

## 2. The files and what each does

These seven files are the whole game. `index.html` must always sit at the
**top level** (root) of wherever it is hosted.

| File | Purpose |
|---|---|
| `index.html` | The entire game — graphics, physics, and all the interactive logic, in one file. |
| `manifest.json` | Tells the phone/browser it's an installable app: name, icons, colors, fullscreen landscape. Required by PWABuilder. |
| `sw.js` | Service worker. Caches the game so it works offline after the first open. |
| `icon-192.png`, `icon-512.png` | App icons (sliced watermelon) used when installed. |
| `apple-touch-icon.png` | The icon iPhone uses for "Add to Home Screen". |
| `favicon-32.png` | The little browser-tab icon. |

The web package containing all of these is **`fruit-ninja-app.zip`**.

Unlike the Tehillim app, the game has **no external dependencies at all** — no
Google Fonts, no images, no audio files. Everything (fruits, bombs, the wood
background, even the sounds) is generated in code, so nothing can ever 404.

---

## 3. How the "packaging into a PWA" actually worked

Starting from the plain game `index.html`, three things were added to make it
an installable app:

1. **A web manifest** (`manifest.json`) linked from the HTML `<head>`, plus
   `<meta>` tags for theme color and Apple/Android home-screen support. It's set
   to `fullscreen` + `landscape`, which is what you want for a game.
2. **A service worker** (`sw.js`) registered by the page, which caches the app
   shell for offline use. (Registration is skipped when the file is opened
   directly from disk, so double-clicking `index.html` still just works.)
3. **Icons** in the sizes phones expect (192 and 512 px, plus the Apple touch
   icon and favicon), drawn with the same canvas-vector style as the game itself.

---

## 4. Hosting it (two options)

### Option A — its own repo / its own address (what you did)

The game is live in its own repo at
**https://yishai320.github.io/Fruit-ninja-clone/**. This is how it was set up,
and how to redo it if ever needed:

1. Create a new public repo (yours is `Fruit-ninja-clone`).
2. Unzip `fruit-ninja-app.zip` on your computer → you get the 7 files.
3. In the repo: **Add file ▸ Upload files**, then drag in **all 7 files
   themselves** (not the zip, not a folder). Confirm `index.html` is at the top
   level. Commit.
4. **Settings ▸ Pages** → Source: "Deploy from a branch" → Branch: **main**,
   folder **/(root)** → **Save**.
5. Wait about a minute. On that same Pages screen a box appears with your live
   link.

The same rule as before applies: **GitHub Pages does not unzip anything** —
upload the loose files, never the `.zip`. A **404** almost always means a zip
or a folder was uploaded instead of the files, or Pages wasn't enabled.

**Faster alternative — Netlify Drop:** unzip the package, drag the folder onto
https://app.netlify.com/drop, and you get a live `https://….netlify.app` link
instantly — no repo, no Pages settings.

### Option B — inside the tehillim-neshama repo

The game's source also lives in the `fruit-ninja/` folder of the
`tehillim-neshama` repo. Once that branch is merged into `main`, the same game
is additionally reachable at
https://yishai320.github.io/tehillim-neshama/fruit-ninja/ — the Tehillim app
stays at the root URL, and the two service workers keep separate offline
caches without interfering. You don't need both; the `Fruit-ninja-clone` repo
is the live home.

---

## 5. Making the Android APK with PWABuilder

1. Go to https://www.pwabuilder.com
2. Paste your live URL: `https://yishai320.github.io/Fruit-ninja-clone/`
3. It analyzes the site (it should find the manifest, service worker, and icons).
4. Choose **Package For Stores ▸ Android**.
5. Download the generated package. It contains:
   - an **APK** you can copy to a phone and install (enable "install from
     unknown sources" when prompted), and
   - a **signing key file** — **save this somewhere safe.** You need the same
     key to publish updates later or to put it on the Play Store.

**Important to understand about this APK:** the PWABuilder Android app is a
thin shell that opens your live hosted site. That means:
- The **hosted site must stay published** — if you delete the repo or turn off
  Pages, the app stops loading.
- Thanks to the service worker, it still works offline **after** the first
  open, because the game caches itself on that first run.

---

## 6. Updating the game later

Because the APK loads your hosted site, **you usually don't need to rebuild the
APK to change the game** — just update the website:

1. Edit `index.html` (all the graphics and logic live there).
2. Re-upload it to the `Fruit-ninja-clone` repo (**Add file ▸ Upload files**,
   replace the file, commit) — or drag the new folder onto Netlify again.
3. The change appears on the live site within a minute, and installed apps pick
   it up next time they're online.

If you change any of the 7 files, also bump the version string at the top of
`sw.js` (`fruit-ninja-v2` → `fruit-ninja-v3`) so phones that already cached the
old version fetch the new one.

To change the app's name or icon *inside the APK*, you'd re-run PWABuilder
(name comes from `manifest.json`; icons are the PNGs).

---

## 7. Gameplay tuning knobs

Everything is in `index.html`, in the constants block near the top of the
script — no code archaeology needed:

- `GRAVITY` (0.22) — how fast fruit falls; higher = harder.
- `BOMB_CHANCE` (0.15) — chance each spawn is a bomb.
- `MAX_LIVES` (3) — how many missed fruits end the game.
- `COMBO_GAP` (350 ms) — how quickly slices must chain to count as a combo.
- Fruit sizes, point values, and spawn weights — the `FRUIT_TYPES` table.
- Spawn pacing — the `interval` line at the bottom of `spawnWave()`.

---

## 8. Quick reference

- Live site: https://yishai320.github.io/Fruit-ninja-clone/
- Repo: https://github.com/yishai320/Fruit-ninja-clone
- Source also kept in: https://github.com/yishai320/tehillim-neshama →
  `fruit-ninja/` folder
- Host (own repo): upload **unzipped files**, `index.html` at root, enable
  Pages under Settings.
- Host (fast alt): https://app.netlify.com/drop
- APK: https://www.pwabuilder.com → paste URL → Package For Stores → Android →
  **keep the signing key**.
- Edit gameplay: change `index.html`, re-upload, bump the `sw.js` cache version.
  No APK rebuild needed.
- The Android app depends on the site staying online (works offline after
  first open).
