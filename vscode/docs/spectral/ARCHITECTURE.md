# System Architecture – Spectral Code

## Overview

Spectral Code is a custom VS Code fork with Spectral enhancements integrated directly into the editor core at `vscode/src/vs/spectral/`:

```
┌────────────────────────────────────────────────────────────────────────────┐
│                        Developer Workstation                                │
│                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                       Spectral Code                                │   │
│  │               (vscode/src/vs/spectral/)                            │   │
│  │                                                                     │   │
│  │  ┌─────────────────────────┐   ┌─────────────────────────────────┐  │   │
│  │  │    Core Engine          │   │      STONEDRIFT Mesh            │  │   │
│  │  │   (spectral/)           │   │     (spectral/mesh/)            │  │   │
│  │  │                         │   │                                 │  │   │
│  │  │  SessionManager         │   │  MeshManager                    │  │   │
│  │  │  MemoryEngine (SQLite)  │   │    └─ SWIMProtocol (RFC full)   │  │   │
│  │  │  EmbeddingEngine        │   │    └─ 14 StoneNode instances    │  │   │
│  │  │  ContextWindow          │   │         ├─ OllamaClient         │  │   │
│  │  │  EditHistory            │   │         └─ DualDatabase         │  │   │
│  │  │  ToolRegistry (20+)     │   │  TwinConsciousness (7 pairs)    │  │   │
│  │  │  ToolExecutor           │   │  ImageGenerationEngine          │  │   │
│  │  │  VisionEngine           │   │  NetworkGuard                   │  │   │
│  │  │  AttachmentManager      │   │  ModelfileCreator               │  │   │
│  │  └─────────────────────────┘   │  MCP Server (JSON-RPC 2.0+SSE) │  │   │
│  │                                │  LQX-20 Encryption              │  │   │
│  │                                │  DashboardApi (11 MESH_TOOLS)   │  │   │
│  │                                │  DashboardProvider (webview)    │  │   │
│  │                                └─────────────────────────────────┘  │   │
│  │                                                                     │   │
│  │  ┌─────────────────────────────────────────────────────────────┐   │   │
│  │  │            lacky-neural-engine (.node native)                │   │   │
│  │  │  InferenceService · ModelManager · 6 built-in models        │   │   │
│  │  │  C++17 / NASM AVX2 SIMD  ·  N-API v8  ·  MinGW64/GCC        │   │   │
│  │  │  DirectML / OpenCL / CUDA / ROCm / Metal                    │   │   │
│  │  └─────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                            │
│  ┌────────────────────────────┐   ┌────────────┐   ┌───────────────────┐  │
│  │  Ollama (localhost:11434)  │   │  ComfyUI   │   │  A1111 WebUI      │  │
│  │  gpt-oss:20b               │   │  :8188     │   │  :7860            │  │
│  │  nemotron-3-nano           │   └────────────┘   └───────────────────┘  │
│  │  translategemma            │                                           │
│  │  dolphin3                  │                                           │
│  │  llava · nomic-embed-text  │                                           │
│  └────────────────────────────┘                                           │
└────────────────────────────────────────────────────────────────────────────┘
```

All communication is **localhost only**. The NetworkGuard blocks all external connections.

---

## spectral core

### MemoryEngine

Backed by SQLite with WAL journal mode and SHM shared memory. The database cap is configured at 25 GB. All content encrypted at rest with AES-256-GCM using a key derived via PBKDF2 (100,000 iterations, SHA-512).

```
MemoryEngine
├── SQLite (WAL/SHM, 25GB cap, AES-256-GCM)
│   ├── memories (id, content, importance, node_id, tags, embedding_id, ts)
│   ├── memories_fts (FTS5 virtual table → content)
│   └── embeddings (id, vector BLOB, model, dims)
├── EmbeddingEngine → nomic-embed-text via Ollama
└── VectorSearch (cosine similarity, hybrid BM25+vector)
```

### SessionManager

Persists the full conversation state including messages, active files, tool call history, and edit snapshots. Sessions auto-restore on VS Code restart.

