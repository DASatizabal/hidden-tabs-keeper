# hidden-tabs-keeper

Save Chrome **incognito** tabs to a passphrase-encrypted file, and restore them later in a fresh incognito window. No server, no sync, no extension — just a static page and a bookmarklet.

**Live vault:** https://dasatizabal.github.io/hidden-tabs-keeper/

## Why

Incognito has no session restore, no sync, and clears `localStorage` when the last incognito window closes. If you've collected a dozen tabs while planning something private (a surprise trip, gift research) you either keep the window open forever or lose the work. Saving as regular bookmarks defeats the point.

Hidden Tabs Keeper writes one artifact: an encrypted `.htk` file on your disk. Without the passphrase it's an opaque blob.

## How it works

- A static page (`index.html`) hosted on GitHub Pages is the **vault**.
- A **bookmarklet** captures the URL of the tab you click it on by opening the vault page with `?add=<url>`. The vault appends to a list in `localStorage` and auto-closes the helper tab.
- When you're done collecting, open the vault page directly, enter a passphrase, and download an encrypted `.htk` file.
- Later, open the vault in a fresh incognito window, upload the `.htk`, enter the passphrase, and click **Open all in new tabs**.

Encryption: **AES-GCM 256** with a key derived from your passphrase via **PBKDF2-SHA-256, 250 000 iterations**. Random 16-byte salt and 12-byte IV per file. File format: `HTK\x01 || salt || iv || ciphertext+tag`.

## Setup (one time)

1. Open https://dasatizabal.github.io/hidden-tabs-keeper/ in a normal Chrome window.
2. Drag the **+ Save tab to Hidden Vault** button to your bookmarks bar.
3. Bookmark the page itself too (Ctrl+D), so you can reopen the vault from incognito to save / load.
4. Make sure your bookmarks bar is visible in incognito (Ctrl+Shift+B in an incognito window).

## Saving a session

1. Open incognito. Browse / collect your tabs as normal.
2. On each tab you want to keep, click the **+ Save tab to Hidden Vault** bookmarklet. A helper tab flashes open ("Tab saved") and closes itself.
3. When done, open the vault bookmark, enter a passphrase twice, click **Encrypt & download**. A `vault-<timestamp>.htk` file is downloaded.
4. Move the file somewhere you'll find it later. Close incognito.

## Restoring a session

1. Open a fresh incognito window. Open the vault bookmark.
2. Pick your `.htk` file, enter the passphrase, click **Decrypt**.
3. Click **Open all in new tabs**. (Allow popups when Chrome asks the first time.)

## Security notes

- **The passphrase is the only thing protecting your file.** Use something long. If you forget it, the file is unrecoverable by design.
- Files are obviously encrypted blobs; their *existence* is detectable, just not their contents.
- Browser extensions allowed in incognito can read pages — including the decrypted list on the vault page. Don't run extensions in incognito if that matters to you.
- The vault is purely client-side: no network calls except loading the static page. Verify with DevTools → Network.
- `localStorage` on the vault page persists across tabs *within one incognito session* and is cleared when the last incognito window closes — that's expected.

## Files

- `index.html` — the entire vault app (HTML + CSS + JS, no build step).
- `.gitignore` — excludes `*.htk` so real vault files never get committed.

## Status

v1. Working bookmarklet + encrypt/decrypt round trip. Per-tab clicking is a Chrome bookmarklet limitation; a real extension could capture all tabs in one click but needs separate install + "Allow in incognito" toggle.
