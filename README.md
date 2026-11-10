# Job Photo Shrinker

A small iPhone web app that shrinks a job's photos and sends them through Gmail as a PDF. Each email has one photo per page and stays under Gmail's size limit.

**Live app:** https://rnmercer1225.github.io/job-photo-shrinker/ (open in Safari on an iPhone)

Everything runs on the phone. Photos are never uploaded anywhere. The app is plain HTML, CSS and JavaScript with no build step and no libraries.

## What's in this folder

| File | What it is |
| --- | --- |
| `index.html` | The whole app: the page, its styles and its script. Almost every tweak happens here. |
| `manifest.json` | The name and colors used when the app is added to the home screen. |
| `icon-180.png`, `icon-192.png`, `icon-512.png` | The home-screen icon at three sizes. |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are. Leave it alone. |

## Common tweaks

The easiest way to find each setting is to search `index.html` for the text in the second column.

| To change | Search `index.html` for | Notes |
| --- | --- | --- |
| Photo sharpness and file size | `const LEVELS` | `edge` is the longest side in pixels. `q` is JPEG quality (0 to 1). Three levels: `max`, `sharp`, `small`. |
| Which detail level is the default | `id="q-sharp"` | Move the `checked` attribute to another of the three radio buttons. |
| Maximum size of one email | `BUNDLE_LIMIT` | In bytes. It's 20 MB now, leaving room under Gmail's 25 MB limit. Don't go above about 23e6. |
| Largest size of a single photo | `6e6` | Photos bigger than 6 MB get re-saved at lower quality. |
| PDF page size | `842 /` | The long side of each page in points (842 is A4 height). |
| Headings and instructions | The words themselves, e.g. `Choose photos`, `Ready to send`, `How it works` | All wording is plain text in the page or in short strings in the script. |
| Colors | `--accent:` | Colors are defined once at the top of the styles. Dark mode has its own copy just below; change both. |
| Note text printed under photos | `function layoutCaption` | Font size, padding and strip color. |
| Dictation language | `r.lang` | Uses the phone's language by default. Set it to a fixed value such as `"en-US"` if needed. |
| App name on the home screen | `apple-mobile-web-app-title` in `index.html`, and `short_name` in `manifest.json` | Change both. |

## Testing a change before it goes live

Anything saved to the `main` branch goes live within a minute or two, and that's the address the manager uses. To avoid breaking his app:

1. **Try it on a computer first.** Download the files and open `index.html` in a browser. Choosing photos, shrinking and adding notes all work there. Depending on the browser, **Send** either downloads the PDF or opens the computer's own share menu.
2. **Try it on an iPhone without touching the live app.** Fork this repository and turn on GitHub Pages for your fork (**Settings → Pages**, branch `main`, folder `/ (root)`). Your fork gets its own address to test on.
3. **Bring the change back.** Open a pull request from your fork. Once it's merged into `main`, the live app updates.

After an update, the iPhone may show the old version for a short while. Close the app fully and open it again, or reload the page in Safari.

## How it works (for code changes)

- **Shrinking:** `shrink()` draws each photo onto a canvas at the chosen size, halving in steps so small text stays crisp, and saves it as a JPEG. Drawing to a canvas also removes location data.
- **Grouping:** `makeBundles()` fills each email up to `BUNDLE_LIMIT`.
- **PDF:** `makePdf()` writes a minimal PDF by hand with one page per photo. The JPEG is embedded as it is, with no re-compression.
- **Sending:** `save()` uses the browser's share menu (`navigator.share`) when it can share files, which is what opens Gmail on the iPhone. Otherwise it downloads the file. The share call has to happen straight from the tap, so don't add any `await` before it in `sendBundle()`.
- **Dictation:** the Speak buttons use the browser's built-in speech recognition (`SpeechRecognition`). They hide themselves where it isn't available. The iPhone keyboard's mic key always works as a backup.
- **Memory:** iPhones are strict about memory, so the code reuses a few canvases and keeps only small thumbnails on screen. Keep that in mind before adding full-size previews.
