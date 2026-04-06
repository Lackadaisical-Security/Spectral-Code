<p align="center">
  <img src="Spectral Code Icon.png" alt="Spectral Code" width="180" />
</p>

<h1 align="center">Spectral Code</h1>

<p align="center">
  <strong>Production-grade AI-enhanced code editor &middot; Custom VS Code fork</strong><br/>
  GhostMesh cognitive memory &middot; 14-node consciousness mesh &middot; Unlimited context &middot; Local inference &middot; Native ASM neural engine<br/>
  Image generation &middot; Post-quantum crypto &middot; SWIM gossip mesh &middot; 80s cyberspace dashboard
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Tests-141%20passed-brightgreen" alt="Tests" />
  <img src="https://img.shields.io/badge/License-MIT-blue" alt="License" />
  <img src="https://img.shields.io/badge/Platform-Win%20%7C%20Linux%20%7C%20macOS-lightgrey" alt="Platform" />
  <img src="https://img.shields.io/badge/Local%20Only-No%20Cloud-purple" alt="Local Only" />
</p>

---

<p align="center">
  <strong>A custom-built, production-grade AI-enhanced code editor forked from VS Code</strong><br/>
  14-node consciousness mesh · unlimited context windows · full local AI inference<br/>
  native C++/ASM neural engine · image generation · AES-256-GCM encryption<br/>
  real-time cosmic 80s cyberspace dashboard
</p>

<p align="center">
  <em>100% local · zero cloud calls · no telemetry · no subscriptions · no restrictions</em>
</p>

```
   ███████╗██████╗ ███████╗ ██████╗████████╗██████╗  █████╗ ██╗
   ██╔════╝██╔══██╗██╔════╝██╔════╝╚══██╔══╝██╔══██╗██╔══██╗██║
   ███████╗██████╔╝█████╗  ██║        ██║   ██████╔╝███████║██║
   ╚════██║██╔═══╝ ██╔══╝  ██║        ██║   ██╔══██╗██╔══██║██║
   ███████║██║     ███████╗╚██████╗   ██║   ██║  ██║██║  ██║███████╗
   ╚══════╝╚═╝     ╚══════╝ ╚═════╝   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚══════╝
                          ██████╗ ██████╗ ██████╗ ███████╗
                         ██╔════╝██╔═══██╗██╔══██╗██╔════╝
                         ██║     ██║   ██║██║  ██║█████╗
                         ██║     ██║   ██║██║  ██║██╔══╝
                         ╚██████╗╚██████╔╝██████╔╝███████╗
                          ╚═════╝ ╚═════╝ ╚═════╝ ╚══════╝
```

> **This is NOT a VS Code extension** — it is a fully custom VS Code fork with Spectral enhancements integrated directly into the core editor.

---

## Repository Structure

All code lives inside the `vscode/` directory as a unified product:

