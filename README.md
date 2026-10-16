# audioswirl — A Looping Read-Along Player

A mobile-friendly, single-page auditory-training player for practicing similar-sounding consonants and vowels. Choose an exercise, watch each word highlight while it is spoken, or switch to audio-only looping for listening with your iPhone locked.

The app also keeps **per-exercise playback totals**, **completed-loop totals**, and two timestamped logs on your device.

## Exercises

| Exercise | Example minimal pairs |
| --- | --- |
| **M / N** | May / Nay, Me / Knee, Map / Nap |
| **D / G** | Day / Gay, Date / Gate, Dig / Gig |
| **K / T** | Key / Tea, Cap / Tap, Coat / Tote |
| **B / V** | Ban / Van, Bat / Vat, Best / Vest |
| **F / V** | Fan / Van, Fine / Vine, Fat / Vat |
| **O / A** | Hot / Hat, Cot / Cat, Lock / Lack |
| **E / A** | Bed / Bad, Pen / Pan, Men / Man |
| **TH / F** | Thin / Fin, Think / Fink, Three / Free, Thought / Fought |

Each exercise contains eight word pairs, played in order with approximately half-speed speech and one-second pauses. The **All 8 videos** option plays the complete sequence and repeats it.

> **Pronunciation note:** O / A and E / A compare the short vowel sounds used in the listed example words. TH / F practices the voiceless **th** sound /θ/ (as in *thin*) against /f/ (as in *fin*).

## Features

- **One-tap exercise selection:** Buttons for all eight sound contrasts plus the full sequence.
- **Watch + Listen:** On-screen words highlight as the recording plays.
- **Background Listening:** Audio-only playback with native looping, which can continue when iOS permits the web app to play audio in the background.
- **Exercise totals at the top:** Each exercise shows how many times it has started playing and how many of its solo loops were completed.
- **Lifetime counters:** Tracks exercise starts, completed video loops, and complete *All 8* sequence loops.
- **Two logs:** Timestamped exercise-start events and completed-loop events.
- **Export and backup:** Two CSV exports and a JSON backup/restore option.
- **No account or database required:** Playback logs are stored locally, not uploaded to a server.

## Run or host the app

The deployable app is **`index.html`**. It embeds the video and audio media within the file, so there are no separate media files to upload and no build step.

### Recommended: Netlify

