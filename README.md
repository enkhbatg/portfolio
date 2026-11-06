# Enkhbat Gansukh — Project Portfolio

**AI Product Owner & Business Analyst · 15 years in contact centres, workforce management and CX · Auckland, New Zealand**

I spent 15 years running and improving contact-centre operations at Mongolia's largest bank and largest telco. In 2025–2026 I started building the tools I always wished I had — end to end, from telephony to UI — using Claude Code as my engineering partner.

Every project below is described with architecture, screenshots and results. **Source code is private**; I'm happy to walk through any of it live in an interview.

| # | Project | One line | Stack | Status |
|---|---|---|---|---|
| 1 | [VoiceOS](./voiceos/) | Mongolian AI voice agent platform: a phone number answered by an AI operator in 2–3 seconds, with a 16-screen admin console | Asterisk · LiveKit · Python · FastAPI · Postgres/pgvector | Live in production; bank on-prem edition in testing |
| 2 | [Delegate](./delegate/) | AI co-worker platform: staff ask in chat, agents act on internal systems, databases and MCP tools within risk tiers and approvals | Python · FastAPI · Postgres RLS · MCP · Claude / GPT / local LLMs | Core in production, Oct–Nov 2026 |
| 3 | [Mongolian Speech Models](./speech-models/) | Fine-tuned speech recognition and synthesis for a language no cloud provider supports, down to CPU-only bank servers | Whisper · NVIDIA NeMo · VoxCPM2 · Magpie TTS · KenLM | In production |
| 4 | [Meeting Minutes Agent](./meeting-minutes/) | Recording to signed-off minutes in the official committee format, with per-person task emails | Diarization · FastAPI · Postgres · Microsoft Graph | Shipped Oct 2026 |
| 5 | [Contact Centre WFM](./wfm-scheduler/) | Forecasting and shift scheduling with constraint optimisation | React · Node/Hono · FastAPI · OR-Tools CP-SAT · Postgres | v9.7, real contact-centre data |
| 6 | [Voice QA Pipeline](./voice-qa/) | Score 100% of calls instead of 1–3%: audio to transcript to PII masking to an LLM scorecard | ffmpeg · Whisper · diarization · Presidio · local LLM | POC, on-prem ready |
| 7 | [Agentic Commerce Gateway](./agentic-commerce/) | Multi-tenant MCP server that lets AI assistants search, check stock and order from local merchants | Node/TS · Postgres RLS · MCP · OAuth | 17 test suites green |
| 8 | [IELTS AI Tutor](./ielts-ai-tutor/) | Writing and speaking practice with instant feedback on 6 official criteria and 19 error types | React · Vite · OpenAI · Azure Speech | UI complete |
| 9 | [Mongolian LLM Benchmark](./mongolian-llm-benchmark/) | Which open model can score a Mongolian call transcript like a quality analyst? | Python · Qwen · OpenRouter | Results published |
| 10 | [Life OS](./life-os/) | Mobile second brain: capture, organise, plan, execute and track, offline-first | Expo/React Native · Supabase · pgvector | Paused |

## Mongolian speech models

Mongolian has almost no open speech-AI support, so the voice platform runs on models I fine-tuned myself. The models are private; method and results are in the [case study](./speech-models/).

| Model | What it does | Headline result |
|---|---|---|
| Whisper large-v3 — Mongolian LoRA | Mongolian speech to text, including 8 kHz telephone audio and Kazakh | Word error rate 94.2 → **14.7**; telephony 18.7; Kazakh 18.9 |
| OmniASR CTC 1B — Mongolian | Alternative recogniser with language-model decoding | FLEURS 36.6 → **18.2** once a KenLM language model is added |
| Nemotron streaming ASR — Mongolian | Streaming recogniser for live calls in Mongolian, Kazakh, English and Russian | Bank-domain fine-tune running on CPU-only bank servers |
| VoxCPM2 — Mongolian LoRA | Main voice of the voice agent, on GPU | Real response time 7.4 → 5.5 s when it replaced the previous engine |
| Magpie TTS — Mongolian | Speech synthesis for bank servers with no GPU | **322 ms** to first audio on a laptop CPU |

## What ties them together
- **Domain first.** Each tool solves a problem I ran into as an operator, WFM analyst or product owner — not a tutorial project.
- **Local-first by design.** Banking data cannot leave the country, so the bank edition runs speech recognition and synthesis on the bank's own servers, CPU-only if needed, and every model can be swapped for a local one.
- **Ship, then measure.** Real phone numbers, real schedules, real call audio.

📫 enkhbat.ga@gmail.com · [LinkedIn](https://linkedin.com/in/enkhbat-gansukh)

*Licence: CC BY-NC-ND 4.0 — you may share this material with attribution; no commercial use, no derivatives.*
