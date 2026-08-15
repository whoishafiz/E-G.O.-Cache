# DIY Geocache — Web Flasher

**AutoMate Vol.1: Low Power Apps · LPA-02 · Age: 16+**

A configurable digital logbook beacon. First boot launches a setup page —
name your cache, set your WiFi, pick your theme color. No code editing.

This repo hosts the browser-based installer for it: buy it on Gumroad, then
flash your ESP32-S3 straight from Chrome or Edge — open `install.html`,
enter your license key, click, done. No build tools needed.

It exists only to host the files a browser needs to flash a device over USB
(ESP Web Tools requires them to be fetchable over HTTP/HTTPS) — it's **not**
an invitation to use the firmware without a purchase. See `LICENSE.txt`.

## Status: almost live — 2 manual steps left

The Gumroad listing exists (`https://whizworks.gumroad.com/l/qtcjj`),
`cloudflare-worker.js`'s `GUMROAD_PRODUCT_ID` and `install.html`'s
`#buy-link` are both wired to the real values. This repo
(`whoishafiz/E-G.O.-Cache`) is public and holds the flasher files.

### Remaining manual steps (Hafiz)

1. **Publish the Gumroad listing.** As of the last `product_id` lookup it
   reported `"is_published": false` — buyers can't check out until you
   publish it. Also confirm **"Generate a unique license key per sale"**
   is enabled under Content settings — the license gate depends on it.
2. **Deploy the Worker**: Cloudflare dashboard → Workers & Pages → Create →
   paste in `cloudflare-worker.js` (product_id is already filled in) →
   Deploy. Copy the resulting `*.workers.dev` URL.
3. **Paste the deployed Worker URL** into `install.html`'s `WORKER_URL`
   constant, replacing `PASTE_DEPLOYED_WORKER_URL_HERE`.
4. **Enable GitHub Pages** on this repo: Settings → Pages → Deploy from
   branch → `main` / `/ (root)` → Save. Live URL will be
   `https://whoishafiz.github.io/E-G.O.-Cache/install.html`.

Until all of the above is done, `install.html` will render correctly but the
license gate will never unlock (`WORKER_URL` points nowhere), which is the
safe failure mode.

## Files in this folder

| File | Purpose |
|---|---|
| `manifest.json` | ESP Web Tools manifest — chip family + flash offsets |
| `bootloader.bin` | Bootloader, offset `0x0` |
| `partition-table.bin` | Partition table, offset `0x8000` |
| `firmware.bin` | Application image, offset `0x10000` |
| `install.html` | Browser flasher page + license-key gate UI |
| `cloudflare-worker.js` | CORS relay to Gumroad's license-verify endpoint |
| `LICENSE.txt` | Terms — flasher page reusable, `.bin` files are not |
| `robots.txt` | Blocks search-engine indexing of this page |

The `.bin` files here were copied from `../build/` (ESP-IDF build output) at
firmware-build time. If you rebuild the firmware, re-copy these three files
before shipping a new flasher.
