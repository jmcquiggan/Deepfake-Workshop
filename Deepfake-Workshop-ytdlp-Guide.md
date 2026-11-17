# Installing and running yt-dlp

**Lab reference — ISC2 Central Florida Chapter**
*Ripping video and audio separately for the Don't Believe Your Eyes labs*

> **Scope — read before you run anything**
>
> Use this only on the consented persona pack provided in class, or on recordings of yourself. Not on public figures, not on coworkers, not on anyone who has not agreed. Pulling media you have no right to use is a separate problem from the one this workshop is teaching you to solve.

**Contents**

1. [Install yt-dlp](#1-install-yt-dlp)
2. [Install ffmpeg](#2-install-ffmpeg--this-is-not-optional-for-these-labs)
3. [Verify both are working](#3-verify-both-are-working)
4. [See what is available before you download](#4-see-what-is-available-before-you-download)
5. [Rip the video](#5-rip-the-video)
6. [Rip the audio as WAV](#6-rip-the-audio-as-wav)
7. [Grab only the clean section](#7-grab-only-the-clean-section)
8. [Metadata, without downloading the media](#8-metadata-without-downloading-the-media)
9. [When it does not work](#9-when-it-does-not-work)
10. [Command cheat sheet](#command-cheat-sheet)

---

## 1. Install yt-dlp

Pick **one** method for your platform. The standalone binary is the simplest — it is a single file with nothing to configure.

### Windows

**Option A — package manager (easiest)**

```powershell
winget install yt-dlp
```

**Option B — standalone binary**

1. Download `yt-dlp.exe` from <https://github.com/yt-dlp/yt-dlp/releases/latest>
2. Create a folder such as `C:\Tools\yt-dlp\` and put the `.exe` in it.
3. Add that folder to your PATH: Start → "Edit the system environment variables" → Environment Variables → select Path → Edit → New → paste the folder path.
4. Close and reopen your terminal so the new PATH takes effect.

### macOS

**Option A — Homebrew**

```bash
brew install yt-dlp
```

**Option B — standalone binary**

```bash
mkdir -p ~/.local/bin
curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp_macos -o ~/.local/bin/yt-dlp
chmod a+rx ~/.local/bin/yt-dlp
```

If the command is not found afterwards, `~/.local/bin` is not on your PATH — see [the troubleshooting table](#9-when-it-does-not-work).

### Linux

```bash
mkdir -p ~/.local/bin
curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o ~/.local/bin/yt-dlp
chmod a+rx ~/.local/bin/yt-dlp
```

### Any platform — via pip

Needs Python 3.11 or newer.

```bash
python3 -m pip install -U "yt-dlp[default]"
```

On Windows use `py` or `python` in place of `python3`.

---

## 2. Install ffmpeg — this is not optional for these labs

yt-dlp downloads streams. ffmpeg is what merges a separate video and audio stream into one file, and what converts audio into WAV. Without it you cannot produce the files the voice-cloning lab needs.

> **A common mistake**
>
> You need the ffmpeg **binary**, not the Python package of the same name. `pip install ffmpeg` does not give you what you need.

| Platform | Command or source |
|---|---|
| Windows | `choco install ffmpeg` — or download a build from [FFmpeg-Builds](https://github.com/yt-dlp/FFmpeg-Builds/releases) and put `ffmpeg.exe` in the same folder as `yt-dlp.exe` |
| macOS | `brew install ffmpeg` |
| Linux (Debian/Ubuntu) | `sudo apt install ffmpeg` |
| Linux (Fedora) | `sudo dnf install ffmpeg` |
| Linux (Arch) | `sudo pacman -S ffmpeg` |

If ffmpeg is installed somewhere yt-dlp cannot find, point at it directly with `--ffmpeg-location /path/to/ffmpeg`.

---

## 3. Verify both are working

```bash
yt-dlp --version
ffmpeg -version
```

Each should print a version number. If either says "command not found", it is a PATH problem, not a broken install.

**Update before you start.** Sites change constantly and most extraction failures are fixed by running the latest build:

```bash
yt-dlp -U
```

If you installed through pip, re-run the pip install command instead.

---

## 4. See what is available before you download

This lists every video and audio stream the source offers, with a format ID for each. Worth doing once so you understand what you are choosing between.

```bash
yt-dlp -F "URL"
```

You will see video-only rows, audio-only rows, and combined rows. The labs need the first two **separately**.

---

## 5. Rip the video

**Video with its audio, merged — your source clip for lip-sync**

```bash
yt-dlp -f "bv*+ba/b" -o "target_full.%(ext)s" "URL"
```

`bv*` = best video, `ba` = best audio, `/b` = fall back to a combined stream if the merge is not possible.

**Video only, no audio track**

```bash
yt-dlp -f "bv*" -o "target_video.%(ext)s" "URL"
```

---

## 6. Rip the audio as WAV

This is the file the voice-cloning lab consumes. WAV is uncompressed, so nothing is lost before the model hears it.

```bash
yt-dlp -x --audio-format wav --audio-quality 0 -o "target_audio.%(ext)s" "URL"
```

`-x` extracts audio and discards the video. `--audio-quality 0` is the best available.

> **What makes a good reference clip**
>
> Thirty to sixty seconds of one person speaking, with no music bed, no second voice, and no applause. A longer noisy sample produces a worse clone than a short clean one. Cut to the clean section rather than taking the whole file.

---

## 7. Grab only the clean section

Rather than downloading an hour and trimming by hand, pull just the span you want. Times are `HH:MM:SS`.

**macOS / Linux**

```bash
yt-dlp -x --audio-format wav --audio-quality 0 \
  --download-sections "*00:01:12-00:01:52" \
  --force-keyframes-at-cuts \
  -o "reference_clip.%(ext)s" "URL"
```

**Windows** — same command on one line, without the backslashes:

```powershell
yt-dlp -x --audio-format wav --audio-quality 0 --download-sections "*00:01:12-00:01:52" --force-keyframes-at-cuts -o "reference_clip.%(ext)s" "URL"
```

**Optional clean-up.** If the clone sounds wrong, converting the reference to single-channel audio sometimes helps:

```bash
ffmpeg -i reference_clip.wav -ac 1 -ar 24000 reference_mono.wav
```

---

## 8. Metadata, without downloading the media

Useful when you are assessing a target rather than building from it — this is the reconnaissance half of the exercise.

```bash
yt-dlp --write-info-json --write-subs --skip-download "URL"
```

Writes a JSON file of title, description, uploader, dates and more, plus subtitles where they exist.

---

## 9. When it does not work

| What you see | What to do |
|---|---|
| `command not found` / not recognised | The binary is not on your PATH. On macOS or Linux, run it as `~/.local/bin/yt-dlp`, or add that folder to your PATH. On Windows, re-check the PATH entry and open a new terminal. |
| Extraction fails, or HTTP 403 | Update first — this fixes the large majority of failures. Run `yt-dlp -U` and try again. |
| `ffmpeg not found` | ffmpeg is not installed or not on the PATH. Install it, or pass `--ffmpeg-location` with the full path. |
| `Requested format is not available` | Run `yt-dlp -F` on the URL and pick a format ID that exists, then use `-f` with that ID. |
| Audio has music or a second speaker | Not a tool problem. Use `--download-sections` to pull a cleaner span of the same source. |
| Output file has no sound | You used `-f "bv*"`, which is video only. Use `"bv*+ba/b"` if you want the audio with it. |
| Very slow download | Add `--concurrent-fragments 4`. If a site is throttling, there is not much else to do. |

---

## Command cheat sheet

| Goal | Command |
|---|---|
| List available formats | `yt-dlp -F "URL"` |
| Video + audio, merged | `yt-dlp -f "bv*+ba/b" -o "target_full.%(ext)s" "URL"` |
| Video only | `yt-dlp -f "bv*" -o "target_video.%(ext)s" "URL"` |
| Audio only, as WAV | `yt-dlp -x --audio-format wav --audio-quality 0 -o "target_audio.%(ext)s" "URL"` |
| Just one span | `--download-sections "*00:01:12-00:01:52" --force-keyframes-at-cuts` |
| Metadata only | `yt-dlp --write-info-json --write-subs --skip-download "URL"` |
| Update yt-dlp | `yt-dlp -U` |

---

Full documentation: <https://github.com/yt-dlp/yt-dlp> · Installation wiki: <https://github.com/yt-dlp/yt-dlp/wiki/Installation>

*Don't Believe Your Eyes · James R. McQuiggan, CISSP · Apparent Security*
