# Zaki OpenLine — PS4 Jailbreak Host

<p align="center">
  <strong>Offline-first PS4 exploit host with automatic firmware detection and GoldHEN integration.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-PlayStation%204-050508?style=for-the-badge&logo=playstation&logoColor=00cfff" alt="Platform: PlayStation 4">
  <img src="https://img.shields.io/badge/firmware-9.00–14.00-050508?style=for-the-badge&color=0098e8" alt="Firmware range">
  <img src="https://img.shields.io/badge/GoldHEN-v2.4b18.13-050508?style=for-the-badge&color=00df88" alt="GoldHEN v2.4b18.13">
  <img src="https://img.shields.io/badge/mode-offline--first-050508?style=for-the-badge&color=0d1218" alt="Offline-first">
</p>

A self-contained static PS4 host. Open `index.html` in the PS4 browser — it reads the console firmware from the User-Agent, picks the matching exploit chain, and redirects automatically. No configuration needed.

> **For educational, preservation, and research purposes only. Use on hardware you own, at your own risk. Not affiliated with or endorsed by Sony Interactive Entertainment.**

---

## Supported firmware

| Firmware | Exploit chain | Host directory |
|---|---|---|
| **9.00 – 9.60** | PSFree + Lapse | `900/` |
| **6.00 – 11.02** | CSSFontFace UAF + Lapse / NetCtrl | `css/latest/` |
| **11.50 – 14.00** | afterglow (SlopKit WebKit UAF) | `1400/` |

Firmware outside these ranges is not supported. The router shows an error page for unsupported versions.

---

## How it works

```
PS4 browser opens index.html
         │
         ▼
   Read User-Agent
   Parse PS4 firmware version
         │
         ├─ 9.00–9.60   ──────────────► 900/index.html
         │                               PSFree + Lapse → GoldHEN
         │
         ├─ 6.00–11.02  ──────────────► css/latest/index.html
         │                               CSSFontFace UAF → GoldHEN
         │
         ├─ 11.50–14.00 ──────────────► 1400/index.html (cache wait)
         │                                     │
         │                               AppCache installs
         │                                     │
         │                               1400/jb.html
         │                               afterglow WebKit UAF → GoldHEN
         │
         └─ other       ──────────────► Unsupported firmware page
```

### 1. Firmware router (`index.html`)

Reads `navigator.userAgent` for the pattern `PlayStation 4 X.XX`. Matches the major/minor version against the three supported ranges and calls `location.href` to the correct host subdirectory. The referrer is preserved, which is required by the afterglow chain's referrer guard.

### 2. PSFree + Lapse host (`900/`)

Entry page loads `psfree.js` (PSFree WebKit exploit), which hands off to the Lapse kernel exploit via firmware-specific ROP chains and kernel patches (`kpatch/*.elf`). On success, `loader.js` loads `goldhen.bin` and shows the payload menu. AppCache is installed through `PSPulse.manifest`, which caches all scripts, patches, and binaries for offline use.

### 3. CSSFontFace host (`css/latest/`)

Entry page loads `src/main.js` and `includes/script.js`. The user picks the kernel chain (Lapse or NetCtrl/Poops) and either lets Auto Jailbreak run after a countdown or presses **Jailbreak** manually. The CSSFontFace UAF exploit is memory-intensive; the host is intentionally scoped to the exploit flow only (no standalone utility payloads) to maximise available heap. AppCache uses per-file SHA-256 hashes in `cache.manifest`.

### 4. afterglow host (`1400/`)

Two-stage flow:
1. `index.html` — shows brand and firmware, installs AppCache from `cache.appcache`, then redirects to `jb.html` once caching is complete (or immediately on cache-hit).
2. `jb.html` — timing-critical page. Loads `<script type="module">import "./jb.js";</script>` and nothing else. All styling is inline; no external resources are referenced to avoid adding timing jitter to the exploit chain. `jb.js` drives the afterglow/SlopKit WebKit UAF, sets `body.log` while running (live log output visible in `#out`), `body.done` on success, and `body.fail` on failure. GoldHEN is loaded from `goldhen.bin` on success.

---

## Offline caching (AppCache)

All three hosts use the HTML5 Application Cache API, which is active in the PS4's WebKit browser. On first load the browser downloads and stores every file listed in the manifest. Subsequent loads — including with no internet — serve from the local cache.

| Host | Manifest | Notes |
|---|---|---|
| `900/` | `900/PSPulse.manifest` | Lists all PSFree/Lapse scripts, kpatch ELFs, payloads, and GoldHEN |
| `css/latest/` | `css/latest/cache.manifest` | Per-file SHA-256 hashes; logo cached at `../../logo.png` |
| `1400/` | `1400/cache.appcache` | Installs from the cache-wait page before redirecting to `jb.html` |