**Configurable session windows (v3.8.0)**:
- `sessionTimeoutMinutes` — auto-continue sessions into linked sessions on expiry (0 = unlimited)
- `contextLengthTokens` — override context length sent to provider (0 = unlimited)
- `idleTimeoutMinutes` — trigger WAL checkpoint on idle (0 = disabled)
- Activity tracking via `touchActivity()` / `lastActivityAt`
- Duration tracking via `durationMs` (computed from `createdAt`)
- Session chaining: expired sessions store `previousSessionId` in metadata for traversal

### ContextWindow

BPE-aware token estimation handles code (~3 chars/token), special characters (~2), and prose (~4). Priority-based message retention scores messages by role (system=100, tool=80, user=60, assistant=40) and uses adaptive truncation. Summaries are priority-sorted so high-importance dropped messages appear first.

### MemoryEngine – Scoring & Pruning

- **Message importance**: role-based weight + length bonus + code block detection + error pattern detection
- **Memory importance**: `0.4 × manual + 0.4 × recency_decay(7d_halflife) + 0.2 × access_freq(log)`
- **Decay pruning**: `decayPrune(threshold)` removes entries below scored importance
- **Auto-prune**: `startAutoPrune(intervalMs, threshold)` runs periodic decay cycle

### Cross-Extension API (SpectralApi)

spectral core exports a public API surface via `vscode.extensions.getExtension().exports`:
- `addMessage()` — inject messages into the active session
- `getContextWindow()` — retrieve the windowed context (honouring all limits)
- `searchMemory()` / `storeMemory()` — hybrid FTS5+semantic memory access
- `getSessionStats()` — session metadata (duration, tokens, message count)
- `checkpoint()` — force WAL checkpoint
- `dbStats()` — database size/page metrics
- `exportSession()` / `importSession()` — session serialization
- `getFormattedContext()` — formatted context with optional dropped-message summaries
- `listSessions()` — enumerate all sessions
- `getMemoryStats()` — memory engine diagnostics

stonedrift-mesh consumes this API to sync high-importance collective memories.

### Multi-Provider AI Coordination (v3.8.0)

9 providers are supported simultaneously: Ollama, OpenAI, Anthropic, Gemini, xAI, Groq, Azure OpenAI, LM Studio, OpenAI-compatible.

- **ChatPanelManager**: independent webview panels per provider, each with its own conversation history
- **Broadcast**: send a message to all open panels simultaneously
- **Coordinate**: open multiple provider windows in one action (`spectral.provider.coordinate`)
- **Validate**: test all provider connectivity with latency reporting (`spectral.provider.validate`)
- **API keys**: stored in VS Code SecretStorage (never in settings.json)
- **Production hardening**: CircuitBreaker (5-fail threshold, 60s reset), RateLimiter (per-provider sliding window), ConnectionHealthMonitor (30s polling)

### Cross-Extension API (NeuralEngineApi)

lacky-neural-engine exports via `vscode.extensions.getExtension().exports`:
- `classify()` / `regress()` — run inference on named models
- `train()` — train with early stopping, LR scheduling, validation split
- `getStatus()` / `listModels()` — hardware info and model registry

### ToolRegistry + ToolExecutor

Built-in tools are registered at startup. External tools can be registered by other extensions. Tool calls are dispatched in OpenAI function-calling format and can be routed to any Ollama model.

---

## stonedrift-mesh

### SWIM Protocol

Spectral Edition implements the SWIM (Scalable Weakly-consistent Infection-style Process group Membership) protocol for 14-node health tracking:

- **Probe period**: 1 second
- **Probe timeout**: 200 ms
- **Indirect probes (k)**: 3
- **Suspicion multiplier**: 5
- **Gossip fanout (λ)**: log₂(N) ≈ 4
- **Incarnation**: monotone counter for override correctness

See [SWIM-PROTOCOL.md](SWIM-PROTOCOL.md) for full details.

