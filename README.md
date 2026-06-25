# ZySign — macOS DSC Toolkit for MCA21 V3

> Sign MCA eForms directly from your Mac using a USB DSC token — no eMudhra emBridge required.

**All signing is local. PINs never leave your machine. No uploads. No cloud.**

---

## What is ZySign?

The MCA21 V3 portal (`efiling.mca.gov.in`) requires a local signing daemon to
communicate with your USB DSC token. ZySign replaces the proprietary eMudhra
emBridge daemon entirely, with two modes:

- **ZySign** — desktop app for signing PDFs directly, no browser needed
- **ZySign Bridge** — background service that lets the MCA portal sign through your browser, exactly as before

---

## Requirements

- macOS 13 Ventura or later (Apple Silicon or Intel)
- A USB DSC token with its driver installed (see [Token Setup](#token-setup) below)

No Python, Xcode, or Homebrew required. ZySign bundles everything it needs.

---

## Install

### Option A — PKG Installer

1. Download `ZySign-<version>.pkg` from the [Releases](https://github.com/hypersdk/zysign/releases) page
2. Double-click the PKG and follow the installer wizard
3. Open `/Applications/ZySign.app`

### Option B — Homebrew Cask

```bash
brew install --cask hypersdk/tap/zysign
```

---

## Token Setup

Install the PKCS#11 driver for your token **before** launching ZySign:

| Token | Driver file | Where to get it |
|-------|-------------|-----------------|
| **mToken CryptoID** | `libcryptoid_pkcs11.dylib` | Download from your mToken vendor — use the Apple Silicon build on M-series Macs |
| **ePass 2003** (Feitian) | `libcastle.1.0.0.dylib` | Download the macOS driver from Feitian |
| **ProxKey / Watchdata** | `libwdpkcs.dylib` | Download from ProxKey / Watchdata |
| **Generic (OpenSC)** | `opensc-pkcs11.so` | `brew install opensc` |

After installing, verify the driver is in place:

```bash
# mToken example — adjust the filename for your token
ls /usr/local/lib/libcryptoid_pkcs11.dylib
```

---

## Using ZySign (Desktop PDF Signing)

1. Plug in your USB token
2. Open `ZySign.app`
3. **Step 1 — Token:** your token name appears once detected
4. **Step 2 — PDF:** click *Open PDF* and select your MCA eFiling form
5. **Step 3 — Sign:** enter your PIN and click *Sign*

The signed PDF is saved alongside the original as `<filename>_MCA_SIGNED.pdf`.

---

## Using ZySign Bridge (Browser-based MCA Signing)

ZySign Bridge replaces the eMudhra emBridge daemon so the MCA portal works in
your browser without any changes to your workflow.

1. Open `ZySign.app` and navigate to **Bridge** in the sidebar
2. Click **Start Bridge** — it stops any running emBridge automatically
3. Visit `efiling.mca.gov.in` and sign as normal

The bridge listens on `https://localhost.emudhra.com:26769`, the same address the
MCA portal expects.

---

## Security

| Property | Guarantee |
|----------|-----------|
| PIN | Never stored, never logged, discarded immediately after signing |
| Private key | Never extracted from hardware — signing is delegated to the token via PKCS#11 `C_Sign` |
| Document data | Never sent off-device |
| PIN transport | AES-256-CBC encrypted over localhost HTTPS only |

---

## Uninstall

```bash
# Remove the app
sudo rm -rf /Applications/ZySign.app

# Remove data files
rm -rf ~/Library/Application\ Support/com.zyvor.zysign
rm -f  ~/Library/Preferences/com.zyvor.zysign.plist
sudo rm -rf /Library/ZySignBridge
```

Or double-click `/Applications/ZySign.app/Contents/Resources/Uninstall ZySign.command`
before removing the app.

---

## License

Proprietary — Zyvor. All rights reserved.