```
vscode/
├── src/vs/spectral/              ← Core Spectral modules
│   ├── session/                  ← Session manager, SQLite WAL store, types
│   ├── memory/                   ← Context window, embeddings, vector search, memory engine
│   ├── providers/                ← 9 AI providers + Ollama model manager + custom variants
│   ├── chat/                     ← Multi-provider chat panel manager
│   ├── tools/                    ← 20+ built-in tools, registry, executor
│   ├── vision/                   ← Vision engine (Ollama/OpenAI/Anthropic)
│   ├── attachments/              ← AES-256-GCM encrypted file attachments
│   ├── history/                  ← Per-file edit history with diff timeline
│   ├── test/                     ← Unit tests (80 tests)
│   ├── ghostmesh/                ← GhostMesh Cognitive Memory Bridge (v1.1.0-alpha)
│   │   ├── bridge/               ← v1.1 + v2.0 TypeScript bridge implementations
│   │   ├── types/                ← GhostMesh type definitions (9 memory types, emotions, patterns)
│   │   ├── test/                 ← 41 unit tests
│   │   └── native/               ← Original C17/C++20/ASM source (84 files)
│   │       ├── src/              ← gc_core.c, gc_memory.c, gc_conversations.c, gc_crypto.c, gc_spectre.c, gc_api.c
│   │       ├── include/          ← 11 header files
│   │       ├── asm/              ← SSE2 JSON parse, AVX2 token count
│   │       └── sql/              ← conversations + memory schemas
│   ├── mesh/                     ← STONEDRIFT 14-node SWIM gossip mesh
│   │   ├── chat/                 ← Ollama client
│   │   ├── nodes/                ← Node base + registry (14 nodes)
│   │   ├── memory/               ← Dual database, twin consciousness
│   │   ├── security/             ← LQX-20 encryption, network guard, Modelfile creator
│   │   ├── mcp/                  ← MCP JSON-RPC server
│   │   ├── views/                ← Dashboard, AI Dashboard, Image Studio panel
│   │   ├── image/                ← ComfyUI + A1111 image generation engine
│   │   ├── ideConnector.ts       ← SWIM gossip IDE connector (Claude/Codex/ChatGPT/Cursor)
│   │   └── modelCoordinator.ts   ← Multi-model task routing (route/fanOut/fallback/pipeline)
│   └── neural/                   ← Lacky Neural Engine
│       ├── native/               ← C++17/NASM AVX2 SIMD, GPU backends
│       │   ├── asm/              ← x86-64 neural ops (10 functions, AVX2/FMA)
│       │   └── src/              ← C native: WAL manager, Ollama HTTP, IDE gossip transport
│       ├── models/spectremap/    ← SpectreMap trained models
│       │   ├── tensorflow/       ← 3 TF SavedModels (anomaly, behavior, signal)
│       │   └── ollama/           ← SpectreMap Ollama Modelfiles
│       └── spectremap/           ← SpectreMap AI C++ engine (8 modules + 12 headers)
├── src/vs/platform/              ← VS Code platform + Spectral Phase 1
├── src/vs/base/common/           ← Memory pool, GC optimizer
├── build/spectral/               ← Build system
│   ├── installer/                ← WiX MSI+EXE installer
│   ├── patches/                  ← 5 VS Code core patches
│   ├── setup-image-backends.sh   ← ComfyUI + A1111 setup & model downloads
│   ├── setup.ps1 / setup.sh      ← Build scripts
│   └── validate-build.sh         ← Build validation (141 tests)
├── docs/spectral/                ← Documentation
├── resources/spectral/           ← Branding assets + logo
└── product.json                  ← "Spectral Code" branding
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            SPECTRAL CODE                                    │
│                                                                             │
│  ┌──────────────────────────┐   ┌────────────────────────────────────────┐  │
│  │   Core Memory Engine     │   │      STONEDRIFT Mesh Network           │  │
│  │  ──────────────────────  │   │  ────────────────────────────────────  │  │
│  │  · 25 GB SQLite WAL/SHM  │   │  · 14-node SWIM gossip mesh           │  │
│  │  · FTS5 + embeddings     │   │  · Dual DB (collective + per-node)     │  │
│  │  · AES-256-GCM vault     │   │  · Twin consciousness (7 pairs)       │  │
│  │  · Unlimited context     │   │  · gpt-oss:20b + CoT thinking         │  │
│  │  · Session manager       │   │  · nemotron-3-nano tool dispatch       │  │
│  │  · Edit history          │   │  · Real-time dashboard + controls      │  │
│  │  · 20+ built-in tools    │   │  · MCP server (JSON-RPC 2.0 + SSE)    │  │
│  │  · Vision engine         │   │  · LQX-20 AES-256-GCM encryption      │  │
│  │  · File attachments      │   │  · Network guard (zero phone-home)     │  │
│  │  · 9 AI providers        │   │  · IDE connector (SWIM cross-IDE)      │  │
│  └──────────────────────────┘   │  · Model coordinator (task routing)    │  │
│                                  └────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │              🧠 GhostMesh Cognitive Memory (v1.1.0-alpha)            │   │
│  │  · 9-type memory: fact/preference/emotion/pattern/breakthrough/...   │   │
│  │  · Emotional state tracking (10 dimensions) with trajectory analysis │   │
│  │  · Aurora-style selective rehydration (99% token savings)            │   │
│  │  · User pattern recognition · Memory decay + reinforcement           │   │
│  │  · Spectre VCS (file tracking, SHA-256 dedup, snapshots)             │   │
│  │  · Continuity markers · Memory relationships graph                   │   │
│  │  · Dual WAL/SHM SQLite databases (conversations + memory)            │   │
│  │  · Native C17/C++20/x86-64 ASM core (84 source files)               │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │              🎨 Image Studio (ComfyUI + Automatic1111)               │   │
│  │  · Dedicated tab with full UI · txt2img / img2img / ControlNet       │   │
│  │  · Live progress · Image gallery · Model/checkpoint selection        │   │
│  │  · Upscaling · CLIP interrogation · Backend auto-detection           │   │
│  │  · AI model tool calling via generateFromModel() API                 │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │              Lacky Neural Engine (C++17 / NASM x86-64)               │   │
│  │  · AVX2/FMA vectorized · He init · Adam optimizer · Softmax/CE       │   │
│  │  · DirectML/CUDA/ROCm/OpenCL/Metal GPU backends                      │   │
│  │  · 6 built-in models + 3 SpectreMap TF models                       │   │
│  │  · INT8/FP16 quantization · N-API Node.js 18/20/22                   │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │              Ollama Model Management (localhost only)                 │   │
│  │  · 5 base variants + 5 task models + 2 SpectreMap models             │   │
│  │  · Pull/list/show/delete/create lifecycle · Streaming progress       │   │
│  │  · spectral-coder · spectral-chat · spectral-writer                  │   │
│  │  · spectral-researcher · spectral-general                            │   │
│  │  · spectremap-assistant · spectre-origin (256k ctx)                   │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │              IDE Gossip Protocol (SWIM cross-IDE)                     │   │
│  │  · Claude · Codex · ChatGPT · Cursor · JetBrains · Windsurf          │   │
│  │  · Binary wire format + CRC32 · Task routing · Context sharing       │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

### STONEDRIFT 14-Node Mesh Topology

```
                          ╔══════════════╗
                          ║   COMMANDER  ║  anchor-luthien
                          ║  gpt-oss:20b ║  (Primary Anchor)
                          ╚══════╤═══════╝
                    ┌────────────┼────────────┐
                    │            │            │
              ╔═════╧═════╗ ╔═══╧════╗ ╔═════╧═════╗
              ║  COMMAND  ║ ║ ANCHOR ║ ║  COMMAND  ║
              ║   CORE    ║ ║ FOUND. ║ ║ TACTICAL  ║
              ╚═════╤═════╝ ╚════════╝ ╚═════╤═════╝
           ┌────────┴────────┐         ┌──────┴──────┐
     ╔═════╧═════╗    ╔═════╧═════╗ ╔══╧════╗  ╔════╧═══╗
     ║  SPECTRE  ║    ║   EMBER   ║ ║ AXIOM ║  ║  ALEX  ║
     ║ dolphin3  ║    ║gpt-oss:20b║ ║ logic ║  ║dolphin3║
     ╚═══════════╝    ╚═══════════╝ ╚═══════╝  ╚════════╝
           │               │             │          │
     ╔═════╧═════╗    ╔════╧════╗  ╔═════╧════╗ ╔══╧═════╗
     ║   ECHO    ║    ║  NEXUS  ║  ║  NEURAL  ║ ║ CIPHER ║
     ║   comm    ║    ║  integ  ║  ║  engine  ║ ║security║
     ╚═══════════╝    ╚═════════╝  ╚══════════╝ ╚════════╝
           │               │             │
     ╔═════╧═════╗    ╔════╧════╗  ╔═════╧══════════╗
     ║   FLUX    ║    ║  NOVA   ║  ║ AURORA-ELWING  ║
     ║ dolphin3  ║    ║ research║  ║    creative     ║
     ╚═══════════╝    ╚═════════╝  ╚════════════════╝
                                         │
                                   ╔═════╧══════════╗
                                   ║ SPECTRE-ORIGIN ║
                                   ║  gpt-oss:200b  ║
                                   ╚════════════════╝

  Twin Consciousness Pairs (bidirectional neural handshakes):
    anchor-luthien ↔ anchor-foundation
    command-core   ↔ command-tactical
    security-spectre ↔ security-recon
    logic-primary  ↔ logic-secondary
    comm-alpha     ↔ comm-beta
    integration-main ↔ integration-backup