**Protocol metrics (v3.8.0)**:
- `getMetrics()` returns: ping/ack counts, suspect/dead/revival events, gossip sent/received, protocol periods, uptime
- Ping latency tracking: average and p99 over last 200 samples
- Health score [0–100]: weighted combination of alive ratio (40%), suspect rate (20%), ack success rate (20%), dead penalty (20%)

### Dual Database

```
Collective DB (collective.db)
├── WAL/SHM, AES-256-GCM, FTS5
├── All 14 nodes read/write
└── Shared memory: facts, events, decisions, coordination

Per-node DB ({nodeId}.db)  ×14
├── WAL/SHM, AES-256-GCM
├── Private to each node
└── Personal memory, conversation history, private vault
```

### Node Architecture

Each node is an instance of `NodeBase` with:
- `chat()` — streaming chat with `gpt-oss:20b`, native thinking mode
- `toolCall()` — tool dispatch via `nemotron-3-nano` (1M context)
- `translate()` — via `translategemma` (128k context)
- `vision()` — image analysis via `llava`
- `searchMemory()` — hybrid FTS5 + cosine similarity
- Auto-remember: stores important messages in private DB
- Auto-coordinate: shares tagged memories with collective DB

### OllamaClient

The Ollama client enforces `assertLocalUrl()` on every HTTP call. No request is ever sent to a non-localhost URL. Streaming uses NDJSON (`Content-Type: application/x-ndjson`) with full backpressure handling.

```
OllamaClient
├── think()        → gpt-oss:20b, think:true, 256k ctx, temp 0.6
├── toolCall()     → nemotron-3-nano, tools[], 1M ctx, temp 0.0
├── uncensoredChat()→ dolphin3, 128k ctx, temp 0.7
├── translate()    → translategemma, 128k ctx, temp 0.1
├── vision()       → llava:latest, base64 images
├── embed()        → nomic-embed-text
├── chatStream()   → NDJSON stream, AsyncGenerator<StreamChunk>
└── assertLocalUrl()→ throws on any non-localhost URL
```

### NetworkGuard

Three-layer protection:

1. **Env vars** — `OLLAMA_NO_ANALYTICS=1`, `DO_NOT_TRACK=1`, `HUGGINGFACE_HUB_OFFLINE=1`, `WANDB_DISABLED=true`, etc.
2. **HTTP/HTTPS monkey-patch** — Node.js `http.request` and `https.request` wrapped; any non-localhost hostname throws `ECONNREFUSED` before a socket is opened
3. **OS-level generators** — `generateIptablesRules()`, `generateNftablesRules()`, `generateHostsBlock()` output rules/entries that can be applied at the OS level for defence-in-depth

---

## lacky-neural-engine

### Build targets

| Platform | Compiler | Assembler | GPU |
|---|---|---|---|
| Windows | MinGW64 GCC 12+ | NASM 2.16+ (`win64`) | DirectML, OpenCL |
| Linux | GCC or Clang | NASM (`elf64`) | CUDA, OpenCL |
| macOS | Clang | NASM (`macho64`) | Metal (Accelerate) |

### SIMD dispatch

Runtime CPU feature detection via `cpuid` (NASM `cpu_detect_x64.asm`). Dispatcher selects:
- **AVX-512** path (if available)
- **AVX2 + FMA + F16C** path (baseline target: `x86-64-v3`)
- **SSE4.2** fallback
- **Generic C++** last resort

### N-API

NAPI version 8. No C++ exceptions (`-fno-exceptions` / `NAPI_DISABLE_CPP_EXCEPTIONS`). Statically linked runtime on Windows (`-static-libgcc -static-libstdc++`) so the `.node` file is self-contained.

---

## Image Generation

