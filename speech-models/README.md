# Mongolian Speech Models — fine-tuning ASR and TTS for a language the cloud ignores

> Mongolian has about 3.5 million speakers and almost no open speech-AI support. No major cloud provider offers a Mongolian voice, and banking regulation does not allow sending customer audio abroad anyway. So the voice platform runs on models I fine-tuned and benchmarked myself.

**Role:** solo — data pipeline, training, evaluation, CPU deployment · **Period:** Jun – Nov 2026 · **Status:** in production, still improving

---

## 1. Speech to text

### Results — the fair comparison

Two public test sets, reported as **word error rate / character error rate**. Lower is better.

| System | FLEURS mn | Common Voice mn | Notes |
|---|---|---|---|
| Whisper large-v3, no fine-tuning | — | 94.2 / 48.0 | Effectively unusable for Mongolian |
| **My Whisper large-v3 + LoRA (v3)** | 33.4 | **14.7** / — | Also 18.7 on 8 kHz telephony, 18.9 on Kazakh |
| **My OmniASR CTC 1B fine-tune** | 36.6 / **10.7** | 35.7 / 9.4 | Better characters, worse words — no language model |
| **OmniASR + KenLM decoding** | **18.2** / **5.4** | — | Language model closes most of the gap |
| **My Nemotron streaming ASR, pilot** | 37.8 / 13.7 | — | Streaming, built for live calls; no hallucinated text on the test set |
| Commercial Mongolian ASR API | 12.7 / 4.5 | — | The bar to beat |

Fine-tuning takes Whisper from unusable to production-ready. Adding a KenLM language model to the CTC model halves its word error rate without any further training — the model knew the sounds, it just needed to know the words.

### The measurement mistake worth writing down

My first comparison said the fine-tuned CTC model was *worse* than the baseline, which made no sense. It turned out that **297 of the 300 Common Voice test sentences also appeared in the training text**. The baseline had effectively seen the answers, so every Common Voice number flattered it.

Once the test set was separated by text, not just by file, FLEURS became the honest benchmark and the real picture appeared. Since then every comparison runs against the same held-out set, measured against the base model from the start.

This is the kind of error that quietly invalidates a whole evaluation. It is also exactly the kind of thing fifteen years of reporting on contact-centre numbers teaches you to look for.

### How it was built

| | |
|---|---|
| Base models | `openai/whisper-large-v3`, OmniASR CTC 1B |
| Method | LoRA adapters (rank 64, alpha 128) and full CTC fine-tuning |
| Training mix | Mongolian, English and code-switched speech; **half degraded to 8 kHz** so the model holds up on real phone lines |
| Languages | Mongolian plus Kazakh, for the Bayan-Ölgii region |
| Decoding | KenLM + pyctcdecode, with hotword biasing for product and place names |
| Hardware | Single rented GPU per run (A40, L40S, RTX 4090) |

A divergence guard and checkpoint retention were added after one run at too high a learning rate diverged and lost the work.

### Streaming, several languages, and a bank with no GPU

Whisper is accurate but not streaming, and a live phone call needs words as they are spoken. So the live-call recogniser is NVIDIA's **Nemotron streaming ASR (0.6B)**, fine-tuned on Mongolian with Kazakh, English and Russian. Every fine-tune replays data in the other three languages and is evaluated on them, so gaining Mongolian never costs the others.

A second fine-tune targets the bank itself: trained on the bank's own GPU during a one-month training window, on audio passed through a simulation of the bank's telephone channel. In production at the bank it has to run on **CPU-only servers**, so it is served through the open-source NeMo-Speech.cpp runtime, built for RHEL 8. The 8-bit quantised version started repeating words; the 16-bit version did not, and that is what the bank runs.

A language-ID model labels each caller's language and keeps it for the call, so the right recogniser and the right human queue are chosen.

## 2. Text to speech

Three generations of Mongolian voice, each chosen for where it has to run.

| Model | Where it runs | What it brought |
|---|---|---|
| CosyVoice 3, four fine-tunes | GPU | First production voice; the lessons below |
| **VoxCPM2 + Mongolian LoRA (v1–v3)** | GPU | Current main voice of the agent. Served with Nano-vLLM, it cut real response time from 7.4 to 5.5 s |
| **Magpie TTS 357M, fine-tuned** | **CPU** | For bank servers with no GPU. **322 ms** to first audio on a laptop CPU (8-bit) |

The newest voices are trained on studio-reviewed recordings of one professional contact-centre voice.

**More training made Magpie worse.** At 6,000 steps the character error rate on the target voice was 11.7; at 15,000 steps it was 16.4. Both checkpoints are kept and the final choice is made by listening on the target hardware, not by the step count.

### Lessons from CosyVoice 3

- **Language model fine-tuned, decoder frozen.** That single decision keeps the base model's emotion and instruction control working in a language it was never trained on. Fine-tuning the whole stack on a few hours of a low-resource language degrades the voice.
- **Replay data prevents collapse.** Training on one target speaker for a few epochs pulls the model towards that speaker and general pronunciation degrades. Mixing in a few hours of general Mongolian during fine-tuning stops it.
- **The reference prompt sets the style, not just the voice.** One voice sounded artificial until I measured it: the reference clip was a 4.5-second slogan read at 11.7 characters per second with a 14-semitone pitch range and no falling ending. A conversational reference at 13.9 characters per second, 10 semitones and a falling ending fixed it. CosyVoice copies the *delivery* of the reference, not only its timbre.

### Dataset pipeline

I also built an automated pipeline that turns long broadcast audio into a clean single-speaker training set: voice activity detection, energy-based splitting, speaker verification clustering, ASR transcription, then human review. It produced **11,293 clips / 29.4 hours** of one speaker. The dataset is private and used for research only.

Two lessons from that pipeline are worth keeping: continuous speech needs energy splitting after voice-activity detection or most of the audio is discarded, and uploading eleven thousand small files to a model hub hits rate limits — archive them first.

## 3. Why this matters commercially

Owning the models rather than renting them is the moat for a local-market product:

- **Compliance.** Customer audio never leaves the country, which is a hard requirement for Mongolian banks.
- **Cost.** No per-minute API fee on a product whose unit economics depend on minutes.
- **Control.** Vocabulary, telephony bandwidth and dialect can all be tuned; a cloud API offers none of that for Mongolian.

## 4. Models

All models and datasets are private. Results on this page come from my own evaluation runs on held-out data; I'm happy to demo any of them live.

## 5. Stack

PyTorch · Hugging Face Transformers · PEFT / LoRA · NVIDIA NeMo (Nemotron ASR, Magpie TTS) · NeMo-Speech.cpp (GGUF, CPU) · VoxCPM2 · Nano-vLLM · fairseq2 · KenLM · pyctcdecode · pyannote · ECAPA language ID · CosyVoice 3 · Whisper · RunPod and on-prem GPUs

---
*Training code and weights are private. Live walkthrough available on request.* · Licence: CC BY-NC-ND 4.0
