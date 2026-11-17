# Synthetic Media & Deepfake Detection Workshop — Local ComfyUI Setup Guide

This guide provides all the commands and configuration steps needed to set up a local **ComfyUI** environment on your Windows or macOS laptop for our three workshop labs:
1. **Lab 1:** Media Ingestion (via `yt-dlp` CLI) & Audio/Video Splitting
2. **Lab 2:** Multilingual Voice Cloning (ChatterBox TTS)
3. **Lab 3:** Neural Lip-Sync (LatentSync)

> **Hardware Note:** Labs 1 and 2 run on almost any modern Windows or macOS laptop. **Lab 3 (LatentSync)** requires an **NVIDIA GPU with at least 12GB–16GB of VRAM (CUDA)**. If you are using a Mac (Apple Silicon) or a laptop with less than 12GB VRAM, complete Labs 1 and 2 locally and use our cloud environment for Lab 3.

---

## Step 1: Install System Prerequisites (`Git`, `FFmpeg`, and `yt-dlp`)

Before installing ComfyUI custom nodes, install **Git**, **FFmpeg** (required for video/audio splitting and muxing), and **yt-dlp** (for downloading source videos outside of ComfyUI).

### Windows (PowerShell as Administrator)
Open **PowerShell** as Administrator and run:
```powershell
winget install Git.Git
winget install Gyan.FFmpeg
winget install yt-dlp.yt-dlp
```
*Restart your terminal (and ComfyUI if it is already open) after installation so Windows updates your system `PATH`.*