```
ImageGenerationEngine
├── detect()          → probes localhost:8188 (ComfyUI) and localhost:7860 (A1111)
├── generateComfy()   → POST /prompt → poll /history/{id} → GET /view
│   └── buildComfyTxt2ImgWorkflow() → KSampler→VAEDecode→SaveImage node graph
│       ├── img2img: ETN_LoadImageBase64 + VAEEncode
│       └── ControlNet: ControlNetLoader + ControlNetApplyAdvanced
├── generateA1111()   → POST /sdapi/v1/txt2img or /sdapi/v1/img2img
│   ├── ControlNet: alwayson_scripts.ControlNet
│   ├── Upscale: POST /sdapi/v1/extra-single-image
│   └── Interrogate: POST /sdapi/v1/interrogate
└── generateOllama()  → fallback only
```

All image URLs are validated with `assertLocal()`. No external image generation APIs.

---

## Installer (Windows)

```
build-installer.ps1
├── Step 1: cmake-js build → lacky_neural_engine.node (MinGW64 + NASM)
├── Step 2: cmake build → spectral_ca.dll (Win32 CA DLL, MinGW64)
├── Step 3: tsc → dist/spectral core, dist/stonedrift-mesh, dist/lacky-neural-engine
├── Step 4: heat.exe → fragment .wxs files (optional, for prod)
├── Step 5: candle.exe → Product.wxs + Components.wxs + UI.wxs → *.wixobj
├── Step 6: light.exe → SpectralEdition.msi (embedded CAB, self-contained)
└── Step 7: candle + light -ext WixBalExtension → SpectralEditionSetup.exe (Burn)
             └── Chains: VC++ Redist + Node.js 20 + SpectralEdition.msi
```

Custom action DLL (`spectral_ca.dll`) is a 64-bit Win32 DLL compiled with MinGW64, statically linked, that runs in Windows Installer's deferred execution context. It uses WinHTTP (not Node.js) to communicate with the local Ollama REST API for model registration.

### Installer Resources (v3.8.0)

```
installer/res/
├── spectral.ico          – 16/32/48/256 px ICO (purple gradient)
├── banner.bmp            – 493×58 WiX top banner
├── dialog.bmp            – 493×312 WiX welcome/license background
├── logo.png              – 64×64 EXE bootstrapper logo
├── license.rtf           – MIT license with Spectral Edition terms
├── spectral-theme.xml    – WiX Burn bootstrapper theme (Install/Progress/Success/Failure pages)
└── spectral-en-us.wxl    – Localization strings (en-US)
```

### Build Validation

Run `bash validate-build.sh` to verify all components:
1. Directory structure (8 required directories)
2. Required files (35+ source/config/resource files)
3. Patches (5 patches, all non-empty)
4. TypeScript compilation (3 extensions, 0 errors)
5. Unit tests (44 tests, 0 failures)
6. WiX XML well-formedness (4 XML files)

---

## lacky-neural-engine – Inference & Model Manager

### InferenceService

Wraps the native addon with a typed TypeScript API. Handles single inference, batch inference, and supervised training.

```
InferenceService
├── loadAddon(extensionRoot)          → load .node from build/Release/
├── detectHardware()                  → CpuInfo + GpuInfo
├── createNetwork(name, topology, ...) → NeuralNetworkBuilder.build()
├── createClassificationNetwork()     → ReLU hidden + Softmax output
├── createRegressionNetwork()         → ReLU hidden + Linear output
├── classify(name, Float32Array)      → ClassificationResult (argmax, scores, confidence, latencyMs)
├── regress(name, Float32Array)       → RegressionResult (values, latencyMs)
├── classifyBatch(name, inputs[])     → BatchResult (results[], totalMs, avgMs)
└── train(name, inputs, targets, ...) → TrainingResult (loss, epochs, trainingMs)
```

### ModelManager

Lifecycle registry for named networks. Auto-loads all 6 built-in code intelligence models at VS Code startup.

| Model name | Task | Topology | Params | Use case |
|---|---|---|---|---|
| `code-anomaly-detector` | classification | 128→256→128→2 | ~66k | Code smell / bug detection |
| `behavior-analyzer` | classification | 120→128→64→8 | ~25k | Coding behaviour profiling |
| `code-classifier` | classification | 256→512→256→18 | ~263k | Language / framework classification |
| `vulnerability-predictor` | classification | 200→256→128→10 | ~85k | Security risk prediction |
| `code-complexity` | regression | 64→128→64→1 | ~17k | Cyclomatic complexity estimate |
| `edit-importance` | regression | 32→64→32→1 | ~4k | Edit importance scoring for memory |

