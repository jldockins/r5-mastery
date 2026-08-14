# R5 Mastery — installable web app

A self-contained PWA. No build step, no dependencies, no server code. Progress is stored in
`localStorage` on the device, so it survives closing the app and works offline after the first load.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app — markup, styles, checklist data, logic |
| `manifest.webmanifest` | Makes it installable (name, icons, standalone display) |
| `sw.js` | Service worker — caches everything for offline use |
| `icon-192.png` / `icon-512.png` | Home screen and splash icons |
| `icon-512-maskable.png` | Android adaptive icon |
| `icon-180.png` | iOS home screen icon |

## Deploy

It needs HTTPS. Service workers and install prompts don't work over plain `http://`,
and they don't work from a `file://` path — opening `index.html` directly still runs the app,
but you won't get offline caching or the install option.

**GitHub Pages**

```bash
git init
git add .
git commit -m "R5 mastery app"
git branch -M main
git remote add origin git@github.com:YOURNAME/r5-mastery.git
git push -u origin main
```

Then Settings → Pages → Source: `main`, folder `/ (root)`. Live at
`https://YOURNAME.github.io/r5-mastery/` in a minute or two.

**Or a subfolder of a site you already run** — drop the folder in and it works, since every
path in the app is relative.

**Or Netlify** — drag the folder onto app.netlify.com/drop. No account needed for a test URL.

## Install on your phone

- **iPhone/iPad:** open the URL in Safari (not Chrome — iOS only installs from Safari), tap
  Share, then Add to Home Screen.
- **Android:** open in Chrome, tap the ⋮ menu, then Install app or Add to Home screen.

It opens fullscreen with no browser chrome and works with no signal.

## Editing the checklist

Open `index.html` and find `const SECTIONS = [`. Each section is:

```js
{
  id: "af",              // unique, used internally
  tab: "AF",             // short label on the tab strip
  name: "Autofocus",     // heading
  color: "#D45FA6",      // drives the accent for the whole tab
  blurb: "...",          // one-line intro under the heading
  whereLabel: "...",     // optional — overrides "Where to change it"
  optionsLabel: "...",   // optional — overrides "Options and when to use them"
  items: [ ... ]
}
```

And each item:

```js
{
  id: "af-method",                  // unique across the whole app — this is the storage key
  title: "AF method (the focus area)",
  where: "Press the AF point selection button...",
  options: [
    ["Option name", "When to use it"],
    ...
  ],
  tip: "Optional italic note at the bottom."   // omit the line entirely if you don't want one
}
```

Two rules: item `id`s must stay unique, and don't rename an `id` you've already checked off —
that's the key progress is saved against, so renaming it loses the checkmark.

## Bumping the cache

The service worker serves cached files first. After you edit `index.html`, change the version
string at the top of `sw.js`:

```js
const CACHE = 'r5-mastery-v2';   // was v1
```

Otherwise installed copies keep showing the old version.