### macOS (Terminal)
Using [Homebrew](https://brew.sh/), run:
```bash
brew install git ffmpeg yt-dlp
```

### Verify Your Installations
Run the following commands in your terminal to confirm all three tools are accessible:
```bash
git --version
ffmpeg -version
yt-dlp --version
```

---

## Step 2: Install ComfyUI & Configure Manager

1. Download and install **ComfyUI Desktop** from [comfy.org](https://www.comfy.org/) (or clone the portable/manual release from GitHub).
2. Launch ComfyUI and verify **ComfyUI Manager** is accessible:
   * Click the top-left **ComfyUI Menu** dropdown and look for **Manager** / **Install Custom Nodes**.
   * *(Optional)* To restore the classic **Manager** button on the top toolbar, open **Settings > Manager** (or Startup Args), add `--enable-manager --enable-manager-legacy-ui`, and restart ComfyUI.
3. **CRITICAL — Disable Nodes 2.0 (Beta):** Open **Settings** (gear icon) and ensure **Nodes 2.0 (Beta)** is turned **OFF**. Legacy custom nodes will not render or drag-and-drop onto the grid properly when Nodes 2.0 is enabled.

---

## Step 3: Install Required Custom Node Packs

Because we download source media via the `yt-dlp` command line before loading it into ComfyUI, you only need **three custom node packs**.

### Option A: Install via ComfyUI Manager (Recommended)
1. Open **Manager > Custom Nodes Manager** (or **Install via Git URL**).
2. Install each of the following three repositories:

| Lab | Custom Node Pack | GitHub Repository URL | Key Nodes Provided |
| :--- | :--- | :--- | :--- |
| **Lab 1 & 3** | **ComfyUI-VideoHelperSuite** | `https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git` | `VHS_LoadVideo`, `VHS_LoadAudioUpload`, `VHS_VideoCombine` |
| **Lab 2** | **ComfyUI_Fill-ChatterBox** | `https://github.com/filliptm/ComfyUI_Fill-ChatterBox.git` | `FL_ChatterboxMultilingualTTS` |
| **Lab 3** | **ComfyUI-LatentSyncWrapper** | `https://github.com/ShmuelRonen/ComfyUI-LatentSyncWrapper.git` | `LatentSyncNode` (or `LatentSync1.6`) |

3. **Full Restart:** After installing all three packs, completely close and relaunch ComfyUI so Python initializes the new dependencies.

### Option B: Install via Terminal (`git clone`)
If ComfyUI Manager blocks Git URLs, navigate to your `ComfyUI/custom_nodes` directory in your terminal and run:
```bash
cd /path/to/your/ComfyUI/custom_nodes

git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git
git clone https://github.com/filliptm/ComfyUI_Fill-ChatterBox.git
git clone https://github.com/ShmuelRonen/ComfyUI-LatentSyncWrapper.git
```
*(After cloning, restart ComfyUI and run **Try Fix** in ComfyUI Manager if any dependencies need to be installed into your Python environment).*

---

## Step 4: Model Checkpoints

* **Lab 2 (ChatterBox Multilingual TTS):** No manual download needed. The model weights download automatically from Hugging Face into `ComfyUI/models/chatterbox` (or `models/TTS`) the first time you run the workflow.
* **Lab 3 (LatentSync Lip-Sync):** Checkpoints usually auto-download on first run. If your network blocks auto-downloads, place the required files manually into `ComfyUI/custom_nodes/ComfyUI-LatentSyncWrapper/checkpoints/`:
  * `latentsync_unet.pt` and `stable_syncnet.pt` (from `ByteDance/LatentSync-1.6` on Hugging Face)
  * `whisper/tiny.pt` (OpenAI Whisper `tiny` checkpoint)
  * `vae/diffusion_pytorch_model.safetensors` and `vae/config.json` (from `stabilityai/sd-vae-ft-mse` on Hugging Face)

---

## Step 5: Download Source Media via `yt-dlp` (CLI Commands)

Instead of downloading inside ComfyUI, use `yt-dlp` in your terminal to save clean `.mp4` and `.mp3` files directly into your **`ComfyUI/input/`** folder.

Open your terminal, navigate to your `ComfyUI/input` directory (`cd /path/to/ComfyUI/input`), and use the commands below:

### 1. Download Full Video (MP4 — H.264 + AAC for maximum compatibility)
```bash
yt-dlp -f "bv*[ext=mp4]+ba[ext=m4a]/b[ext=mp4]" --merge-output-format mp4 -o "target_video.%(ext)s" "YOUR_VIDEO_URL"
```

### 2. Download Audio Only (MP3 — for Voice Cloning Reference)
```bash
yt-dlp -x --audio-format mp3 --audio-quality 0 -o "target_voice.%(ext)s" "YOUR_VIDEO_URL"
```

### 3. Download a Specific Time Range (Start & Stop Timestamps)
To download only a specific section (e.g., from `00:00:15` to `00:00:35`) without downloading the entire video:
```bash
yt-dlp --download-sections "*00:00:15-00:00:35" -f "bv*[ext=mp4]+ba[ext=m4a]/b[ext=mp4]" --merge-output-format mp4 -o "trimmed_clip.%(ext)s" "YOUR_VIDEO_URL"
```

### 4. Bypass Age/Bot Restrictions Using Browser Cookies
If YouTube or TikTok prompts for sign-in verification, pass your browser's active session cookies directly (`chrome`, `firefox`, `edge`, or `brave`):
```bash
yt-dlp --cookies-from-browser chrome -f "bv*[ext=mp4]+ba[ext=m4a]/b[ext=mp4]" --merge-output-format mp4 -o "target_video.%(ext)s" "YOUR_VIDEO_URL"
```

*(Once downloaded into `ComfyUI/input/`, click the refresh button on your `Load Video` or `Load Audio` node in ComfyUI to select the file).*

---

## Troubleshooting Quick Checks

* **Nodes appear red (`IMPORT FAILED`) after importing a lab JSON:**
  Open **Manager > Custom Nodes Manager**, change the filter dropdown from *All* to **Import Failed**, click **Try Fix** next to the broken pack, and restart ComfyUI.
* **Nodes appear in the left sidebar but won't drag onto the grid:**
  Open **Settings** and disable **Nodes 2.0 (Beta)**, then hard-reload the interface (`Ctrl + Shift + R` on Windows / `Cmd + Shift + R` on macOS).
* **`yt-dlp` fails with a 403 Forbidden error:**
  Update `yt-dlp` to the latest nightly build (`yt-dlp -U` or `pip install -U yt-dlp`) and append `--cookies-from-browser chrome` (or `firefox`/`edge`) to your command.
* **Video or audio export fails with an FFmpeg error:**
  Verify FFmpeg is accessible by typing `ffmpeg -version` in your terminal. If it returns "command not found," re-run Step 1 and restart your computer.