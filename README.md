# OTP Authenticator

A single-page, fully client-side Google Authenticator replica. No server, no build step — TOTP codes are computed in the browser with the Web Crypto API and never leave your device.

Vendored decoders (both MIT/Apache licensed): `zxing.js` — the primary QR engine, [zxing-wasm](https://github.com/Sec-ant/zxing-wasm) (zxing-cpp compiled to WebAssembly) bundled into one classic script with the WASM inlined via esbuild, so it works even from `file://`; `jsqr.js` — [jsQR](https://github.com/cozmo/jsQR) as fallback. ZXing reads the dense, small, anti-aliased enrollment QRs that defeat pure-JS decoders.

## Features

- **TOTP / HOTP** (RFC 6238 / 4226): SHA-1/256/512, 6 or 8 digits, custom periods. Engine self-tests against the official RFC vectors on every load (see console).
- **Enroll accounts** five ways: paste a QR screenshot (`Win+Shift+S` then `Ctrl+V`), upload a QR image, live **scan-from-screen** (`getDisplayMedia`), **camera scan** (`getUserMedia`, rear camera on phones), or manual Base32 secret / `otpauth://` URI. Capture buttons the device can't do are hidden automatically.
- **Installable PWA**: manifest + service worker + icons; installs to the desktop or a phone home screen when served over HTTPS (e.g. GitHub Pages) and works fully offline afterwards. (Service worker and install don't apply when opened via `file://` — the page still works normally there.)
- **Masked passphrase dialogs** for unlock, change-passphrase and backup import — the passphrase is never shown on screen.
- **Google Authenticator migration**: scan or paste the phone app's *Transfer accounts* QR (`otpauth-migration://`) to import all accounts at once.
- **Encrypted vault**: accounts are stored in `localStorage` encrypted with AES-GCM-256, key derived from your passphrase via PBKDF2 (SHA-256, 310k iterations). Nothing is ever stored in plain text.
- **Export / Import**: encrypted JSON backups using the same envelope format; duplicate-safe merge on import.
- Click a card to copy the code; live 30-second countdown bar; rename/delete per account.

## Run locally

Just open `index.html` in Chrome or Edge (double-click works — `file://` is a secure context there). Always open it from the same path so the browser keeps the same `localStorage` vault.

## Deploy (later)

GitHub Pages, account **attiqtherehman** (`gh auth switch --user attiqtherehman` first, switch back after):

1. Create a public repo `otp-authenticator`, push this folder.
2. Settings → Pages → Deploy from branch → `main` / root.
3. The page and everyone's secrets stay client-side; the site itself holds nothing sensitive.

## Security notes

- Secrets live only in your browser's `localStorage`, AES-GCM encrypted. Losing the passphrase means losing the vault — keep an exported backup.
- The vault never syncs anywhere; each browser/device is its own vault (use Export/Import to move).
- Codes depend on an accurate system clock.
