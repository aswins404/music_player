# Orbit Music Player

A static, vanilla JavaScript music player for public MEGA folders. It is designed for GitHub Pages and does not need a server, MEGA account login, API secret, database, or build step.

## Files

- `index.html` — accessible interface and controls
- `style.css` — responsive dark theme
- `script.js` — folder enumeration, M3U matching, playback, and controls
- `tests/queue-state.test.js` — queue transition, preload race, M3U parsing, and link validation tests

## MEGA integration and playback behavior

Orbit uses the browser ES module build of [MEGAJS](https://mega.js.org/), pinned to `megajs@1.3.10` on unpkg. MEGAJS is an unofficial, open-source client. Its current documentation describes `File.fromURL()` for shared links, `loadAttributes()` for shared folder metadata, recursive directory children, and decrypted download streams. The app checks the expected `File.fromURL` export before enabling folder loading. If the CDN is blocked or the export changes, the page shows a visible error and leaves the rest of its interface responsive.

MEGA's encrypted download is not a direct audio URL. When you select a track, the browser downloads and decrypts that whole track, then creates a local Blob URL for the HTML Audio API. Playback starts only after decryption completes, the browser reports `canplay`, and the play request succeeds. Download progress is shown. The default client-side limit is 200 MiB per track to avoid unbounded memory use; this can be changed with `MAX_TRACK_BYTES` in `script.js`. Depending on the browser, Blob construction and playback may briefly need more than the track's file size in memory. Very large files should be downloaded through MEGA or played by a local/native client.

The browser may not decode every codec even if a file has a familiar extension. FLAC, OPUS, AAC, and other format support depends on the browser and operating system. A discovered file is only a candidate until it downloads, decrypts, decodes, and begins playback successfully.

The public folder URL and key are visible to anyone who can access this static website. A shared folder's key is already a bearer credential; do not use private or restricted material in a public player. This page asks for no MEGA account password and does not transmit the URL to an Orbit server. The third-party MEGAJS module is fetched from unpkg at runtime; users who need tighter supply-chain control can self-host the pinned browser module and its dependencies after reviewing MEGAJS's license and distribution.

## Use the player

1. Open the published GitHub Pages URL over HTTPS. For local testing, start a static HTTP server in this folder and open its localhost URL; browsers may block JavaScript modules or MEGA requests from `file://`.
2. Paste a public MEGA folder URL including its decryption key and choose **Load folder**. The supplied folder URL is prefilled.
3. **All MEGA Songs** is the independent main collection. To import a MEGA playlist, expand **Import MEGA playlist** and choose **Find playlists**. The player lists audio folders and M3U/M3U8 playlists found in the shared folder with their supported track counts. M3U entries are matched against both the imported folder and All MEGA Songs. Select **Import** beside each playlist you want; nothing is imported during discovery. Up to two MEGA playlists can be active; remove one from the import panel to make room.
4. Use **Add M3U/M3U8** to import device playlists. Matched entries preserve playlist order; ambiguous or unresolved local paths are listed. Choose **Match local audio** to grant the browser access to selected local files. Nothing is uploaded.
5. Each song has **+ Next**. The newest Play Next selection is placed first. Shuffle randomizes the upcoming list, a second press reshuffles it, and **Off** restores sequential order while keeping protected Play Next entries at the front. The next complete track is downloaded and prepared after playback reaches 50% when possible.
6. Use **Edit Playlist** for move-up/down controls or drag-and-drop, then **Save Playlist to Device** to download an edited M3U8. Editing never changes the MEGA folder. The player can also download the current track with ⇩.
7. Keyboard shortcuts: Space toggles play/pause, J/K move to previous/next, and `/` focuses search (shortcuts are ignored while editing a field).

## Publish with GitHub Pages

1. Upload `index.html`, `style.css`, `script.js`, and `README.md` to the root of a GitHub repository.
2. In the repository, open **Settings → Pages**.
3. Under **Build and deployment**, select **Deploy from a branch**, choose the branch containing the files and the `/ (root)` folder, then save.
4. Wait for GitHub Pages to publish and open the provided site URL over HTTPS.

No GitHub Actions workflow, package installation, or server-side configuration is required. The page depends on the unpkg CDN and MEGA's public-link APIs, so network access or service policy changes can prevent folder loading. GitHub Pages cannot proxy those requests if browser cross-origin restrictions or MEGA service changes block them; a small server-side proxy would then be needed, but would have to be operated and secured separately.

## Troubleshooting

- **MEGA client/CDN error:** check the browser console and network access to `unpkg.com`, then press **Load folder** to retry. The page pins the library version in `script.js` and tries the documented UMD build if the ES module is unavailable.
- **Invalid or inaccessible link:** use a complete public folder link from MEGA, include the key after `#`, check that the folder is still shared, and use a folder (not a single-file) link.
- **Folder loads with no songs:** check that supported audio files exist in the shared folder or its subfolders. The extension filter is in `AUDIO_EXTENSIONS` in `script.js`.
- **A track fails:** inspect the visible error and browser console. MEGA may deny a request, the link key/file may be invalid, the network may fail, the file may exceed the memory limit, or the browser may not decode its codec.
- **M3U shows unmatched entries:** use paths relative to the MEGA folder where possible. If only filenames are present, duplicate filenames cannot be safely guessed and are reported unmatched.
- **Browser memory on large audio:** tracks are downloaded and decrypted fully before playback. Use smaller files or a native MEGA client for large tracks.
- **Cross-origin restrictions:** GitHub Pages cannot configure MEGA's response headers. If MEGA's public endpoints stop allowing the browser requests needed by MEGAJS, pure static hosting will no longer be sufficient; use a carefully secured proxy or download files to another static host that supports byte-range audio requests.

## Tests and verification

Run the dependency-free checks with:

```sh
node --check script.js
node --test tests/queue-state.test.js
```

The queue tests cover Play Next priority ordering, shuffle enable/reshuffle/off transitions, actual next selection, the 50% preload threshold, and pressing Shuffle while the current preload is in flight (including the case where shuffle keeps the same next track). They also check prepared-Blob reuse, EXTINF parsing, duplicate filenames with distinct paths, invalid MEGA links, MEGA playlist preview/confirmation, duplicate-import prevention, the two-folder limit, and app initialization. Real MEGA folder access, browser codec support, file-picker permissions, and actual browser-initiated downloads depend on the user's browser/network and should be checked manually after deployment.

## License

Orbit's source files are provided for personal use and modification. MEGAJS is a separate MIT-licensed dependency; see its upstream repository for its license and terms.