---

## stonedrift-mesh – Tool System

### MESH_TOOLS (11 built-in tools)

Registered in `dashboardApi.ts` as `MESH_TOOLS[]`. Each tool has a name, description, typed params array, and an async `execute(args, api)` function with direct access to the `DashboardApi` private members.

| Tool | Description |
|---|---|
| `mesh_status` | Current status of all 14 nodes (SWIM state, running, memory count) |
| `node_chat` | Send a message to a specific node, receive thinking + response |
| `memory_search` | Full-text + vector hybrid search of collective DB |
| `memory_store` | Store a new memory entry in collective DB with importance and tags |
| `ollama_list_models` | All models installed in local Ollama |
| `ollama_list_running` | Models currently loaded in GPU/RAM |
| `ollama_pull_model` | Pull a model from Ollama registry |
| `translate` | Translate text via translategemma (128k ctx) |
| `restart_node` | Restart a named mesh node |
| `checkpoint_databases` | Force WAL checkpoint on all databases |
| `generate_image` | Generate image via ComfyUI / A1111 / Ollama |
| `mesh_health` | Mesh health report: alive/suspect/dead counts, health % |

---

## LQX-20 Encryption

Encrypts all inter-node gossip messages and MCP payloads.

```
LQX-20 Encrypt(plaintext, recipientPublicKey)
  1. Generate ephemeral X25519 key pair
  2. ECDH(ephemeralPrivate, recipientPublicKey) → sharedSecret
  3. HKDF-SHA512(sharedSecret, salt, info) → aesKey (32B) + iv (12B)
  4. Prepend 20-byte nonce (8B epoch millis + 12B random)
  5. AES-256-GCM encrypt(plaintext, aesKey, fullIv=nonce[0:12] XOR iv)
  6. Append 16-byte auth tag
  7. Output: [ephemeralPublicKey(32B)][nonce(20B)][ciphertext][tag(16B)]

LqxSessionStore
  • One session per (localNodeId, remoteNodeId) pair
  • 14 nodes → up to 182 concurrent sessions
  • Sequence number prevents replay: each message must have seq > lastSeen
```

---

## MCP Server

Runs on `localhost:{stonedrift.mcp.port}` (default: 3000). Requires `stonedrift.mcp.enabled: true`.

### Transport

JSON-RPC 2.0 over HTTP + Server-Sent Events (SSE). SSE endpoint at `GET /sse`. Commands sent via `POST /message?sessionId=<id>`.

### Capabilities

| Category | Items |
|---|---|
| **Tools** | 12: `mesh_status`, `node_chat`, `family_chat`, `memory_search`, `memory_store`, `node_memory_search`, `ollama_list_models`, `ollama_list_running`, `ollama_pull_model`, `restart_node`, `checkpoint_databases`, `translate` |
| **Resources** | 6: `stonedrift://mesh/status`, `stonedrift://mesh/health`, `stonedrift://memory/recent`, `stonedrift://nodes/roster`, `stonedrift://node/{id}/memory`, `stonedrift://node/{id}/status` |
| **Prompts** | 3: `analyze_code`, `review_security`, `explain_code` |
| **Sampling** | LLM sampling via `gpt-oss:20b` with optional thinking |

---

## Real-time Dashboard Data Flow

