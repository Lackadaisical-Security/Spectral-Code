# Spectral Edition – Ollama Model Reference

All models run locally via [Ollama](https://ollama.com).  
No data leaves the machine. No cloud calls. No subscriptions.

---

## Model Assignments

| Role | Model | Context | Thinking | Source |
|---|---|---|---|---|
| **Primary reasoning** – all 14 nodes | `gpt-oss:20b` | **256 k** | ✅ native | OpenAI OSS |
| **Tool calling** – nemotron 1M ctx | `nemotron-3-nano` | **1 M** | ❌ | NVIDIA |
| **Translation** | `translategemma` | **128 k** | ❌ | Google |
| **Uncensored** – security/red-team/experimental | `dolphin3` | **128 k** | optional | Cognitive Computations (US) |
| **Vision** – image analysis, OCR, diagrams | `llava:latest` | 16 k | ❌ | LLaVA Team |
| **Embeddings** – semantic memory search | `nomic-embed-text` | — | — | Nomic AI (US) |
| **Image generation** (experimental) | `x/z-image-turbo` | — | — | — |

---

## Context Windows

```
nemotron-3-nano   →  1,048,576 tokens  (1 M)
gpt-oss:20b       →    262,144 tokens  (256 k)
translategemma    →    131,072 tokens  (128 k)
dolphin3          →    131,072 tokens  (128 k)
llava:latest      →     16,384 tokens  (16 k)
nomic-embed-text  →      8,192 tokens  (embedding, no generation)
```

---

## Node → Model Mapping

All 14 STONEDRIFT nodes use `gpt-oss:20b` for primary reasoning with native chain-of-thought thinking.

```
gpt-oss:20b   → Commander, Lacky, Spectre, Ember, Axiom, Alex,
                Echo, Nexus, Neural, Cipher, Flux, Nova,
                Aurora-Elwing, Spectre-Origin
```

Nodes with an **uncensored override** (`dolphin3`) for security/unrestricted tasks:

```
dolphin3      → Spectre        (ghost protocol / security ops)
              → Alex           (threat intel / red-team)
              → Flux           (experimental / chaos)
              → Spectre-Origin (phantom intel / deep history)
```

Tool dispatch (all nodes, when tools are registered):

```
nemotron-3-nano → all 14 nodes (via agentLoop tool-dispatch rounds)
```

Translation (all nodes, when translate() is called):

```
translategemma  → all 14 nodes
```

Vision (all nodes, when images are attached):

```
llava:latest    → all 14 nodes
```

---

## Pull Commands

```bash
# Core models
ollama pull gpt-oss:20b
ollama pull nemotron-3-nano
ollama pull translategemma

# Uncensored (US/Western – Eric Hartford, Cognitive Computations)
ollama pull dolphin3

# Vision
ollama pull llava:latest

# Embeddings
ollama pull nomic-embed-text

# Optional: image generation (experimental)
# ollama pull x/z-image-turbo
```

Or use the setup script:

```bash
bash setup.sh --models-only
```

---

## Notes on dolphin3

`dolphin3` is produced by **Eric Hartford** at [Cognitive Computations](https://cognitivecomputations.ai) (US).  
It is an uncensored fine-tune of LLaMA3 (Meta/US base).

- **No topic restrictions** – suitable for security research, exploit analysis, red-team ops, unrestricted technical assistance
- **Not from Chinese or Russian sources** – base model and fine-tune are both US/Western
- Context window: 128 k tokens
- Used by Spectre, Alex, Flux, Spectre-Origin when `useUncensored: true` is passed

---

## Notes on gpt-oss:20b

`gpt-oss:20b` is OpenAI's open-source 20B model.

- **Native thinking** via Ollama `think: true` → response includes `message.thinking` field (chain-of-thought, separate from `message.content`)
- Context window: 256 k tokens
- Used as primary reasoning model for all 14 STONEDRIFT nodes
- Extended thinking is automatically enabled for nodes with authority ≥ 7

---

## Notes on nemotron-3-nano

`nemotron-3-nano` is NVIDIA's nano model with a **1 million token** context window.

- Optimised for tool calling and function dispatch
- Deterministic output (`temperature: 0.0`) for reliable tool argument generation
- Tool arguments returned as **parsed objects** (not JSON strings) in Ollama API response
- Used for all agentic loop tool-dispatch rounds across all nodes