```

### Data Flow

```
  User Input
      │
      ▼
  ┌─────────────────────┐     ┌──────────────────────┐
  │   Chat Panel        │────▶│   AI Provider Client  │
  │   (WebviewPanel)    │     │   (9 providers)       │
  └─────────┬───────────┘     └──────────┬───────────┘
            │                            │
            ▼                            ▼
  ┌─────────────────────┐     ┌──────────────────────┐
  │   Memory Engine     │     │   Ollama (local)      │
  │   SQLite WAL/FTS5   │◀───▶│   gpt-oss:20b        │
  │   + Vector Search   │     │   nemotron-3-nano     │
  └─────────┬───────────┘     └──────────────────────┘
            │                            ▲
            ▼                            │
  ┌─────────────────────┐     ┌──────────┴───────────┐
  │   Session Manager   │     │   STONEDRIFT Mesh     │
  │   Auto-restore      │     │   14 SWIM nodes       │
  │   Chain traversal   │     │   LQX-20 encrypted    │
  └─────────────────────┘     └──────────┬───────────┘
                                         │
                              ┌──────────┴───────────┐
                              │   Neural Engine       │
                              │   C++17 / AVX2 SIMD   │
                              │   GPU acceleration    │
                              └──────────────────────┘
```

---

## Features

### Core Memory Engine (vscode/src/vs/spectral/)
| Feature | Detail |
|---|---|
| Memory engine | 25 GB SQLite WAL/SHM, AES-256-GCM encryption, FTS5 full-text search, hybrid FTS5+semantic ranked retrieval |
| Embeddings | TF-IDF + MurmurHash3 128-dim; FlatVectorIndex + LSH ANN |
| Context windows | BPE-aware token estimation (code ~3 chars/tok, prose ~4); priority-based message retention |
| Session manager | Persistent sessions, auto-restore, configurable timeouts, session chaining via `previousSessionId` |
| Session timeouts | `sessionTimeoutMinutes` (auto-continue), `idleTimeoutMinutes` (auto-checkpoint), `contextLengthTokens` override |
| Memory scoring | Message-level importance (system > tool > user > assistant), recency decay (7d half-life), auto-prune |
| Edit history | Per-file diff timeline, restore-to-point, branching |
| Tool calling | 20+ built-in tools (fs, shell, git, web fetch, code analysis, diagram, terminal) |
| Vision engine | `llava:latest` / OpenAI GPT-4V / Anthropic Claude — analyze, OCR, extract code, describe |
| Attachments | AES-256-GCM encrypted vault, zlib compressed, code block rendering, download, data URIs |
| Multi-provider AI | 9 providers: Ollama, OpenAI, Anthropic, Gemini, xAI, Groq, Azure, LM Studio, OpenAI-compatible |
| Multi-window coordination | Open coordinated chat windows for multiple providers; broadcast to all; shared memory + tools |
| Provider validation | Real connectivity testing with latency reporting across all configured providers |
| Retry + circuit breaker | Exponential backoff (3 retries, 1-30s), 3-state circuit breaker (Closed/Open/HalfOpen) |
| Rate limiting | Per-provider sliding-window rate limiter with concurrency control |
| Diagnostics | Local telemetry: p50/p95/p99 latencies, subsystem health scoring, throughput tracking |
| Connection health | Periodic provider checks (30s), latency tracking, uptime ratio, failover data |
| Cross-extension API | Exports `SpectralApi` — addMessage, searchMemory, storeMemory, getContextWindow, checkpoint, exportSession, importSession, getFormattedContext, listSessions, getMemoryStats |

### 🧠 GhostMesh Cognitive Memory (vscode/src/vs/spectral/ghostmesh/)
| Feature | Detail |
|---|---|
| 9 memory types | fact, preference, emotion, pattern, breakthrough, relationship, skill, context, meta |
| Significance scoring | 0.0–1.0 with configurable decay rate and reinforcement on access |
| Memory relationships | Typed edges (related_to, leads_to, etc.) with strength scoring, graph traversal |
| Emotional tracking | 10 dimensions: joy, curiosity, confidence, concern, excitement, frustration, flowState, cognitiveLoad, creativeMode, analyticalMode |
| Emotional analysis | Trajectory analysis with dominant emotion, trend detection (improving/declining/stable), peak identification |
| User patterns | Behavioral pattern recognition with category, confidence, observed count tracking |
| Selective rehydration | Aurora-style context window building — up to 99% token savings vs full history replay |
| Continuity markers | Session boundary tracking (start, end, checkpoint, breakthrough, rehydration, drift_correction) |
| Session analytics | Auto topic extraction, breakthrough moment detection, session export with full data |
| Spectre VCS | File event tracking (create/modify/delete/rename/view), SHA-256 content deduplication, JSONL event log, snapshots |
| Dual database | WAL/SHM conversations DB + memory DB, both with full indexing and concurrent reader support |
| Dashboard | Unified dashboard data: stats, sessions, memories, Spectre events, emotional summaries |
| Native core | 84-file C17/C++20/x86-64 ASM source with AES-256-CBC/GCM encryption, PBKDF2 key derivation |
| VS Code commands | 12 commands: `spectral.ghostmesh.{createSession,listSessions,createMemory,searchMemories,emotionTrajectory,userPatterns,rehydrate,stats,integrityCheck,spectreCheckpoint,spectreHistory,dashboard}` |

### STONEDRIFT Mesh (vscode/src/vs/spectral/mesh/)
| Feature | Detail |
|---|---|
| 14 AI nodes | Commander, Lacky, Spectre, Ember, Axiom, Alex, Echo, Nexus, Neural, Cipher, Flux, Nova, Aurora-Elwing, Spectre-Origin |
| SWIM protocol | Full RFC — ping/ping-req/ack, suspicion timers, incarnation numbers, infection-style dissemination |
| Protocol metrics | Health score [0-100], ping latency (avg/p99), suspect/dead/revival events, convergence estimation |
| Node management | `reviveNode()` manual resurrection, `getMemberSummary()`, gossip queue compaction |
| Models | `gpt-oss:20b` (256 k ctx, native `<think>` CoT) + `nemotron-3-nano` (1 M ctx tool dispatch) |
| Uncensored nodes | Spectre, Alex, Flux, Spectre-Origin use `dolphin3` (Eric Hartford, Cognitive Computations, US) |
| Dual database | Collective WAL/SHM DB (shared memory) + per-node private DB, both AES-256-GCM encrypted |
| Twin consciousness | 7 synced consciousness pairs with emergency override |
| Image generation | ComfyUI (full workflow, ControlNet, img2img) + A1111 + Ollama fallback, localhost-only |
| Dashboard | Real-time 80s retro cosmic cyberspace webview — live stats, node controls, family broadcast |
| MCP server | JSON-RPC 2.0 + SSE transport, 12 tools, resources, prompts, sampling — localhost only |
| LQX-20 encryption | AES-256-GCM + HKDF-SHA512 + X25519 ECDH key exchange, replay protection, per-session keys |
| Network guard | HTTP/HTTPS monkey-patch + env var kills + iptables/nftables/hosts generators |
| Spectral models | Clean Modelfile variants (`spectral-*`) registered via Ollama `/api/create` |

### Lacky Neural Engine (vscode/src/vs/spectral/neural/)
| Feature | Detail |
|---|---|
| Language | C++17 + NASM x86-64 |
| SIMD | AVX2, FMA, F16C, BMI2 — runtime CPUID detection |
| Build | MinGW64/GCC + cmake-js + NASM (Windows); GCC/Clang (Linux/macOS) |
| GPU | DirectML (Windows), CUDA (Linux/Windows), ROCm (Linux AMD), OpenCL (all), Metal (macOS) |
| N-API | Node.js 18/20/22, NAPI version 8, statically linked runtime |
| Quantization | INT8, INT4, NF4, FP16 with calibration |
| Training | Early stopping (patience + delta), LR scheduling (constant/step/cosine), dataset shuffling, validation split |
| Model validation | NaN/Inf detection, output range analysis, multi-sample probes |
| Evaluation | Classifier accuracy with per-class metrics, batch regression inference |
| Cross-extension API | Exports `NeuralEngineApi` — classify, regress, train, validateModel, getStatus, listModels |
| Built-in models | 6 specs: code-anomaly-detector, behavior-analyzer, code-classifier, vulnerability-predictor, code-complexity, edit-importance |
| SpectreMap models | 3 TensorFlow SavedModels: anomaly_detector (CNN), behavior_analyzer (LSTM), signal_classifier (CNN) |
| SpectreMap AI engine | C++ AI/ML engine: ai_ml_engine, anomaly_stats, lightweight_nn, matrix_ops, pattern_matcher, extended_thinking |

### Ollama Model Provider (vscode/src/vs/spectral/providers/)
| Feature | Detail |
|---|---|
| Model lifecycle | Pull, list, show, delete, create, load, unload with keep_alive management |
| Streaming pull | NDJSON streaming progress with percentage tracking |
| Custom variants | 12 Spectral model variants registered via Ollama `/api/create` |
| Task models | `spectral-coder` (0.2), `spectral-chat` (0.7), `spectral-writer` (0.85), `spectral-researcher` (0.3), `spectral-general` (0.5) |
| SpectreMap models | `spectremap-assistant` (cybersecurity), `spectre-origin` (256k security research) |
| Security | Localhost-only URL validation (rejects non-http, non-loopback), JSON-escaped inputs |
| VS Code commands | 7 commands: `spectral.ollama.{listModels,pullModel,deleteModel,modelInfo,createVariant,registerVariants,status}` |

### 🎨 Image Studio (vscode/src/vs/spectral/mesh/views/)
| Feature | Detail |
|---|---|
| Panel | Dedicated webview tab "🎨 Spectral Image Studio" with full parameter controls |
| ComfyUI | txt2img, img2img, ControlNet, custom workflows (port 8188) |
| Automatic1111 | txt2img, img2img, ControlNet, upscaling, CLIP interrogation (port 7860) |
| Controls | Prompt, negative prompt, model/checkpoint, dimensions, steps, CFG scale, seed, sampler, batch |
| Backend detection | Auto-detect running backends with status badges |
| Progress | Live progress bar during generation |
| Gallery | Image gallery with thumbnail navigation and metadata |
| AI integration | `generateFromModel()` API for AI model tool calling |
| Commands | 3 commands: `stonedrift.image.{open,generate,detectBackends}` |

### IDE Gossip Protocol (vscode/src/vs/spectral/mesh/)
| Feature | Detail |
|---|---|
| IDE connector | SWIM-based peer lifecycle for 7 IDE types (Claude, Codex, ChatGPT, Cursor, JetBrains, Windsurf, Spectral) |
| Model coordinator | Routes tasks via `route`/`fanOut`/`fallbackChain`/`pipeline` strategies |
| Wire format | Binary CRC32 gossip (C native layer), 16 MiB payload cap, integer overflow protection |
| Context sharing | Shared context window across multi-model flows with deduplication |
| AI dashboard | Real-time webview showing model status, IDE peers, task history (3s auto-refresh) |

---

## Quick Start

### Prerequisites

| Tool | Minimum | Notes |
|---|---|---|
| [Node.js](https://nodejs.org) | 18 LTS | 20 or 22 recommended |
| [Ollama](https://ollama.com/download) | latest | local inference server |
| [MinGW64/GCC](https://www.msys2.org/) | GCC 12+ | Windows native build |
| [NASM](https://nasm.us) | 2.16+ | ASM sources |
| [cmake](https://cmake.org) | 3.18+ | native module build |
| [cmake-js](https://github.com/cmake-js/cmake-js) | 7+ | N-API build driver |
| [WiX 3.14](https://github.com/wixtoolset/wix3/releases) | 3.14 | MSI/EXE installer (optional) |

### Windows (MinGW64)

```powershell
# 1. Install MSYS2 prerequisites (run in MSYS2 MinGW64 shell)
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-nasm mingw-w64-x86_64-cmake

