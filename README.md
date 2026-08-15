# E-G.O. Cache — Web Flasher

**AutoMate Vol.1: Low Power Apps · LPA-02 · Age: 16+**

A configurable digital logbook beacon. First boot launches a setup page —
name your cache, set your WiFi, pick your theme color. No code editing.

This repo hosts the browser-based installer for it: buy it on Gumroad, then
flash your ESP32-S3 straight from Chrome or Edge — open `install.html`,
enter your license key, click, done. No build tools needed.

It exists only to host the files a browser needs to flash a device over USB
(ESP Web Tools requires them to be fetchable over HTTP/HTTPS) — it's **not**
an invitation to use the firmware without a purchase. See `LICENSE.txt`.

## Status: live

Gumroad listing published (`https://whizworks.gumroad.com/l/qtcjj`),
`cloudflare-worker.js`'s `GUMROAD_PRODUCT_ID`, `install.html`'s `#buy-link`
and `WORKER_URL` are all wired to real values, GitHub Pages is enabled on
this repo (`whoishafiz/E-G.O.-Cache`) — live at
`https://whoishafiz.github.io/E-G.O.-Cache/install.html`. End-to-end license
verification confirmed working with a real purchased key (2026-08-15).

If you're maintaining this: the Worker lives at
`https://egocache.hafizmaiddin.workers.dev/`, source in
`cloudflare-worker.js`. Its `GUMROAD_PRODUCT_ID` (`2LyfxyRFV3Y66g0ftSXQKw==`)
was looked up from `https://api.gumroad.com/v2/products/qtcjj` — no Gumroad
API token needed, that endpoint is public.

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
