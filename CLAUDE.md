# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Browser-based teleprompter app for social media recording. Open `index.html` directly in Chrome — no build step, no server.

**Core behaviour:**
- Displays a scrollable script with the current word highlighted
- Shows the speaker's webcam face in a draggable picture-in-picture overlay
- Uses the Web Speech API to track position in the script as the speaker reads
- **Auto-pauses the recording** (via `MediaRecorder.pause()`) when silence is detected or the speaker stumbles — the webcam overlay shows a ⏸ veil while paused
- Resumes automatically when speech is detected again, or manually via the Pause/Resume button
- Exports the finished take as a `.webm` file

## Running / Developing

No install required. Open `index.html` in Chrome (Chrome has the best Web Speech API support):

```bash
open index.html          # macOS
xdg-open index.html      # Linux
# or drag the file into Chrome
```

For live-reload during development:

```bash
npx serve .              # or any static file server
```

> The Web Speech API requires HTTPS or `localhost`. Opening the file via `file://` works in Chrome but may fail in other browsers.

## Architecture

Everything lives in a single `index.html` — HTML, CSS, and JS are co-located intentionally so the file can be shared or opened directly without a build step.

**Key pieces inside the `<script>` block:**

| Symbol | Role |
|---|---|
| `S` (state object) | Single source of truth — cursor position, recording flags, stream/recorder handles |
| `initCamera()` | Acquires `getUserMedia` stream (video+audio), feeds it to the `<video>` element and later to `MediaRecorder` |
| `initSpeech()` | Boots `SpeechRecognition` in continuous mode; wires `onresult` → `handleTranscript`, `onspeechend` → silence timer |
| `handleTranscript(text)` | Fuzzy-matches spoken words against `S.words[]`; advances `S.cursor`; marks spoken/mistake classes on `<span>` elements |
| `armSilence(ms)` / `autoPause()` | Silence timer — if no speech arrives within the window, calls `S.recorder.pause()` and shows the veil |
| `togglePause()` / `startRecording()` / `stopRecording()` | Recording lifecycle; `buildDownload()` assembles the `Blob` on stop |
| `moveCursor(idx)` | Applies `current` CSS class and smooth-scrolls the teleprompter to keep the active word at ~42% from the top |

**Word tracking flow:**
```
SpeechRecognition.onresult
  → handleTranscript(finalTranscript)
    → fuzzy-match each spoken word against S.words[cursor … cursor+5]
    → moveCursor(newIdx)
      → scroll teleprompter
      → if silence timer fires → autoPause() → MediaRecorder.pause()
      → on next speech → resumeRecording() → MediaRecorder.resume()
```

## Conventions

- All state is on the single `S` object — never add module-level mutable variables outside it.
- CSS classes on word `<span>` elements are the source of truth for visual state: `w`, `w spoken`, `w current`, `w mistake`.
- Silence threshold is `1600 ms` (regular) / `900 ms` (after `onspeechend`). Adjust `armSilence(ms)` call sites to tune sensitivity.
- `bestMime()` picks the recording format — prefer `vp9,opus` for quality. Output is always `.webm`.

## gstack (REQUIRED — global install)

**Before doing ANY work, verify gstack is installed:**

```bash
test -d ~/.claude/skills/gstack/bin && echo "GSTACK_OK" || echo "GSTACK_MISSING"
```

If GSTACK_MISSING: STOP. Do not proceed. Tell the user:

> gstack is required for all AI-assisted work in this repo.
> Install it:
> ```bash
> git clone --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack
> cd ~/.claude/skills/gstack && ./setup --team
> ```
> Then restart your AI coding tool.

Do not skip skills, ignore gstack errors, or work around missing gstack.

Using gstack skills: After install, skills like /qa, /ship, /review, /investigate,
and /browse are available. Use /browse for all web browsing.
Use ~/.claude/skills/gstack/... for gstack file paths (the global path).