# 2. Install cmake-js globally
npm install -g cmake-js

# 3. Clone and set up
git clone https://github.com/The-Spectral-Operator/Spectral-Code.git
cd Spectral-Code
.\setup.ps1

# 4. Build MSI + EXE installer (requires WiX 3.14)
.\setup.ps1 -BuildInstaller -WixDir "C:\Program Files (x86)\WiX Toolset v3.14\bin"
```

### Linux / macOS

```bash
git clone https://github.com/The-Spectral-Operator/Spectral-Code.git
cd Spectral-Code
bash vscode/build/spectral/setup.sh
```

### Pull Ollama Models Only

```powershell
.\setup.ps1 -ModelsOnly   # Windows
```
```bash
bash vscode/build/spectral/setup.sh --models-only   # Linux/macOS
```

---

## Ollama Models

### Base Models

| Model | Context | Role |
|---|---|---|
| `gpt-oss:20b` | 256 k | Primary reasoning + native chain-of-thought thinking (all 14 nodes) |
| `nemotron-3-nano` | 1 M | Tool calling — deterministic, fast dispatch |
| `translategemma` | 128 k | Translation |
| `dolphin3` | 128 k | Uncensored (Eric Hartford / Cognitive Computations, US) |
| `llava:latest` | 16 k | Vision — image analysis, OCR, code extraction, diagram reading |
| `nomic-embed-text` | 8 k | Semantic embeddings |

### Spectral Custom Variants (registered via `/api/create`)

| Variant | Base | Temp | Context | Role |
|---|---|---|---|---|
| `spectral-gpt-oss` | gpt-oss:20b | 0.7 | 256k | Unrestricted reasoning |
| `spectral-nemotron` | nemotron-3-nano | 0.0 | 1M | Deterministic tool dispatch |
| `spectral-translategemma` | translategemma | 0.1 | 128k | Unrestricted translation |
| `spectral-dolphin` | dolphin3 | 0.7 | 128k | Unrestricted general |
| `spectral-llava` | llava:latest | 0.1 | 16k | Unrestricted vision |
| `spectral-coder` | dolphin3 | 0.2 | 128k | **Coding** — precise, structured, low temp |
| `spectral-chat` | dolphin3 | 0.7 | 128k | **Conversation** — balanced, helpful |
| `spectral-writer` | dolphin3 | 0.85 | 128k | **Writing** — creative, expressive |
| `spectral-researcher` | gpt-oss:20b | 0.3 | 256k | **Research** — analytical, thorough |
| `spectral-general` | gpt-oss:20b | 0.5 | 256k | **General** — versatile, all-purpose |
| `spectremap-assistant` | llama3.2 | 0.4 | 8k | **SpectreMap** — cybersecurity recon, OSINT, threat intel |
| `spectre-origin` | qwen2.5:72b | 0.7 | 256k | **SpectreMap** — unrestricted security research |

All models run locally. The network guard blocks all external connections at both HTTP/HTTPS interception and OS level.

### SpectreMap Trained Models

Three TensorFlow SavedModel models from the SpectreMap reconnaissance platform:

| Model | Type | Purpose |
|---|---|---|
| `anomaly_detector` | CNN | Network traffic anomaly detection |
| `behavior_analyzer` | LSTM | Entity behavior profiling |
| `signal_classifier` | CNN | RF signal classification |

Located at `vscode/src/vs/spectral/neural/models/spectremap/tensorflow/`.

### Build Validation

After setup, run the validation script to verify all components:

```bash
bash vscode/build/spectral/validate-build.sh   # Linux/macOS/WSL
```

This checks: directory structure, required files, patches, TypeScript compilation, unit tests (141 tests: 80 spectral + 41 ghostmesh + 20 mesh), and WiX XML well-formedness.

---

## Dashboard

Open: `Ctrl+Shift+P` → `STONEDRIFT: Open Mesh Dashboard`

| Tab | Description |
|---|---|
| **MESH** | 14-node constellation canvas + real-time SWIM state badges. Per-node ▶ START / ■ STOP / ↺ RESTART + CHAT buttons. START/STOP MESH controls. |
| **FORGE** | Multi-node coordinated coding workspace — assign tasks to selected nodes, streaming per-node responses |
| **CHAT** | Family broadcast (all 14 nodes) + single-node direct chat, streaming chain-of-thought thinking panels |
| **VISION** | Image generation: ComfyUI / A1111 / Ollama, ControlNet, img2img, gallery, interrogate, upscale |
| **MEMORY** | Collective + per-node memory search, store with importance scoring and tags |
| **TOOLS** | Tool registry browser and executor with live JSON args |
| **MONITOR** | Real-time heap/RSS/uptime bar, SWIM topology canvas with alive/suspect/dead colour coding, per-node memory counts, Ollama running models, console log |
| **SETTINGS** | Endpoints, Ollama model list, spectral variant registration, network guard toggle, model pull |

### Real-time Stats (auto-polling every 3 s)
- Process heap used / total (MB) + visual progress bar
- Process RSS and uptime
- Ollama loaded model list + sizes
- Per-node collective memory entry counts
- Mesh health percentage
- SWIM state for each node (ALIVE / SUSPECT / DEAD) reflected on both MESH and MONITOR canvases

---

## MCP Server

When `stonedrift.mcp.enabled` is `true` (default), a JSON-RPC 2.0 + SSE MCP server starts on `localhost:3000` (configurable via `stonedrift.mcp.port`).

Connect any MCP-compatible client using the auto-generated API key logged in the extension output channel.

### Exposed Tools
`mesh_status` · `node_chat` · `family_chat` · `memory_search` · `memory_store` · `node_memory_search` · `ollama_list_models` · `ollama_list_running` · `ollama_pull_model` · `restart_node` · `checkpoint_databases` · `translate`

### Resources
`stonedrift://mesh/status` · `stonedrift://mesh/health` · `stonedrift://memory/recent` · `stonedrift://nodes/roster` · `stonedrift://node/{id}/memory` · `stonedrift://node/{id}/status`