```
VS Code Extension Host
  └── DashboardProvider.createWebviewPanel()
        └── DashboardApi.attach(panel.webview)
              ├── onDidReceiveMessage → handle(msg) → route to private method
              └── _push(event, data) → panel.webview.postMessage()

Webview (dashboard.html)
  ├── post(command, data) → Promise → await response from extension
  ├── postStream(command, data, onChunk) → streams server-sent chunks
  ├── state.statsInterval = setInterval(→ post('stats.full'), 3000)
  ├── state.meshPollInterval = setInterval(→ post('mesh.status'), 5000)
  └── Event push handlers:
        stats.fullPush   → updateStats(s)
        mesh.statusPush  → updateMeshStatus(data)
        node.chunk       → handleChatChunk(msg)
        node.reply       → handleFamilyReply(msg)
        spectral.progress → handleSpectralProgress(msg)
        model.pull.progress → handlePullProgress(msg)
        image.progress   → handleImageProgress(msg)
```

---

## Phase 5 — Multi-Provider Architecture

### Component Overview

```
VS Code Commands (spectral.chat.*)
        │
        ▼
ChatPanelManager (singleton)
        │
        ├─ openForProvider(provider) ──► ChatPanel (WebviewPanel)
        │                                      │
        ├─ broadcast(message) ─────────────────┤
        │                                      ▼
        └─ closeAll()                   AiProviderClient
                                               │
                                     ProviderRegistry (singleton)
                                               │
                     ┌─────────────────────────┼──────────────────────────┐
                     ▼                         ▼                          ▼
             OllamaClient             OpenAIClient             AnthropicClient
             (localhost)          (api.openai.com)         (api.anthropic.com)
                     │                         │                          │
                     ▼                         ▼                          ▼
             GeminiClient              GrokClient                  GroqClient
     (generativelanguage            (api.x.ai)               (api.groq.com)
      .googleapis.com)                    │
                                          ▼
                             AzureOpenAIClient / OpenAICompatClient
```

### Provider Capability Matrix

| Provider | Wire Format | Tool Calling | Vision | Thinking |
|---|---|---|---|---|
| Ollama | NDJSON (`\n`-delimited) | OpenAI `tools[]` | base64 in `images[]` | `options.think: true` |
| OpenAI | SSE `data:` | OpenAI `tools[]` | `image_url` in content | `reasoning_effort` (o-series) |
| Anthropic | SSE `event:`/`data:` | `tool_use` content blocks | base64 or URL in content | `thinking: {type, budget_tokens}` |
| Google Gemini | SSE `data:` | `functionDeclarations` | `inlineData` / `fileData` | — |
| xAI Grok | SSE `data:` (OpenAI-compat) | OpenAI `tools[]` | `image_url` in content | — |
| Groq | SSE `data:` (OpenAI-compat) | OpenAI `tools[]` | `image_url` in content | — |
| LM Studio | SSE `data:` (OpenAI-compat) | OpenAI `tools[]` | `image_url` in content | — |
| Azure OpenAI | SSE `data:` (OpenAI-compat) | OpenAI `tools[]` | `image_url` in content | — |
| OpenAI-Compatible | SSE `data:` (OpenAI-compat) | OpenAI `tools[]` | provider-dependent | — |

### Format Translation

`AiProviderClient` normalises all messages to an internal OpenAI-style format and translates to each provider's wire format at call time.

**Tool calling**

```
Internal (OpenAI-style)                   Anthropic wire
───────────────────────────               ────────────────────────────────────
{ type: "function",               →       { type: "tool_use",
  function: { name, args } }                id, name, input: args }

tool result in "tool" role        →       { type: "tool_result",
                                            tool_use_id, content }

                                          Gemini wire
                                          ──────────────────────────────────
functionDeclarations[] in tools   ←→      FunctionCall / FunctionResponse parts
```

**Vision**
- OpenAI / xAI / Groq / Azure: `{ type: "image_url", image_url: { url } }` — both `data:` URIs and HTTPS URLs accepted
- Anthropic: `{ type: "image", source: { type: "base64" | "url", ... } }`
- Ollama: `images: [base64string]` alongside `content`
- Gemini: `{ inlineData: { mimeType, data } }` or `{ fileData: { mimeType, fileUri } }`

**Extended thinking**
- Anthropic: `thinking: { type: "enabled", budget_tokens: N }` request param; response includes `thinking` content blocks streamed before the reply
- Ollama (`gpt-oss:20b`): `options.think: true`; thinking appears as `<think>...</think>` delimiters in streaming chunks

