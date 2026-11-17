# Build a Deepfake, Learn the Defense

**Student handout**
*Building video deepfakes to understand the threat.*

**Contents**

1. [Start your setup now](#1-start-your-setup-now)
2. [What you need with you](#2-what-you-need-with-you)
3. [The two tools, and the difference between them](#3-the-two-tools-and-the-difference-between-them)
4. [ComfyUI in sixty seconds](#4-comfyui-in-sixty-seconds)
5. [The three labs](#5-the-three-labs)
6. [Save points](#6-save-points)
7. [If you get stuck](#7-if-you-get-stuck)
8. [Resources](#8-resources)

---

## 1. Start your setup now

1. **Create a MimicPC account and add credit.** Budget about $10. The free trial allowance is not enough to finish the labs, and purchases are non-refundable.
2. **ComfyUI.** For the labs we will use this shared ComfyUI environment — <https://home.mimicpc.com/app-image-share?key=e9ab37a5b7f54453a66e157d3a626962>
3. **Launch a Large-tier instance** (L4 / A10 class, 24 GB graphics memory). A smaller tier will run the voice lab and then fail at lip-sync.
4. **Set up yt-dlp** for downloading videos to clone from YouTube, TikTok, Vimeo and similar. See [the yt-dlp guide](yt-dlp-guide.md).

---

## 2. What you need with you

| Requirement | Detail |
|---|---|
| Laptop | Any modern machine, 8 GB RAM or more. Nothing is installed locally. |
| Browser | Current Chrome, Edge, or Firefox. |
| Network | Stable connection. You will move video and audio files to and from the cloud machine. |
| Headphones | Easier to listen with these than with laptop speakers in a full room. |
| MimicPC account | Created in advance, with roughly $10 of credit on it. |
| *Optional:* your own likeness | A 60-second front-facing video of yourself, clean audio, good light, plus one clear photo. |
| *Optional:* local NVIDIA GPU | 24 GB VRAM or more, Windows or Linux. Apple Silicon is not supported for these labs — use the cloud path. |

---

## 3. The two tools, and the difference between them

| Tool | What it is |
|---|---|
| **MimicPC** | The computer. A machine with a powerful graphics card that you rent by the hour and reach through your browser. It is somewhere else; you are renting it, not installing it. |
| **ComfyUI** | The application running on that computer. Where you load a workflow, set inputs, and press Queue Prompt. This is where you spend the day. |

> **Note about MimicPC**
>
> Closing the browser tab or shutting your laptop does not stop the machine — it keeps running and keeps billing. Stop the instance at both breaks, and again before you leave. Download anything you want to keep first.

---

## 4. ComfyUI in sixty seconds

A workflow is a flowchart that runs. Instead of writing code, you connect boxes; each box does one job and hands its result to the next.

- **Node** — a box that does one job: load a file, run a model, save a result.
- **Link** — a wire carrying data from one box's output into the next box's input.
- **Widget** — a setting inside a node: a number, a filename, a slider.
- **Workflow** — the whole connected graph, saved as a file. Yours are pre-built.
- **Queue Prompt** — the button that runs it. Press it once. Generation takes minutes, not seconds; the silence is the tool working.
- **Output** — your generated file, written to a folder on the rented machine. Not to your laptop until you download it.

**Navigation:** drag an empty part of the canvas to move around, scroll to zoom. If the view ends up somewhere strange, reload the workflow file — it costs nothing and always works.

**One rule for today:** change the widgets the instructor points at, and leave the wires alone.

---

## 5. The three labs

Each lab consumes the previous one's output. Keep track of your file paths — this chain is the whole workshop.

| Lab | You start with | What you do | You end with |
|---|---|---|---|
| **1** | Your own recording | Retrieve source video and a clean single-speaker audio file. Clone the voice from a short reference sample and write a line for it to say. | Cloned-voice audio file |
| **2** | Lab 1's audio + your source video | Lip-sync the cloned voice onto the real face. Expect imperfect output — the flaws are the lesson. | Lip-synced video |
| **3** | Lab 2's output, or a still image | Generate video from a single still image, then re-sync it to your cloned voice. Nothing in the final frame is real. | Fully synthetic clip |

**Live avatar demo:** instructor-led. You are watching, not building. It is a separate application and is not part of today's toolchain.

---

## 6. Save points

At each save point, save the file **and** download it to your own machine. Anything left only on the rented instance is gone when you stop it.

| File | Where I saved it |
|---|---|
| Source video | |
| Clean reference audio | |
| Cloned-voice audio (Lab 1) | |
| Lip-synced video (Lab 2) | |
| Generated and re-synced clip (Lab 3) | |

---

## 7. If you get stuck

**Report it like this:** the node name, and the message. Say it early, not at the break.

| What you see | Most likely fix |
|---|---|
| Instance will not launch | No credit on the account. Add credit — the GPU tiers will not start without it. |
| Models downloading during a lab | You launched a fresh instance. Go back to the one you set up this morning. |
| Red node: missing model | Same cause as above — the pre-staged models are not on this instance. |
| Red node: out of memory | The tier is too small. You need the 24 GB class, not the 16 GB one. |
| Nothing happens on Queue Prompt | An input was left empty. Check the load nodes first. |
| Voice clone sounds robotic | The reference sample is too short, noisy, or has music under it. Re-cut a clean 30–60 second single-speaker clip. |
| Lip-sync output is poor | Expected. Pick a more front-facing source frame and move on — do not burn ten minutes chasing perfection. |

---

## 8. Resources

### Tools and resources used today

- **Workshop repository** — <https://github.com/jmcquiggan/deepfake-workshop>
- **MimicPC** — browser-based cloud GPU platform. Large tier, 24 GB class. <https://www.mimicpc.com>
- **ComfyUI** — the node-graph application the workflows run in.
- **yt-dlp** — command-line retrieval of source media. <https://github.com/yt-dlp/yt-dlp>
- **Sync.so** — web tool for creating lip-sync videos. Free tier available; more resources on a paid subscription.
- **ElevenLabs.io** — web tool to generate a voice clone and output an audio file.
- **ComfyUI Deepfake Labs** — <https://home.mimicpc.com/app-image-share?key=7ae0a3891fee4f8994c76ce8c3cc5eb4>

### Cases referenced in the session

- **Arup, Hong Kong office** — ~US$25.6M across 15 transfers, January 2024. CNN, 16 May 2024.
- **DPRK IT-worker schemes** — U.S. Treasury SB0416 (Mar 2026); FBI/State alert (Jul 2026); multinational advisory (Sep 2026).
- **The Liar's Dividend** — Chesney & Citron, *California Law Review*, 2019.

### Take these back to work

- Out-of-band callback to a number from your own directory — never the one in the request.
- Two approvers plus a cooling-off period. Secrecy plus urgency means stop.
- Live identity in hiring: camera on, ID match plus liveness. Ship equipment only to a verified address.
- Payee name must match the employee. Flag repeated bank changes and crypto pay requests.

---

*James R. McQuiggan, CISSP · Apparent Security*
[jamesmcquiggan.com](https://jamesmcquiggan.com) · [apparentsecurity.com](https://apparentsecurity.com) · [youtube.com/@JamesRMcQuiggan](https://youtube.com/@JamesRMcQuiggan)
