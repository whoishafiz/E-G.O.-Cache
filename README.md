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

## Status: NOT live — setup pending

Everything in this folder is built and ready (manifest, firmware binaries,
install page, license-gate UI, worker code) but the license-verification path
is **not wired up yet** because no Gumroad listing exists for this product.
`install.html`'s `WORKER_URL` and `#buy-link` are placeholders, and
`cloudflare-worker.js`'s `GUMROAD_PRODUCT_ID` is a placeholder too.

### Remaining manual steps (Hafiz)

1. **Create the Gumroad listing** for DIY Geocache (age 16+; price set on
   Gumroad, not tracked here). When setting it up, enable **"Generate a
   unique license key per sale"**
   under the product's Content settings — the license gate depends on this.
2. **Get the real `product_id`.** Gumroad's `v2/licenses/verify` endpoint
   requires `product_id`, not `product_permalink`, for any product created
   on/after Jan 9 2023 — using the permalink silently fails real license
   checks while still looking correct against a bogus key. To get the real
   id: call the verify endpoint once with the permalink and any key —
   Gumroad's own error response echoes back the required `product_id`.
   (Same trick already used for Premium Digital Front.)
3. **Paste the `product_id`** into `cloudflare-worker.js`, replacing
   `GUMROAD_PRODUCT_ID_PLACEHOLDER`.
4. **Deploy the Worker**: Cloudflare dashboard → Workers & Pages → Create →
   paste in `cloudflare-worker.js` → Deploy. Copy the resulting
   `*.workers.dev` URL.
5. **Paste the deployed Worker URL** into `install.html`'s `WORKER_URL`
   constant, replacing `PASTE_DEPLOYED_WORKER_URL_HERE`.
6. **Paste the real Gumroad buy-link** into `install.html`'s `#buy-link`
   anchor, replacing the `#` placeholder href.
7. Publish this repo (or this folder) via GitHub Pages, then update the
   top-level product README with the live `install.html` URL.

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
