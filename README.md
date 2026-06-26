# PS4 GoldHEN Exploit Host

A comprehensive PS4 exploit host with support for multiple firmware versions and GoldHEN payload delivery.

## Host Selection System

The main page provides a **unified host selector** where users can choose the appropriate host for their PS4 firmware version:

| Host | Firmware Range | Exploit Method |
|------|----------------|----------------|
| **5.05 Host** | 5.05 | WebKit + Kernel Exploit |
| **6.72 Host** | 6.72 | WebKit + Kernel Exploit |
| **7.xx - 8.xx Host** | 7.00 - 8.52 | pOOBs4 + Lapse |
| **9.xx Host** | 9.00 - 9.60 | pOOBs4 + Lapse |

> **Why separate hosts for 7.xx-8.xx and 9.xx?**
> Although both firmware ranges use the pOOBs4 + Lapse exploit chain, the underlying activation approach differs between them. Separating these into dedicated hosts allows for version-specific optimizations, resulting in maximum stability and reliability for each firmware range.

## Supported Firmware Versions

| Firmware Range | Exploit Method | Host Directory | Status |
|----------------|----------------|----------------|--------|
| **5.05** | WebKit + Kernel Exploit | `/505` | ✅ Stable |
| **6.72** | WebKit + Kernel Exploit | `/672` | ✅ Stable |
| **7.00 - 7.55** | pOOBs4 + Lapse | `/700` | ✅ Stable |
| **8.00 - 8.52** | pOOBs4 + Lapse | `/700` | ✅ Stable |
| **9.00 - 9.60** | pOOBs4 + Lapse | `/900` | ✅ Stable |

### Firmware 7.xx - 8.xx Specific Versions
- **7.xx**: 7.00, 7.01, 7.02, 7.50, 7.51, 7.55
- **8.xx**: 8.00, 8.01, 8.03, 8.50, 8.52

### Firmware 9.xx Specific Versions
- **9.xx**: 9.00, 9.03, 9.04, 9.50, 9.51, 9.60

## GoldHEN Versions

All supported firmware versions now include a **GoldHEN version selector**:

| Version | Description |
|---------|-------------|
| **GoldHEN v2.4b18.10** | Latest version with newest features |
| **GoldHEN v2.4b18.5** | Stable version for maximum reliability |

Each host includes both `v2.4b18.10` and `v2.4b18.5` with independent caching systems.

## Available Payloads

### Firmware 5.05

| Payload | File | Description |
|---------|------|-------------|
| GoldHEN | goldhen.bin / goldhen5.bin | Homebrew enabler (v2.4b18.10 / v2.4b18.5) |
| Kernel-Clock | Kernel-Clock.bin | System clock patches |
| FTP Server | ftp.bin | Remote file access |
| PS4Debug | ps4debug.bin | Debugging utility |
| App2USB | app2usb.bin | Move apps to external storage |
| AppCache Install | appcache-install.bin | Application cache installer |
| Backup | backup.bin | Save data backup |
| Restore | restore.bin | Save data restore |
| Disable Updates | disable-updates.bin | System update blocker |
| Enable Updates | enable-updates.bin | System update enabler |
| History Blocker | history-blocker.bin | Browser history protection |
| PUP Decrypt | pup-decrypt.bin | Firmware decryption |
| RIF Renamer | rif-renamer.bin | License file renamer |

### Firmware 6.72

| Payload | File | Description |
|---------|------|-------------|
| GoldHEN | goldhen.bin / goldhen5.bin | Homebrew enabler (v2.4b18.10 / v2.4b18.5) |
| FTP Server | ftp.bin | Remote file access |
| PS4Debug | ps4debug.bin | Debugging utility |
| App2USB | app2usb.bin | Move apps to external storage |
| AppCache Install | appcache-install.bin | Application cache installer |
| Backup | backup.bin | Save data backup |
| Restore | restore.bin | Save data restore |
| Disable Updates | disable-updates.bin | System update blocker |
| Enable Updates | enable-updates.bin | System update enabler |
| History Blocker | history-blocker.bin | Browser history protection |
| PUP Decrypt | pup-decrypt.bin | Firmware decryption |
| RIF Renamer | rif-renamer.bin | License file renamer |

### Firmware 7.xx - 8.xx

| Payload | File | Description |
|---------|------|-------------|
| GoldHEN | goldhen.bin / goldhen5.bin | Homebrew enabler (v2.4b18.10 / v2.4b18.5) |
| PSFree Fix | ps4-psfree-fix.bin | PSFree stability patch |
| WebRTE 9.00 | WebRTE_900.bin | Real-time editing |
| FTP Server | ftp.bin | Remote file access |
| PS4Debug | ps4debug.bin | Debugging utility |
| App2USB | app2usb.bin | Move apps to external storage |
| AppCache Install | appcache-install.bin | Application cache installer |
| Backup | backup.bin | Save data backup |
| Restore | restore.bin | Save data restore |
| Disable Updates | disable-updates.bin | System update blocker |
| Enable Updates | enable-updates.bin | System update enabler |
| History Blocker | history-blocker.bin | Browser history protection |
| PUP Decrypt | pup-decrypt.bin | Firmware decryption |
| RIF Renamer | rif-renamer.bin | License file renamer |