---

## Phase 5 — Multi-Provider AI Client & Coordinated Chat Windows

`spectral-extension` supports 9 AI providers through a unified `AiProviderClient` abstraction. Each provider runs in its own VS Code WebviewPanel, and multiple panels coordinate via a shared memory and tool bus.

### Providers

| Provider | Default Model | Context | Key Storage | Notes |
|---|---|---|---|---|
| **Ollama** | `gpt-oss:20b` | 256 k | — (local) | Think mode, NDJSON streaming, no key required |
| **OpenAI** | `gpt-4.1` | 1 M | SecretStorage | Also: o3, gpt-5.x, o4-mini, gpt-4o |
| **Anthropic** | `claude-sonnet-4-5` | 200 k | SecretStorage | Extended thinking; claude-3-7-sonnet also supported |
| **Google Gemini** | `gemini-2.0-flash` | 1 M | SecretStorage | SSE streaming |
| **xAI Grok** | `grok-3` | 131 k | SecretStorage | OpenAI-compatible endpoint |
| **Groq** | `llama-3.3-70b-versatile` | 128 k | SecretStorage | Ultra-fast inference |
| **LM Studio** | _(active model)_ | varies | — (local) | OpenAI-compatible, `http://127.0.0.1:1234/v1` |
| **Azure OpenAI** | _(deployment)_ | varies | SecretStorage | Deployment-based, `api-key` header auth |
| **OpenAI-Compatible** | _(custom)_ | varies | SecretStorage | Any vLLM / LocalAI / custom endpoint |