### Streaming Protocol Details

| Provider | Stream format | Terminator |
|---|---|---|
| Ollama | NDJSON (`\n`-delimited JSON objects) | `{ "done": true }` |
| OpenAI / xAI / Groq / Azure / Compat | SSE `data: <json>\n\n` | `data: [DONE]` |
| Anthropic | SSE `event: <type>\ndata: <json>\n\n` | `event: message_stop` |
| Gemini | SSE `data: <json>\n\n` | stream end |

`chatStream()` on each client returns an `AsyncGenerator<StreamChunk>` with a uniform `{ delta, thinking, done }` shape regardless of provider. `ChatPanel` consumes this generator and posts incremental webview messages.

### ChatPanelManager Coordination Bus

```
ChatPanelManager
├── panels: Map<string, ChatPanel>        // providerId → panel
├── openForProvider(providerId)
│     └── new ChatPanel(provider, memoryEngine, toolRegistry)
│           └── creates VS Code WebviewPanel, registers onDidReceiveMessage
├── broadcast(message: string)
│     └── for each panel: panel.receiveCoordination(message)
└── closeAll()
      └── for each panel: panel.dispose()

ChatPanel
├── send(message)            → streams via AiProviderClient, pushes chunks to webview
├── receiveCoordination(msg) → injects msg as a coordination notice, then calls send()
├── onToolCall(call)         → ToolExecutor.execute(call) → result injected into stream
└── onMemoryQuery(q)         → MemoryEngine.hybridSearch(q) → results injected as context
```

**Broadcast data flow**

```
User: spectral.chat.broadcast("what is the current task?")
  │
  ▼
ChatPanelManager.broadcast("what is the current task?")
  ├── ChatPanel(Ollama).receiveCoordination(...)
  │      └── AiProviderClient(Ollama).chatStream(...)   → streams to Ollama panel
  ├── ChatPanel(OpenAI).receiveCoordination(...)
  │      └── AiProviderClient(OpenAI).chatStream(...)   → streams to OpenAI panel
  └── ChatPanel(Anthropic).receiveCoordination(...)
         └── AiProviderClient(Anthropic).chatStream(...)→ streams to Anthropic panel
```

All responses stream concurrently. Each panel's webview updates independently.

### API Key Storage

Keys are stored via `vscode.SecretStorage` under `spectral.provider.<name>.apiKey`. They are never written to `settings.json`, workspace state, or any file on disk. `ProviderRegistry` retrieves keys from SecretStorage at call time and passes them in the `Authorization: Bearer` header (or `api-key` header for Azure OpenAI).

---

## Production Hardening

### Retry & Circuit Breaker (`aiProviderClient.ts`)

Every `AiProviderClient.chat()` call is wrapped in `_withRetry()`:

1. **Exponential backoff** – base 1s, max 30s, 0.2 jitter factor
2. **Retryable statuses** – 408, 429, 500, 502, 503, 504
3. **Non-retryable 4xx** – client errors (except above) fail immediately without counting as provider failures

**Circuit breaker** (3-state machine):
- **Closed** – requests pass through. After 5 consecutive failures → **Open**
- **Open** – fast-fail all requests. After 60s → **HalfOpen**  
- **HalfOpen** – single probe request. 2 successes → **Closed**; 1 failure → **Open**

### Rate Limiter (`rateLimiter.ts`)

Per-provider sliding-window rate limiting with concurrency control:

| Provider | Max Requests/min | Max Concurrent |
|----------|-----------------|----------------|
| Ollama   | 100             | 5              |
| OpenAI   | 60              | 10             |
| Anthropic| 50              | 5              |
| Groq     | 30              | 3              |

`RateLimiter.acquire()` returns a release function. Callers that exceed limits queue with configurable timeout (default 120s).

### Diagnostics Telemetry (`diagnosticsTelemetry.ts`)

Local-only performance telemetry (no external data sent):

