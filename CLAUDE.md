# Tally

Private, PIN-locked personal tracker. Data is encrypted on the device (PBKDF2 + AES-GCM) and an encrypted copy syncs to Firebase (`tally-85dd6`). `backup.html` is the backup/restore page.

Live: https://bengalebg23.github.io/Tally/

## How Ben works
- Ben works from his phone. He asks for changes in chat and Claude pushes them straight to the deploy branch. There's no local build and no pull-request review.
- Pushing to `main` deploys via GitHub Pages within a minute or two.
- The repo is **public**. Never commit secrets, API keys meant to stay private, family photos or personal data.

## Every release
- Bump `VERSION` and the `<title>` in `index.html` and `CACHE` in `sw.js` together.
- Keep `HANDOFF.md` up to date (version, what changed, open threads) if the repo has one.
- After pushing, give Ben the live URL and the new version number so he can check it on his phone.

## Code rules
- Single-file app: keep HTML, CSS and JS in `index.html`. No build step, bundler or npm packages.
- Pin exact CDN versions (no `@latest` or floating majors). A floating Babel major broke an app before.
- Self-review before pushing: duplicate `const`/`let` in one scope, variable shadowing inside loops, unclosed template literals, undefined references.
- Defer re-renders triggered inside click handlers with `setTimeout(..., 0)` to avoid detached-node bugs from bubbled events.
- Don't swallow Firebase errors with a bare `console.warn`. Surface them so permission problems are visible.
- UK English in all user-facing text.
- Bulk Firebase writes: use direct awaited `set()` calls. Debounced save helpers only keep the last write in a tight loop.
- Never let a fresh or cleared device overwrite cloud data on first load. Read the cloud first, merge, then write.

## This app
- Privacy comes first. Never log, display or commit decrypted data, PINs or keys, and never weaken the encryption or the wrong-PIN lockout.
- **Timeline depends on Tally's crypto.** It reads `tally.vault`/`tally.blob` and the Firebase vault copy read-only on the same origin. If you change the key derivation, vault format or storage keys, update `bengalebg23/Timeline` at the same time.
- Setting the Firebase config to `null` runs Tally on the device only.
