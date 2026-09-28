# SwingSync Releases
Repo for public releases

SwingSync helps you find, review, and save the musical tempo of your music
collection. It combines acoustic BPM estimates with Swing and Rhythm & Blues
profiles, then lets you choose the pulse that matches how you count the song.

The desktop app runs on Windows and macOS and includes English and Argentine
Spanish interfaces. A command-line interface is also available.

## Features

- Analyze folders of MP3, WAV, FLAC, M4A, AAC, and OGG audio.
- Compare the detected tempo with a profile-based musical interpretation.
- Review suggestions individually or approve them in a batch.
- Open tracks in your default music player, tap the beat, or enter a custom BPM.
- Write BPM metadata, add a filename prefix such as `[178 BPM]`, or do both.
- Reuse acoustic analysis from cache, ignore it for a fresh run, or clear it
  for selected folders.
- Create a backup before applying changes and undo the last backed-up Apply.
- Choose light/dark themes, text size, color-vision settings, and reduced motion.

## Run a packaged app

Packaged builds include Electron, the analysis engine, and FFmpeg. You do not
need Node.js or npm to run them.

| Platform | Package | How to run |
| --- | --- | --- |
| Windows x64 | Portable `.exe` | Open the executable. |
| macOS Apple Silicon | `arm64.dmg` | Open the DMG, drag SwingSync into Applications, then open it. |
| macOS Intel | `x64.dmg` | Open the DMG, drag SwingSync into Applications, then open it. |

Current test builds are unsigned. See [Troubleshooting](#troubleshooting) if
your operating system asks you to approve the app.

## Use SwingSync

1. **Library:** choose your music folders, profile, and output mode.
2. **Analyze:** let SwingSync estimate each track's BPM.
3. **Review:** compare detection and suggestion. Play the track in your default
   player, use Tap Tempo, choose a custom value, or skip it.
4. **Apply:** inspect the proposed changes. Enable **Create backup before
   modifying files** if you want to be able to undo this batch, then confirm.
5. Use **Undo last Apply** to restore the last backed-up batch when needed.

Analysis and review do not modify your audio files. Applying metadata or
filename changes does. Keep your own music-library backup as well.

### Choose a profile

| Profile | Behavior |
| --- | --- |
| Swing | Uses rhythmic evidence to consider half/double-time and supported 3:2 interpretations. This is the desktop's first-run default. |
| Rhythm & Blues | Applies its own rhythmic evidence thresholds. |
| Generic | Preserves the detector's preferred metrical level. |

The desktop remembers your last profile choice. The CLI defaults to
`rhythm-and-blues`; pass `--profile swing` explicitly when desired.

The acoustic detector searches 50–240 BPM. Supported Swing interpretations
and human review can reach 360 BPM. Tempo can be ambiguous: a faster suggestion
still needs to match the musical pulse you hear.

### Choose an output

- **Metadata:** write a BPM tag. Supported writing formats are MP3, FLAC, OGG,
  and M4A; FFmpeg copies the audio stream without re-encoding it.
- **Filename:** normalize the BPM prefix, for example `[178 BPM] Song.mp3`.
- **Both:** update metadata and the filename.

Analysis support and metadata-writing support differ. Use Filename for
formats without a supported BPM-tag writer.

### Work with the cache

Normal analysis reuses saved acoustic features when possible. Profile
interpretation is recalculated from those features.

- **Ignore cached analysis:** force a fresh acoustic analysis for that run.
- **Clear cache for selected folders:** remove cached analysis under those roots.
- Reset a track to Pending when you want it back in the analysis workflow.

Clearing the analysis cache does not delete music files. Saved review choices
are separate; clear or replace a choice if you want to revisit it.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| macOS cannot verify the developer | For your trusted test build, try opening it, then use System Settings → Privacy & Security → Open Anyway. See [Apple's instructions](https://support.apple.com/en-us/102445). |
| Windows shows SmartScreen | Test releases are unsigned and may show a reputation warning. Verify the package source and checksum before deciding whether to run it. |
| Mac shows an old/default icon | Quit SwingSync, eject old DMGs, replace the app in Applications with the new build, and open that copy. Remove an old Dock shortcut and add the new app if needed. See [macOS icon checks](DESKTOP.md#macos-icon-checks). |
| Desktop reports missing fonts | Run the three font installers and `npm run desktop:font:check`. |
| Install reports an unapproved script | The package explicitly allows the pinned FFmpeg, electron-winstaller, and fsevents scripts. Check the exact package/version; do not approve every dependency script blindly. |
| FFmpeg cannot start after moving the project | Extract fresh source and run `npm install` on the destination OS/architecture. Do not transfer `node_modules` from another platform. |
| A song is detected at half or double tempo | Check the profile, listen and tap the beat, and choose a custom BPM if needed. Use Ignore cached analysis when comparing fresh detector results. |
| Review rejects a value | Custom values must be numeric and between 50 and 360 BPM. |
| A previous review choice is still selected | Clear the review decision or replace it; clearing acoustic cache does not clear human choices. |
| Apply cannot write or rename a track | Check folder permissions, whether another program has the file open, the proposed filename, and whether the output format is supported. |
| Undo is unavailable | Undo requires a backup created before the last Apply. It is not a replacement for a library backup. |

When reporting an issue, include the SwingSync version, OS and architecture,
profile, exact error, detected/suggested BPM, and whether cached analysis was
ignored. Include the analysis evidence or a reproducible test recording when
you can share it.