1. Make a backup of your existing training logs (see [Back up your history](#back-up-your-history)).
2. Sign in to [Netlify](https://app.netlify.com/).
3. Create a site using Netlify's manual deploy workflow, or open your **existing** Netlify site's **Deploys** section to publish an update.
4. Upload a folder containing `index.html` at its top level. If you have a ZIP file, unzip it first and upload the extracted folder. You can include this `README.md` in that folder, but it is not required to run the player.
5. Open your site's `https://…netlify.app` URL in Safari.
6. To make it feel like an app: tap **Share → Add to Home Screen**, then launch it from the Home Screen icon.

**To keep existing history:** Update the **same Netlify site and URL**, and continue using the same Home Screen app. Different URLs, Safari tabs, and Home Screen web apps can have different local storage. Back up first even when redeploying to the same URL.

### Local use

`index.html` can also be opened on a computer in a modern browser. On iPhone, opening an HTML file from **Files** may show a non-interactive preview; hosting the page and opening it in Safari is more reliable.

## Using the player

1. Open the website or your Home Screen app.
2. Tap **M / N**, **D / G**, **K / T**, **B / V**, **F / V**, **O / A**, **E / A**, or **TH / F** to load and play that exercise only. Tap **All 8 videos** to play everything in sequence.
3. Select **Watch + Listen** to view highlighted words. Choose **Background Listening** to play an audio-only loop.
4. If iOS doesn't start playback automatically, tap **Play** in the media controls.
5. Tap another exercise to switch immediately. The selected recording repeats until you change it or pause it.

When using Background Listening, try locking your iPhone after playback has started. Whether playback continues depends on iOS and the device's media policies.

## How logging works

### Log 1 — Individual exercise plays

Records a row when an exercise starts playing. Fields in **`exercise_play_log.csv`**:

| Field | Meaning |
| --- | --- |
| `timestamp` | Event time in ISO 8601 format |
| `localTime` | Human-readable date and time on the device |
| `exercise` | Exercise played, such as `M/N` or `TH/F` |
| `pass` | Playback pass number within the current app session |
| `atVideoSecond` | Position within the selected recording at the event |
| `selectedVideo` | The chosen solo exercise or full sequence |

For the full-sequence selection, starts of individual exercises may be logged as the full recording progresses. A **play** does not mean a complete viewing or a correct answer.

### Log 2 — Completed video loops

Records a row when the selected media completes a loop **and the player has observed at least approximately 90% of its duration playing**. Fields in **`lifetime_loop_log.csv`**:

| Field | Meaning |
| --- | --- |
| `timestamp` | Completion time in ISO 8601 format |
| `localTime` | Human-readable local completion time |
| `loopNumber` | Cumulative recorded completion number |
| `video` | Solo exercise or `All 8` |
| `secondsPlayed` | Approximate playback coverage observed by the player |

The **Full-sequence loops** counter counts completed *All 8* cycles; solo-loop totals belong to their individual exercise buttons.

**Background accuracy limitation:** iOS can suspend webpage JavaScript while audio continues. The player may therefore **undercount starts or loops during locked-screen playback**. The totals represent **recorded events**, not a guaranteed count of every audio repetition. Playback through another app is never recorded here.

## Where history is saved

The app stores its logs in your browser/Home Screen web app's `localStorage` under this key:

```text
cochlear-readalong-tracker-v1
```

The key is shared with earlier versions of this player to preserve compatible logs. The app does **not** send training history to Netlify, ChatGPT, or a cloud database. Opening a different website address or browser context may show a different history, and clearing website data can erase it.

## Back up your history

In **Your data and backups**:

- **Export exercise CSV:** Download the complete exercise-start log.
- **Export loops CSV:** Download the complete completed-loop log.
- **Backup both logs (JSON):** Save `training_tracker_backup.json`, which includes both logs and can be restored later.
- **Restore backup:** Choose a previously saved JSON file. **Restoring replaces the app's current logs**, rather than merging them.
- **Clear logs:** Erases the history currently stored in this browser/app. Export a backup first.

**Recommended practice:** Make a JSON backup before publishing a new version, removing or reinstalling your Home Screen app, switching devices, or clearing Safari website data. Keep occasional backups in iCloud Drive or another location you control.

## Troubleshooting

| Issue | What to try |
| --- | --- |
| Exercise button doesn't start audio/video | Tap the media **Play** control once; iOS may require an explicit user action. |
| Background audio stops when the screen locks | Use **Background Listening** instead of **Watch + Listen**; start playback before locking the phone. iOS may still interrupt web playback. |
| Exercise totals seem to reset | Open the **same Home Screen icon** and website URL. Check whether browser storage is enabled; restore your saved JSON backup if needed. |
| New exercise button doesn't appear after redeployment | Confirm you uploaded the newest `index.html` to the correct Netlify site, then reopen the app. If needed, refresh the hosted page in Safari. |
| Logs miss some repetitions | Background JavaScript is not guaranteed to run while iOS is locked; recorded loop counts can be lower than actual audio loops. |
| Local HTML file displays without working buttons | Host the page, then open it in Safari. |

## Project files

```text
index.html    # Entire eight-exercise player, including embedded media and logging
README.md     # This guide (optional for hosting)
```

The MP4/M4A recordings can also be retained separately for archival or editing, but **are not required** to host this self-contained `index.html` version.

## Privacy and scope

This is a personal auditory-practice tool, not a clinical hearing test. It logs playback activity; it does not score speech recognition, identify whether an answer was correct, or sync history between devices. Use the export and backup controls to preserve a long-term practice record.
