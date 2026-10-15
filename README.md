# Orbit Music Player

A lightweight, dark-themed music player for listening to audio from **public MEGA folders** and working with **M3U/M3U8 playlists**.

**Website:** [Open Orbit Music Player]([YOUR_WEBSITE_URL_HERE](https://aswins404.github.io/music_player/))

Orbit runs in your browser and does not require you to sign in to a MEGA account. You provide a public folder link, and the player reads its contents using the MEGAJS browser client.

## Features

### MEGA music library
- Connect to a public MEGA folder by pasting its share URL.
- Discover supported audio files in the folder and its subfolders.
- Search tracks by filename or folder path.
- View file size separately from track length; length is shown when duration metadata is available.
- Play audio with play/pause, previous/next, seeking, volume, mute, shuffle, and repeat controls.
- See the current track and upcoming queue.
- Queue a track to play next, remove tracks from the queue, or play a collection from the beginning.
- Preload the next track after playback passes the halfway point when possible, so it may be ready sooner. Preloading downloads the full track into browser memory.
- Download the currently selected track to your device.
- Display available cover art when found, with Orbit's artwork placeholder as a fallback.

### Playlists
- Import `.m3u` and `.m3u8` playlist files from your device.
- Import playlists discovered inside a public MEGA folder. Choose which playlist to import; Orbit allows up to **two imported MEGA playlists at a time**.
- Keep the main **All MEGA Songs** collection separate from imported playlists.
- Match playlist entries to songs already found in MEGA, or select local audio files/folders with **Match local audio** to resolve local paths that the browser cannot access automatically.
- See unresolved playlist entries so you can identify paths that could not be matched.
- Edit an imported playlist by dragging tracks or using the move controls, and remove entries from the playlist without deleting the underlying audio files.
- Save an edited playlist to your device as an M3U/M3U8 file.
- Remove imported playlists from the player when they are no longer needed.

### Interface and accessibility
- Responsive layout for desktop and mobile, including a visible queue on mobile.
- Keyboard shortcuts:
  - **Space** — play or pause
  - **J** — previous track
  - **K** — next track
  - **/** — focus the search field
- Reduced-motion support for the scrolling current-track title.

## Supported audio formats

Orbit currently recognizes these filename extensions:

`mp3`, `m4a`, `aac`, `ogg`, `opus`, `wav`, `flac`, `oga`, `weba`, `webm`, `mp4`, `aif`, `aiff`, `mid`, `midi`, and `3gp`.

Actual playback depends on the browser's audio codec support.

## Run locally

Because the browser loads the MEGAJS client as a JavaScript module, run the project through a local web server instead of opening `index.html` directly with a `file://` URL. If Python is installed, open a terminal in the project folder and run:

```bash
python -m http.server 8000
```

Then open <http://localhost:8000> in your browser.

## Basic working

1. **Connect to a music folder:** Orbit loads the MEGAJS browser client and reads the public MEGA folder link you provide.
2. **Find audio files:** it scans the folder and its subfolders, then lists supported audio files in the collection.
3. **Play a track:** when you select a song, Orbit downloads and decrypts that file in the browser before playing it through the HTML audio player. This is download-before-playback, not progressive streaming.
4. **Manage playback:** use the player controls to pause, skip, seek, change volume, shuffle, repeat, and manage the upcoming queue. Orbit can begin downloading the next queued song after the current track passes 50% playback.
5. **Use playlists:** import M3U/M3U8 files from your device or choose a playlist found in a public MEGA folder. If playlist paths refer to files on your device, select **Match local audio** and choose the relevant audio files or folder.
6. **Edit and save:** reorder or remove playlist entries, then select **Save Playlist to Device** to export an edited M3U/M3U8 copy. These edits do not change the original MEGA folder.

## Using Orbit

1. **Load your music:** enter a public MEGA folder URL and click **Load folder**.
2. **Browse and search:** use **All MEGA Songs** or search by track name/folder path.
3. **Listen:** select a track and use the player controls. The queue shows upcoming tracks.
4. **Import a playlist:** choose **Add M3U/M3U8** for a playlist file, or expand **Import MEGA playlist** and select **Find playlists**. Choose the playlist you want to import.
5. **Resolve local paths if needed:** open the relevant device playlist, select **Match local audio**, and choose the matching audio files or folder. The browser only gets access to files you explicitly select.
6. **Edit and export:** open an imported playlist, select **Edit Playlist**, reorder or remove entries, then select **Save Playlist to Device** to keep a copy.
7. **Download a song:** select the track and use the download button in the player.

## Important notes and limitations

- **Use public MEGA share links.** Orbit is intended for publicly shared folders and does not sign in to your MEGA account.
- **The MEGA folder key is sensitive.** A public share URL may include the decryption key after `#`. Anyone with the complete link may be able to access the shared content. Avoid publishing private share links in source code, issues, screenshots, or public documentation. The default link shown in the app is a convenience and can be replaced with your own link.
- **Playback is not progressive streaming.** Orbit downloads and decrypts the selected song in the browser before playback. The next-track preload also downloads the full upcoming track. Large files can use substantial bandwidth and memory; the current implementation skips tracks above its 200 MB per-track limit.
- **Browser/CDN/network access is required.** The MEGAJS client is loaded from unpkg. If that service or MEGA is unavailable, folder loading may fail.
- **Local files remain local.** Selecting audio files for playlist matching does not upload them. Browser file access is granted only for the files you select and is not a permanent link to their original filesystem paths.
- **Playlist edits do not change the MEGA folder.** Editing a MEGA playlist changes the copy held by Orbit, not the source folder. Save the edited playlist to your device if you want to preserve the changes.
- **Playlist imports are session-based.** The current implementation keeps imported playlist state in the page session; export edited playlists if you need a copy to keep.
- **M3U entries may not all resolve.** Entries are matched against known MEGA tracks, selected local audio files, or supported HTTP(S) URLs. Paths that remain unmatched are shown in the interface.
- **Cover art may be unavailable.** If Orbit cannot find suitable artwork, it uses the built-in placeholder.
- **GitHub Pages is static hosting.** No backend server or database is included in this project.

## Project files

```text
.
├── index.html   # Page structure and player interface
├── script.js    # MEGA connection, playlist handling, playback, queue, and controls
├── style.css    # Theme, layout, responsive styling, and animations
└── README.md    # Project documentation
```

## Technologies

- HTML5
- CSS3
- Vanilla JavaScript (ES modules and browser APIs)
- [MEGAJS](https://github.com/meganz/MEGA.js), loaded from unpkg
- HTML audio element for playback

## Privacy and security

Orbit is a client-side static application. It does not include an Orbit backend or account system. Your browser communicates with MEGA and loads the MEGAJS library from the configured CDN. Audio data is downloaded to the browser for playback and may be held temporarily in memory for the current track or next-track preload.

Only use public share links and audio files you have permission to access. Do not commit private MEGA links or decryption keys to a public repository.