**After installation:** close and reopen the PS4 browser when the page instructs you to. If stale content loads, clear browser site data for the host and redo the cache install.

**When files change:** update the matching manifest (and hashes for `css/latest/`) before deploying. The PS4 browser will only fetch a new version of the cache when the manifest file itself changes.

---

## GoldHEN

All three hosts bundle **GoldHEN v2.4b18.13** (`goldhen.bin`, SHA-256 `39452f5e1c305ac9efcb4745eef7958b4db8c6d85299529f08700970b009ea4d`). This build supports all firmware versions covered by the host (9.00 through 14.00).

---

## Payload tools (900/ host)

The PSFree host includes a set of utility payloads accessible after GoldHEN loads:

| Payload | Purpose |
|---|---|
| FTP Server | File access over FTP |
| PS4Debug | Debug server for homebrew development |
| App2USB | Move applications to USB |
| Backup / Restore | System backup and restore |
| Enable / Disable Updates | Toggle system update checks |
| AppCache Install | Install AppCache on the console |
| History Blocker | Block browser history collection |
| PUP Decrypt | Decrypt PS4 firmware PUPs |
| RIF Renamer | Rename licence files |
| PSFree Fix | Re-apply PSFree after a crash |
| WebRTE | WebKit Remote Tools Extension |

Payloads are sent over the network using the host's payload sender. A compatible payload receiver must be running on the console (provided by GoldHEN) on the expected port.

---

## Troubleshooting

**Wrong page or stale version loading**
Clear the PS4 browser's site data for this host, close and reopen the browser, then start from `index.html`.

**Cache never finishes**
Make sure the host is served over HTTP or HTTPS (not `file://`), and that every path listed in the manifest resolves correctly from the server root.

**Exploit does not complete (CSSFontFace)**
The CSSFontFace chain is a heap-spray exploit — multiple attempts are normal. After each failure, fully close and reopen the PS4 browser (do not just reload). Try switching between the Lapse and NetCtrl chains if one chain fails repeatedly on a specific firmware. Do not modify spray constants or timing.

**Exploit does not complete (afterglow/PSFree)**
Reboot the console, clear browser data, and load the host fresh. Ensure no other browser tabs are open and no background processes are running.

---

## Project structure

```
index.html                  Firmware router (entry point)
logo.png                    Brand logo
navigation.manifest         AppCache manifest for root page

900/                        PSFree + Lapse host (FW 9.00–9.60)
│   index.html
│   loader.js               GoldHEN loader and payload UI
│   psfree.js, lapse.js     Exploit implementations
│   lapse/, rop/, kpatch/   Firmware-specific stages and kernel patches
│   module/                 Shared WebKit memory primitives
│   payloads/               Utility payload binaries
│   goldhen.bin             GoldHEN v2.4b18.13
│   PSPulse.manifest        AppCache manifest
│
css/latest/                 CSSFontFace host (FW 6.00–11.02)
│   index.html
│   includes/script.js      Exploit controller
│   src/                    CSSFontFace exploit, kernel, userland, patches
│   cache.manifest          AppCache manifest (SHA-256 per file)
│
1400/                       afterglow host (FW 11.50–14.00)
    index.html              Cache-wait and redirect page
    jb.html                 Timing-critical exploit page (all CSS inline)
    jb.js                   afterglow exploit entry (ES module)
    core.js, mem.js         Exploit primitives
    afterglow.js            WebKit UAF implementation
    ps4_offsets.js          Per-firmware offsets
    goldhen.bin             GoldHEN v2.4b18.13
    patchset-*.bin          Per-firmware kernel patches
    cache.appcache          AppCache manifest
```

---

## Credits and attribution

This project is built on top of publicly released PS4 exploit research. The exploit chain implementations are not original to this repository.

```
PSFree WebKit exploit ......... fail0verflow, theflow0, and contributors
Lapse kernel exploit .......... theflow0 and contributors
CSSFontFace UAF chain ......... Specter (SpecterDev) and contributors
afterglow / SlopKit ........... well0nez, m0zz and contributors
GoldHEN homebrew enabler ...... GoldHEN Team (SiSTRo, SocraticBliss, etc.)
Original PSPulse host ......... BlackArch (sudoBlackArch)
Community ..................... PlayStation Pulse (Telegram)
Premium game servers .......... NodePlay
```

**Original host author:** [BlackArch](https://t.me/sudoBlackArch)  
**Community:** [PlayStation Pulse](https://t.me/PlayStation_Pulse)  
**Premium Game Servers:** [NodePlay](https://nodeplay.net/)

> For any use of the materials or files, the links to the original author [BlackArch](https://t.me/sudoBlackArch) and the [PlayStation Pulse](https://t.me/PlayStation_Pulse) Telegram group must remain on all pages.

---

*Maintained by [zakio0](https://github.com/zakio0)*