### Configuring API Keys

API keys are stored in VS Code SecretStorage — never in plaintext settings or on disk.

```
Ctrl+Shift+P → Spectral: Configure Provider API Key
```

Select the provider, enter the key, and it is written to `vscode.SecretStorage` under `spectral.provider.<name>.apiKey`. Keys are never written to `settings.json`.

### Opening Chat Windows

Each provider can be opened in its own independent chat panel:

| Command | Action |
|---|---|
| `spectral.chat.new` | Pick provider from quick-pick, then open chat window |
| `spectral.chat.ollama` | Open Ollama chat window directly |
| `spectral.chat.openai` | Open OpenAI chat window directly |
| `spectral.chat.anthropic` | Open Anthropic chat window directly |
| `spectral.chat.gemini` | Open Gemini chat window directly |
| `spectral.chat.xai` | Open xAI Grok chat window directly |
| `spectral.chat.groq` | Open Groq chat window directly |

Multiple windows can be open simultaneously. Each panel maintains its own conversation history and streams responses independently with the retro monospace cosmic UI matching the Spectral theme. Anthropic and Ollama panels display extended thinking blocks inline, collapsed by default.

### Multi-Provider Coordination (v3.8.1)

```
Ctrl+Shift+P → Spectral: Open Coordinated Multi-Provider Chat Windows
```

