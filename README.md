# hidden-tabs-keeper

A bookmark-driven tool for saving and restoring Chrome **incognito** tabs through an encrypted file — so a private browsing session (planning a surprise trip, gift research, etc.) can be paused and resumed later without leaving a trace in normal history, sync, or session restore.

## The problem

Incognito mode has no session restore, no sync, and clears `localStorage` when the last incognito window closes. If you've collected a dozen tabs while planning something secret and need to step away, your only options are: leave the window open indefinitely, or lose the work. Saving them as regular bookmarks defeats the whole point.

## The idea

1. Click a bookmark in incognito → captures open tabs into a passphrase-encrypted file you download.
2. Later, open a fresh incognito window → load the encrypted file → enter your passphrase → all tabs reopen.

The encrypted file on disk is the only artifact. Without the passphrase it's unreadable.

## Approach (planned)

- **Vault page** hosted on GitHub Pages (single static HTML/JS file). Works fully client-side — no server, no telemetry.
- **Encryption:** Web Crypto API, AES-GCM with a key derived from the passphrase via PBKDF2 (high iteration count). Random salt + IV per file.
- **Capture flow:** because a bookmarklet can only see the *current* tab, each tab is added one click at a time. Click the bookmarklet on each tab → it opens the vault page with that URL appended → vault accumulates URLs in memory → "Save" produces an encrypted `.htk` file download.
- **Restore flow:** open vault page → upload `.htk` file → enter passphrase → "Open all" launches each saved URL in a new tab.
- **No persistence inside incognito** — the encrypted file on the user's filesystem is the source of truth.

## Status

Repo scaffold only. Implementation pending.

## Security notes

- Passphrase strength is the only thing standing between an attacker (with the file) and your tabs. Use a long passphrase.
- Browser extensions in incognito (if you've allowed any) can read page contents — including the vault page after decryption. Don't enable extensions in incognito if you care about this.
- `.htk` files are obviously encrypted blobs; their existence is detectable, just not their contents.
