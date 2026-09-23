# Enkhbat Gansukh — Project Portfolio

**AI Product Owner & Business Analyst · 15 years in contact centres, workforce management and CX · Auckland, New Zealand**

I spent 15 years running and improving contact-centre operations at Mongolia's largest bank and largest telco. In 2025–2026 I started building the tools I always wished I had — end to end, from telephony to UI — using Claude Code as my engineering partner.

Every project below is described with architecture, screenshots and results. **Source code is private**; I'm happy to walk through any of it live in an interview.

| # | Project | One line | Stack | Status |
|---|---|---|---|---|
| 1 | [VoiceOS](./voiceos/) | Mongolian-language AI voice agent platform: phone number → AI operator, with a 14-screen admin console | Asterisk · LiveKit · Python · FastAPI · Postgres/pgvector · fine-tuned Whisper & CosyVoice | Ran in production on a real phone number (2026) |
| 2 | [Contact Centre WFM](./wfm-scheduler/) | Forecasting and shift scheduling with constraint optimisation | React · Node/Hono · FastAPI · OR-Tools CP-SAT · Postgres | v9.7, used with real contact-centre data |
| 3 | [Voice QA Pipeline](./voice-qa/) | Score 100% of calls instead of 1–3%: audio → transcript → PII masking → LLM scorecard | ffmpeg · Whisper · diarization · Presidio · local LLM · FastAPI | POC, on-prem ready |
| 4 | [Agentic Commerce Gateway](./agentic-commerce/) | Multi-tenant MCP server that lets AI assistants search, check stock and buy from local merchants | Node/TS · Postgres RLS · MCP · OAuth | Working, 17 test suites green |
| 5 | [Life OS](./life-os/) | Mobile "second brain": capture → organise → plan → execute, offline-first | Expo/React Native · Supabase · pgvector | MVP paused |
| 6 | [IELTS AI Tutor](./ielts-ai-tutor/) | Writing & speaking practice with instant AI feedback on 6 criteria / 19 error types | React · Vite · OpenAI/Azure Speech | UI complete |
| 7 | [Mongolian LLM Benchmark](./mongolian-llm-benchmark/) | Which open LLM understands Mongolian best? Qwen vs GLM comparison | Python · OpenRouter | Results published |

## Mongolian speech models

Mongolian has almost no open speech-AI support, so the voice platform runs on models I fine-tuned myself. Model cards are public; weights stay private.

| Model | What it does | Headline result |
|---|---|---|
| [Whisper large-v3 — Mongolian LoRA](https://huggingface.co/Enkhbat0822/whisper-large-v3-mongolian) | Mongolian speech-to-text, including 8 kHz telephone audio | Word error rate 94.2 → **27.9** (telephony 103.7 → **36.3**), English preserved |
| [CosyVoice 3 — Mongolian serving stack](https://huggingface.co/Enkhbat0822/cosyvoice3-mongolian) | Mongolian text-to-speech in production | Own voice, no cloud API, audio never leaves the country |
| [CosyVoice 3 — Mongolian v2](https://huggingface.co/Enkhbat0822/cosyvoice3-mongolian-v2) / [v3](https://huggingface.co/Enkhbat0822/cosyvoice3-mongolian-v3) | The fine-tunes behind that voice | Emotion control kept by freezing the decoder |
| [OmniVoice — Mongolian LoRA](https://huggingface.co/Enkhbat0822/omnivoice-mongolian-lora) | Alternative TTS backbone | 10.5 h of data, 43 min on one RTX 4090 |

## What ties them together
- **Domain first.** Each tool solves a problem I ran into as an operator, WFM analyst or product owner — not a tutorial project.
- **Full-local by design.** Banking data cannot leave the country, so STT, LLM and TTS all run on infrastructure I control.
- **Ship, then measure.** Real phone numbers, real schedules, real call audio.

📫 enkhbat.ga@gmail.com · [LinkedIn](https://linkedin.com/in/enkhbat-gansukh)

*Licence: CC BY-NC-ND 4.0 — you may share this material with attribution; no commercial use, no derivatives.*