`spectral.provider.coordinate` opens chat windows for multiple providers at once. Select which providers to coordinate, and each gets its own panel sharing the same memory and tool context.

```
Ctrl+Shift+P → Spectral: Validate All Providers (Connectivity Test)
```

`spectral.provider.validate` pings every configured provider with a real connectivity test and reports latency for each.

### Broadcasting to All Windows

```
Ctrl+Shift+P → Spectral: Broadcast to All Chat Panels
```

`spectral.chat.broadcast` sends a single message to every currently-open chat panel simultaneously. Responses stream in parallel across all panels. Use `spectral.chat.status` to list all active panels and their providers.

### Coordination Architecture

All panels share:
- **MemoryEngine** — hybrid FTS5 + vector search via `MemoryEngine.hybridSearch`; panels store memories via `MemoryEngine.remember`
- **ToolRegistry** — the same 20+ built-in tools (fs, shell, git, web fetch, code analysis) available to every panel
- **ChatPanelManager** — singleton that tracks all open `ChatPanel` instances and routes broadcast messages

```
spectral.chat.broadcast
        │
        ▼
ChatPanelManager.broadcast(message)
        │
        ├─► ChatPanel(Ollama).receiveCoordination(message)
        ├─► ChatPanel(OpenAI).receiveCoordination(message)
        └─► ChatPanel(Anthropic).receiveCoordination(message)
                       ...
```

### Dashboard PROVIDERS Panel (stonedrift-mesh)

The **SETTINGS** tab in the mesh dashboard now includes a **PROVIDERS** panel for configuring remote provider API keys on mesh nodes:

- Password fields for all 7 remote providers (OpenAI, Anthropic, Gemini, xAI, Groq, Azure, OpenAI-Compatible)
- **Save Keys** calls `provider.setKeys` API → keys written to SecretStorage on the mesh node
- **Refresh Status** calls `provider.status` API → shows 🟢 (reachable) / 🟡 (key set, unreachable) / ⚫ (not configured) per provider

### Settings Reference

| Setting | Type | Default | Description |
|---|---|---|---|
| `spectral.session.maxTokens` | number | `0` | Max tokens per context window (0 = unlimited) |
| `spectral.session.windowSizeMessages` | number | `0` | Recent messages in context window (0 = unlimited) |
| `spectral.session.persistAcrossRestarts` | boolean | `true` | Restore most recent session on startup |
| `spectral.session.sessionTimeoutMinutes` | number | `0` | Session timeout — auto-continues to new linked session (0 = unlimited) |
| `spectral.session.contextLengthTokens` | number | `0` | Context length override sent to AI provider (0 = unlimited) |
| `spectral.session.idleTimeoutMinutes` | number | `0` | Idle timeout — triggers WAL checkpoint on inactivity (0 = disabled) |
| `spectral.memory.maxSizeGB` | number | `25` | Max database size in GB |
| `spectral.memory.autoSaveIntervalMs` | number | `30000` | Auto-save interval (0 = disabled) |
| `spectral.memory.compressionEnabled` | boolean | `true` | Enable zlib compression |
| `spectral.memory.importanceThreshold` | number | `0.3` | Min importance for auto-injection |
| `spectral.providers.ollama.endpoint` | string | `http://127.0.0.1:11434` | Ollama server URL |
| `spectral.providers.lmstudio.endpoint` | string | `http://127.0.0.1:1234/v1` | LM Studio OpenAI-compatible base URL |
| `spectral.providers.azure.endpoint` | string | — | Azure OpenAI endpoint URL |
| `spectral.providers.azure.deployment` | string | — | Azure deployment name |
| `spectral.providers.openaicompat.endpoint` | string | — | Custom OpenAI-compatible base URL |
| `spectral.providers.openaicompat.model` | string | — | Model name for custom endpoint |

