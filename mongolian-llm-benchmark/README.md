# Mongolian LLM Benchmark — which open model understands Mongolian call transcripts?

> Before building a local-only voice QA system I needed to know: can an open-weight LLM read a Mongolian customer-service transcript and score it like a QA analyst? This benchmark answers that with a fixed rubric and real-style transcripts.

**Period:** Sep 2026 · **Status:** first results published; extending to more models and transcripts

---

## 1. Method
- **Task** — given a Mongolian contact-centre transcript, return structured JSON: intent, topic, sentiment, emotion, frustration, empathy, professionalism, resolution quality, root cause, summary, overall score
- **Prompt** — one fixed system prompt (the same rubric used by the Voice QA project)
- **Models** — Qwen 3 family (8B, 14B, 32B) via OpenRouter; local Qwen and GLM runs via Ollama / NVIDIA NIM in a companion script
- **Metrics** — success (valid JSON), latency, and score consistency across runs

## 2. First results (Sep 2026)

| Model | Valid output | Latency (s) | Overall score given | Notes |
|---|---|---|---|---|
| Qwen3-8B | ✅ | 14.5 | 75 | Correct intent and topic in Mongolian; concise summary |
| Qwen3-14B | ✅ | 15.2 | 80 | Best reasoning on resolution quality |
| Qwen3-32B | ❌ | 0.4 | — | Provider error — rerun pending |

**Takeaway:** mid-size Qwen3 models already produce usable Mongolian QA scorecards with valid structure. 14B is the practical sweet spot for on-prem GPUs; larger models need a stable provider before judging.

## 3. Why it matters
Banking data in Mongolia cannot leave the country, so cloud LLMs are off the table. Knowing that a 14B open model is "good enough" makes a fully local QA pipeline realistic on a single-GPU server.

## 4. Stack
Python · pandas · OpenAI-compatible client · OpenRouter · Ollama · Qwen 3 · GLM

---
*Benchmark script and full results available on request.* · Licence: CC BY-NC-ND 4.0
