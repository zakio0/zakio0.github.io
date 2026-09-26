# 《 Z@ki OpenLine 》

A self-hosted, offline PS4 jailbreak entry point. Point the PS4 browser at this
site and it auto-detects the console's firmware version, picks the matching
public exploit chain, and routes to it — no internet connection required once
loaded, thanks to AppCache.

Edited and maintained by **Houssam Lovy**, based on the publicly released
PS4/PS5 exploit chains listed in [Credits](#credits).

## How it works

1. `index.html` reads the PS4's `User-Agent` string to work out the firmware
   version (e.g. `PlayStation 4/11.52`).
2. That version is matched against a firmware→chain table to pick the correct
   exploit:

   | Chain     | Firmware range   |
   |-----------|------------------|
   | PSFree    | 9.00 – 9.60      |
   | CSSKern   | 10.00 – 10.51    |
   | Lapse     | 11.00 – 12.02    |
   | Poops     | 12.50 – 13.00    |
   | Slop      | 13.02 – 13.52    |

3. The page redirects to the matching `run_*.html` entry point, which loads
   that chain's kernel exploit, patches, and payload.
4. A chain can be forced regardless of detected firmware with a query string,
   e.g. `?bug=lapse`.
5. The `cache.appcache` manifest lets the whole site (including binaries) be
   cached by the PS4's browser on first load, so the exploit still runs if the
   console is offline afterward.

## Project layout

```
.
├── index.html            # Firmware detection + chain router (entry point)
├── cache.appcache        # AppCache manifest for offline hosting
├── logo_raw.png          # Site logo
│
├── run_psfree.html       # PSFree chain UI (FW 9.00–9.60)
├── psfree/               # PSFree exploit implementation
│   ├── loader.js, send.js, payloads.js, config.js, alert.js
│   ├── module/           # WebKit heap/memory primitives (mem, rw, view, offset...)
│   ├── kpatch/           # Kernel patch ELFs per firmware (900.elf, 950.elf, ...)
│   ├── lapse/, rop/      # Firmware-specific lapse & ROP stages used by PSFree
│   └── goldhen.bin       # GoldHEN plugin loader payload
│
├── run_lapse.html        # Lapse chain UI (FW 11.00–12.02)
├── chain_lapse.js        # Lapse exploit implementation
│
├── run_poops.html        # Poops chain UI (FW 12.50–13.00)
├── chain_poops.js        # Poops exploit implementation
│
├── run_slop.html         # Slop chain UI (FW 13.02–13.52)
├── slop/                 # Slop exploit implementation
│   ├── core.js, jb.js, mem.js, int64.js, ps4_offsets.js, rpc_worker.js
│   ├── patches/          # Kernel patch binaries per firmware
│   └── goldhen.bin, payload2.bin
│
├── css/                  # CSSKern chain (FW 10.00–10.51)
│   ├── run_css.html
│   ├── includes/script.js
│   └── src/              # CSSKern exploit implementation (main, loader, netctrl, ps4/...)
│
├── patches/              # Shared kernel patch binaries (1100.bin – 1300.bin)
├── core.js, mem.js, int64.js, ps4_offsets.js, rpc_worker.js  # Shared PS4 exploit primitives
└── payload.bin            # Shared homebrew enabler / GoldHEN payload
```

## Usage

1. Host the contents of this repository somewhere the PS4 can reach — GitHub
   Pages, Netlify, or a local web server on your network.
2. Open the PS4's web browser and navigate to the hosted URL.
3. Wait for firmware detection; the page will show the detected firmware and
   the exploit chain it selected, then redirect automatically.
4. Follow the on-screen state (`detecting` → `running` → `done`) on the chain
   page. On success, GoldHEN (or the configured payload) is loaded.

### Forcing a specific chain

```
https://your-host/?bug=lapse
```

Valid values: `psfree`, `css`, `lapse`, `poops`, `slop`.

## Supported firmware

Only firmware **9.00 and above** is supported. Consoles below 9.00, or above
the highest range listed above, will show an **UNSUPPORTED FIRMWARE** state
on the landing page.

## Credits

This project bundles and routes between several independently published,
open-source PS4 exploit chains — the exploit logic itself is not original to
this repo. The chain implementations here are sourced from:

- [**raw13g.github.io**](https://raw13g.github.io)
- [**psx8.github.io**](https://psx8.github.io)

All credit for the underlying research and exploit code (PSFree, CSSKern,
Lapse, Poops, Slop, and the GoldHEN payload) goes to their respective
original authors and the above projects. This repository only adds the
auto-detection front end, offline hosting (AppCache), and UI.

## Disclaimer

This project is intended for use on consoles you own, for homebrew,
backups, and research purposes. Use at your own risk.