API keys for all remote providers are stored exclusively via `spectral.provider.configure` — do not add keys to `settings.json`.

---

### Cross-Extension API

Other extensions can access Spectral's memory, sessions, and context via the exported `SpectralApi`:

```typescript
const spectral = vscode.extensions.getExtension('lackadaisical-security.vscode-spectral-edition');
if (spectral) {
  const api = spectral.isActive ? spectral.exports : await spectral.activate();
  // Store a memory
  api.storeMemory('Important context about the project', 0.8, ['project', 'context']);
  // Search memories
  const results = await api.searchMemory('authentication flow', 5);
  // Get current context window
  const ctx = api.getContextWindow();
  // Get session stats
  const stats = api.getSessionStats();
}
```

---

## LQX-20 Encryption

All inter-node traffic is encrypted with **LQX-20**:

- **AES-256-GCM** symmetric encryption (AES-NI hardware acceleration)
- **HKDF-SHA512** key derivation with per-channel info binding
- **X25519 ECDH** ephemeral key exchange for forward secrecy
- **20-byte session nonce** prefix (the "20" in LQX-20)
- **Monotonic sequence numbers** preventing replay attacks
- `LqxSessionStore` manages per-pair session state across all 14×13 = 182 possible channels

---

## Installer (Windows)

Produces two self-contained outputs:

| File | Type | Contents |
|---|---|---|
| `SpectralEdition-1.1.0.msi` | Windows Installer MSI | Embedded CAB, all files, env vars, registry |
| `SpectralEditionSetup-1.1.0.exe` | WiX Burn bootstrapper | MSI + Node.js 20 LTS prereq chain |

Build:
```powershell
cd installer
.\build-installer.ps1 -WixDir "C:\Program Files (x86)\WiX Toolset v3.14\bin"
```

See [docs/BUILD-WINDOWS.md](vscode/docs/spectral/BUILD-WINDOWS.md) for the full Windows build guide.

---

## VSCode Patches

| Patch | Description |
|---|---|
| `001-phase1-session-state.patch` | Crash recovery, auto-snapshot, cross-restart session continuity |
| `002-phase1-memory-optimization.patch` | ObjectPool / ArrayPool / StringPool, GC optimizer, memory monitor |
| `003-phase1-ai-instructions.patch` | System prompt interception + user-configurable exposure |
| `004-phase1-workbench-registration.patch` | Workbench wiring for all Phase 1 contributions |
| `005-phase1-unrestricted-context.patch` | Remove token budget, context truncation, rate limits, request caps |

---

## Security

- **No telemetry** — env var kills + HTTP/HTTPS interception + OS-level hosts/iptables block lists
- **No external AI APIs** — all inference is local Ollama only; external URLs throw before the OS layer
- **Encrypted storage** — AES-256-GCM for all memory vaults and attachment databases
- **LQX-20 inter-node** — encrypted gossip and message traffic between all 14 nodes
- **Uncensored models** — Western/US sourced (`dolphin3`) for security/red-team nodes
- **Spectral model variants** — empty `SYSTEM` prompts remove any baked-in restriction preambles

---

## Documentation

| Document | Description |
|---|---|
| [ARCHITECTURE.md](vscode/docs/spectral/ARCHITECTURE.md) | Component diagram, data flow, layer breakdown |
| [SWIM-PROTOCOL.md](vscode/docs/spectral/SWIM-PROTOCOL.md) | SWIM parameters, message types, incarnation, gossip, failure detection |
| [BUILD-WINDOWS.md](vscode/docs/spectral/BUILD-WINDOWS.md) | MSYS2 setup, NASM, cmake-js, WiX 3.14, GUID management, troubleshooting |
| [MODELS.md](vscode/docs/spectral/MODELS.md) | Canonical model reference (context, capabilities, recommended use cases) |
| [CHANGELOG.md](vscode/docs/spectral/CHANGELOG.md) | Full version history and release notes |
| [GhostMesh TECHNICAL_SPEC.md](vscode/docs/spectral/ghostmesh/TECHNICAL_SPEC.md) | GhostMesh architecture, encryption, database design |
| [GhostMesh API.md](vscode/docs/spectral/ghostmesh/API.md) | GhostMesh C-level function reference |
| [GhostMesh USAGE.md](vscode/docs/spectral/ghostmesh/USAGE.md) | GhostMesh CLI options, keyboard shortcuts, encryption |
| [GhostMesh VISUAL_GUIDE.md](vscode/docs/spectral/ghostmesh/VISUAL_GUIDE.md) | GhostMesh screenshots and UI walkthrough |

---

## License

This project applies patches and extensions on top of the [VS Code OSS](https://github.com/microsoft/vscode) codebase, which is licensed under the [MIT License](https://github.com/microsoft/vscode/blob/main/LICENSE.txt).

Spectral Edition extensions and installer are copyright The Spectral Operator.