### Firmware 9.xx

| Payload | File | Description |
|---------|------|-------------|
| GoldHEN | goldhen.bin / goldhen5.bin | Homebrew enabler (v2.4b18.10 / v2.4b18.5) |
| PSFree Fix | ps4-psfree-fix.bin | PSFree stability patch |
| WebRTE 9.00 | WebRTE_900.bin | Real-time editing |
| FTP Server | ftp.bin | Remote file access |
| PS4Debug | ps4debug.bin | Debugging utility |
| App2USB | app2usb.bin | Move apps to external storage |
| AppCache Install | appcache-install.bin | Application cache installer |
| Backup | backup.bin | Save data backup |
| Restore | restore.bin | Save data restore |
| Disable Updates | disable-updates.bin | System update blocker |
| Enable Updates | enable-updates.bin | System update enabler |
| History Blocker | history-blocker.bin | Browser history protection |
| PUP Decrypt | pup-decrypt.bin | Firmware decryption |
| RIF Renamer | rif-renamer.bin | License file renamer |

## Technical Optimizations

### Version Separation for Stability
- **Dedicated 7.xx-8.xx host** (`/700`) — Optimized specifically for firmware 7.00–8.52 activation flow
- **Dedicated 9.xx host** (`/900`) — Optimized specifically for firmware 9.00–9.60 activation flow
- **Independent exploit scripts** — Each host uses its own tailored exploit chain, kernel patches, and ROP gadgets tuned for the target firmware range
- **Reduced failure rate** — Version-specific tuning eliminates cross-version compatibility compromises

### Kernel Panic Prevention
- **Optimized memory management** — Proper cleanup of exploit buffers after payload execution
- **Timing improvements** — Refined delays between exploit stages to ensure stable kernel state
- **Garbage collection control** — Strategic GC calls to prevent memory corruption
- **Enhanced error handling** — Graceful recovery from partial exploit failures

### Exploit Chain Reliability
- **Improved UAF exploit stability** — Refined heap spray parameters for consistent exploitation
- **WebKit exploit optimization** — Reduced race condition windows
- **Kernel ROP chain refinements** — Optimized return-oriented programming sequences
- **Payload loader improvements** — Enhanced binary loading with integrity checks

### Algorithm Improvements
- **Multi-stage exploit verification** — Each stage confirms success before proceeding
- **Automatic retry logic** — Failed stages can be retried without full page reload
- **Dynamic firmware detection** — Automatic selection of correct offsets and patches
- **Cache manifest versioning** — Ensures proper offline functionality and prevents stale code execution

### GoldHEN Optimization
- **Dual loading system** — Local BinLoader with online fallback
- **Isolated caching** — Separate cache manifests for each GoldHEN version
- **Auto-activation** — Automatic exploit execution in offline mode
- **Thread management** — Optimized payload execution to prevent crashes

## Offline Support

Each host includes cache manifest files for offline functionality:

| Host | Manifest Files |
|------|----------------|
| 505 | cache.manifest |
| 672 | cache.manifest |
| 700 | PSPulse.cache (v2.4b18.10), PSPulse5.cache (v2.4b18.5) |
| 900 | PSPulse.manifest (v2.4b18.10), PSPulse5.manifest (v2.4b18.5) |

## Usage

1. Host these files on a local web server or use a hosting service
2. On your PS4, navigate to the host URL using the web browser
3. Select your firmware version from the main page
4. Choose your preferred GoldHEN version (v2.4b18.10 or v2.4b18.5)
5. Follow on-screen instructions to load GoldHEN

---

## Changelog

### v2.4b18.10 — Latest Release

#### GoldHEN Updated
- Updated GoldHEN from **v2.4b18.9** to **v2.4b18.10** across all hosts (5.05, 6.72, 7.xx-8.xx, 9.xx)

#### PSFree Improvements
- Integrated latest **PSFree 1.7** stability improvements for pOOBs4 + Lapse exploit chain
- Refined exploit timing for firmware 7.xx–8.xx and 9.xx ranges
- Reduced failure rate during the WebKit and kernel exploitation stages

#### Bug Fixes
- **Fixed payload loading on 7.xx-8.xx host** — All payload buttons (PSFree Fix, FTP Server, PS4Debug, etc.) previously failed to deliver the binary to the PS4 when the online (non-BinLoader) path was taken. The `toogle_payload` function was missing from the 7.xx-8.xx exploit chain (`lapse.mjs` / `lapse5.mjs`), causing silent failures. This has been implemented and now matches the behavior of the 9.xx host

#### Stability Improvements
- Improved memory cleanup after payload execution on 7.xx-8.xx firmware
- Better error reporting in the exploit chain console output
- More consistent offline cache behavior across all hosts

---

**Author:** [BlackArch](https://t.me/sudoBlackArch)  
**Telegram:** [PlayStation Pulse](https://t.me/PlayStation_Pulse)  

