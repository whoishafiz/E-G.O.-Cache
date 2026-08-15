# E-G.O. Cache — ESP32 Digital Geocache Logbook (Browser Web Flasher)

**AutoMate Vol.1: Low Power Apps · LPA-02 · Age: 16+**

A configurable ESP32 digital geocache logbook beacon — a DIY electronic
logbook geocachers find in the field, connect to over Wi-Fi, and sign
digitally instead of a paper log. First boot launches a setup page: name
your cache, set your Wi-Fi, pick your theme color. No code editing.

**Buy E-G.O. Cache: [whizworks.gumroad.com/l/qtcjj](https://whizworks.gumroad.com/l/qtcjj)**

This repo hosts the browser-based installer for it: buy it on Gumroad, then
flash your ESP32, ESP32-S3, or ESP32-C3 board straight from Chrome or Edge
— open `install.html`, enter your license key, click, done. No build tools
needed. ESP32-S3 is hardware-verified; classic ESP32 and ESP32-C3 are
build-verified only (compiles clean, not yet confirmed on physical
hardware) — see `manifest.json`'s per-chip builds.

It exists only to host the files a browser needs to flash a device over USB
(ESP Web Tools requires them to be fetchable over HTTP/HTTPS) — it's **not**
an invitation to use the firmware without a purchase. See `LICENSE.txt`.

## Getting started

1. Purchase on Gumroad → instantly receive a license key + the full source
   code as a zip.
2. Flash via browser: **[whoishafiz.github.io/E-G.O.-Cache/install.html](https://whoishafiz.github.io/E-G.O.-Cache/install.html)**
   (Chrome or Edge on desktop) — enter your license key, plug in your
   ESP32, ESP32-S3, or ESP32-C3 board, click **Connect & Install**.
3. Power on → connect to the device's Wi-Fi → run the first-boot setup page
   to name your cache and finish configuration.

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
| `manifest.json` | ESP Web Tools manifest — one `builds[]` entry per chip family, each a single merged image at offset `0x0` |
| `firmware-esp32s3.bin` | Merged bootloader+partition-table+app image for ESP32-S3 (hardware-verified) |
| `firmware-esp32.bin` | Same, for classic ESP32 (build-verified only) |
| `firmware-esp32c3.bin` | Same, for ESP32-C3 (build-verified only) |
| `install.html` | Browser flasher page + license-key gate UI |
| `cloudflare-worker.js` | CORS relay to Gumroad's license-verify endpoint |
| `LICENSE.txt` | Terms — flasher page reusable, `.bin` files are not |
| `robots.txt` | Blocks search-engine indexing of this page |

### Rebuilding the firmware binaries

Each `firmware-<chip>.bin` is a single merged image (bootloader + partition
table + app, via `esptool.py merge_bin`) so the manifest only needs one
offset-`0x0` part per chip, sidestepping the bootloader-offset difference
between chip families (`0x0` for S3/C3, `0x1000` for classic ESP32). To
rebuild all three after a firmware change:

```
idf.py set-target <esp32s3|esp32|esp32c3>
idf.py build
python -m esptool --chip <chip> merge_bin -o flasher/firmware-<chip>.bin \
  --flash_mode dio --flash_freq <from build/flash_args> --flash_size 4MB \
  <0x0 or 0x1000 — see build/flash_args> build/bootloader/bootloader.bin \
  0x8000 build/partition_table/partition-table.bin \
  0x10000 build/geocache.bin
```
Repeat per target, then `idf.py set-target esp32s3 && idf.py build` again to
restore the primary dev target before committing. Read `--flash_mode`/
`--flash_freq`/the bootloader offset from the freshly generated
`build/flash_args` each time rather than assuming — they differ by target.