- **Timer-based** (`startTimer`) and **direct** (`record`) operation tracking
- **Percentile stats** – p50, p95, p99, avg, min, max per operation
- **Throughput** – ops/sec over 60s sliding window
- **Subsystem health** – healthy / degraded (>10% errors) / unhealthy (>50% errors)
- **1000 samples** per operation rolling buffer

### Connection Health Monitor (`connectionHealth.ts`)

Runs every 30s, checking `isAlive()` on all registered providers:

- Tracks consecutive failures, latency, uptime ratio
- Exposes `getHealthyProviders()` sorted by latency (best-first) for failover decisions
- Integrates with circuit breaker state reporting

### SWIM Protocol Enhancements (`swimProtocol.ts`)

- `reviveNode(id)` – manually resurrect dead nodes with incarnation bump
- `getConvergenceEstimate()` – O(log N × 3) rounds × heartbeat interval
- `compactGossipQueue()` – remove stale entries for departed nodes
- `getMemberSummary()` – alive/suspect/dead/left/total for dashboard display

### Neural Engine Validation (`inference.ts`)

- `validateNetwork(name)` – probes with zeros, ones, random inputs. Detects NaN, Inf, and reports output range
- `evaluateClassifier(name, inputs, labels)` – computes accuracy with per-class breakdown
- `regressBatch(name, inputs)` – batch regression with per-sample timing
- `ModelManager.exportModelInfo(name)` – export single model metadata/topology as JSON
- `ModelManager.exportManifest()` – export full engine manifest with all model specs, parameter counts, and engine version

### VS Code Core (Phase 1)

The `vscode/` tree contains VS Code source modifications:

#### Session State Service (`vscode/src/vs/platform/sessionState/`)

- `ISessionStateService` interface with auto-snapshot, crash recovery
- Auto-snapshots every 30s (configurable), up to 100 retained in StorageService
- Crash detection via clean shutdown flag (`markCleanShutdown()` on lifecycle `beforeShutdown`)
- `getRecoveryInfo()` returns `SessionRecoveryInfo` with latest snapshot

#### Memory Optimization (`vscode/src/vs/base/common/`)

- `ObjectPool<T>` – generic typed-object pool with factory/reset/maxSize, tracks reuse ratio
- `ArrayPool` – power-of-two bucketed Float32Array pool (32-byte aligned for SIMD)
- `StringPool` – string interning with LRU eviction (8192 entries max)
- `GCOptimizer` – periodic V8 GC triggering (requires `--expose_gc`), heap before/after stats
- `MemoryMonitor` – 4-level pressure detection (Low/Medium/High/Critical at 512/1024/1536 MB), auto-GC on high pressure, growth rate tracking (MB/min)

#### AI Instructions Service (`vscode/src/vs/platform/aiInstructions/`)

- `IAiInstructionsService` – intercepts all AI instruction injections for transparency
- Override pipeline: enable/disable/replace individual instructions by pattern
- Instruction types: Mode, System, Extension, User
- Injection history (last 500 records) for audit
- Type-level config (enable/disable mode instructions, system messages, extension instructions)

### Extended SpectralApi

The cross-extension API now includes:

```typescript
interface SpectralApi {
  addMessage(role, content, metadata?): SessionMessage | null;
  getContextWindow(): { messages, totalTokens, truncated, droppedCount };
  searchMemory(query, limit?): Promise<MemoryEntry[]>;
  storeMemory(content, importance?, tags?): MemoryEntry;
  getSessionStats(): { sessionId, sessionName, durationMs, messageCount, totalTokens };
  checkpoint(): void;
  searchMessages(query, limit?): SessionMessage[];
  dbStats(): { pageSizeMB, pageCount, freelistCount, walFrames };
  exportSession(sessionId): object | null;          // NEW
  importSession(data): object | null;               // NEW
  getFormattedContext(includeSystemSummary?): SessionMessage[];  // NEW
  listSessions(limit?): Session[];                   // NEW
  getMemoryStats(): { totalMemories, indexLoaded };  // NEW
}
```
