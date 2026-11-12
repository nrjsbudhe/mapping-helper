# Freezer Measure (PWA)

Offline, glove-friendly app for recording `measurements` in `world_config.json`.

## What's in here

- `index.html` is the app itself.
- `sw.js` is the service worker. It caches the app on the phone so it runs with no signal.
- `manifest.webmanifest` and `icons/` provide the home-screen install, icon and name.

The app holds no warehouse data. Each phone loads its own `world_config.json`, so the hosted files are safe to put on any static host.

## Hosting

Any static HTTPS host works. HTTPS is required, because service workers don't run on plain HTTP (localhost is the only exception). Upload the whole folder as-is; it works from a subfolder too.

- **Internal nginx/Apache:** copy the folder into the web root. Serve `sw.js` with `Cache-Control: no-cache` so phones see updates promptly. Serve `.webmanifest` as `application/manifest+json`.
- **Cloudflare Pages / Netlify / S3 + CloudFront / GitHub Pages:** upload the folder; no build step is needed.

Example nginx snippet:

    location = /measure/sw.js { add_header Cache-Control "no-cache"; }
    types { application/manifest+json webmanifest; }

## Installing on a phone (do this on Wi-Fi, before going in the freezer)

**Android (Chrome):** open the URL, tap ⋮ then **Add to Home screen** (or **Install app**).
**iPhone (Safari):** open the URL, tap Share then **Add to Home Screen**.

Open it from the new icon once. The home screen should say **Ready to work offline** with a green dot. After that it works with no connection.

## Using it in the field

1. Load `world_config.json` from the phone (Files, Downloads or Drive offline copy).
2. Record measurements. Every change is saved on the phone automatically.
3. Back on Wi-Fi, use **Export** (Android) or **Share** (iPhone or Android) to get the updated file off the phone.

On iPhone, use **Share** rather than Export: iOS home-screen apps don't download files reliably.

## Pushing an update

1. Edit the files.
2. Change `VERSION` in `sw.js` (for example `measure-v1` to `measure-v2`).
3. Upload.

Phones show **Update available** the next time they open the app while online. Tapping it reloads into the new version. Loaded config and unexported measurements are kept.

## Good to know

- Unexported work lives in that phone's app storage. Uninstalling the app or clearing site data deletes it, so export at the end of each session.
- Each installed copy is separate. A Safari tab and the home-screen app on the same iPhone don't share data.
- Voice input still depends on the phone. The keypad always works offline.
