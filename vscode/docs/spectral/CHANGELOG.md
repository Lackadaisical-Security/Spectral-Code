# Changelog – Spectral Code

All notable changes to Spectral Code (custom VS Code fork) are documented here.

---

## [4.0.0] – 2026-02-27

### Changed – Core architecture restructuring
- **This is NOT an extension anymore** — Spectral Code is a custom VS Code fork with enhancements integrated directly into the editor core
- Moved all Spectral modules into `vscode/src/vs/spectral/` as core VS Code modules
  - `spectral-extension/` → `vscode/src/vs/spectral/` (session, memory, providers, chat, tools, vision, attachments, history)
  - `extensions/stonedrift-mesh/` → `vscode/src/vs/spectral/mesh/` (SWIM protocol, nodes, MCP server, dashboard, image generation)
  - `extensions/lacky-neural-engine/` → `vscode/src/vs/spectral/neural/` (C++/NASM native engine + TypeScript bindings)
  - `stonedrift-reality-forge/` → `vscode/src/vs/spectral/reality-forge/`
- Moved installer to `vscode/build/spectral/installer/`
- Moved patches to `vscode/build/spectral/patches/`
- Moved docs to `vscode/docs/spectral/`
- Moved branding assets to `vscode/resources/spectral/`
- Moved build scripts to `vscode/build/spectral/`

### Changed – Product branding
- `product.json` updated: nameShort/nameLong → "Spectral Code"
- Application name → `spectral-code`
- Data folder → `.spectral-code`
- Win32 registry → `SpectralCode`
- Darwin bundle → `com.lackadaisical.spectral-code`
- URL protocol → `spectral-code`

### Fixed – Internal imports
- Fixed all TypeScript import paths for new directory structure
- All 3 modules compile with 0 errors
- All 44 unit tests pass

---

## [3.8.1] – 2026-02-27

### Added – Installer resources & build validation
- Created `installer/res/` directory with all WiX-required resource files:
  - `spectral.ico` – multi-resolution icon (16/32/48/256px) with purple spectral gradient
  - `banner.bmp` – 493×58 WiX MSI dialog banner
  - `dialog.bmp` – 493×312 WiX welcome/license background
  - `logo.png` – 64×64 EXE bootstrapper logo
  - `license.rtf` – MIT license with Spectral Edition terms, third-party acknowledgments
  - `spectral-theme.xml` – WiX Burn bootstrapper theme (Install/Progress/Success/Failure pages)
  - `spectral-en-us.wxl` – localization strings for installer UI
- `validate-build.sh` – comprehensive build validation script (directories, files, patches, TypeScript, tests, XML)
- WiX Components.wxs: added patch 005, implementation plan doc, util namespace declaration

### Added – Multi-provider coordination
- `spectral.provider.validate` command – tests all configured providers with real connectivity checks and latency reporting
- `spectral.provider.coordinate` command – opens multiple chat windows simultaneously for coordinated multi-provider interaction
- All 9 providers can coordinate from separate chat windows sharing memory + tool context

### Changed – Build system
- `setup.ps1`, `setup.sh`, `build-installer.ps1` – now include lacky-neural-engine TypeScript compilation
- WiX registry version updated to 3.8.0
- Components.wxs util namespace declared for standalone XML validation

### Updated – Documentation
- `docs/ARCHITECTURE.md` – expanded SpectralApi section (13 methods), multi-provider coordination, installer resources, build validation
- `CHANGELOG.md` – added v3.8.1 entries

---

## [3.8.0] – 2026-02-26

### Added – Configurable session windows & timeouts
- `spectral.session.sessionTimeoutMinutes` – session auto-continuation on timeout (0 = unlimited, default)
- `spectral.session.contextLengthTokens` – configurable context length override (0 = unlimited, default)
- `spectral.session.idleTimeoutMinutes` – idle detection with auto WAL checkpoint (0 = disabled, default)
- `Session.durationMs` / `Session.lastActivityAt` – computed session duration and activity tracking
- `SessionManager.isSessionExpired()` / `SessionManager.isIdle()` – expiry and idle detection
- Auto-continue: expired sessions seamlessly roll into a new linked session with `previousSessionId` metadata for chain traversal

### Added – Cross-extension integration API
- `SpectralApi` interface exported from spectral-extension for external consumption
- Methods: `addMessage`, `getContextWindow`, `searchMemory`, `storeMemory`, `getSessionStats`, `checkpoint`, `searchMessages`, `dbStats`
- Other extensions can call `vscode.extensions.getExtension('lackadaisical-security.vscode-spectral-edition')?.exports`
- `NeuralEngineApi` interface exported from lacky-neural-engine: `classify`, `regress`, `train`, `getStatus`, `listModels`
- stonedrift-mesh now declares `extensionDependencies` on spectral-extension
- `stonedrift.spectral.sync` command – syncs high-importance STONEDRIFT collective memories to Spectral memory

### Added – Memory engine enhancements
- `hybridSearch()` – fixed duplicate ftsResults bug (was missing vecResults in result collection)
- `scoreMessageImportance()` – message-level importance scoring for retention decisions (system > tool > user > assistant)
- `decayPrune()` – auto-prune stale memories using decay scoring (exponential half-life 7d)
- `startAutoPrune()` / `stopAutoPrune()` – configurable periodic decay prune cycle
- Workspace state tracking in `retrieveContext()` (workspace, activeEditors, recentCommands)

### Added – BPE-aware context window
- `estimateTokens()` – BPE-aware token estimation: code blocks ~3 chars/token, specials ~2, prose ~4
- Priority-based message retention with role weighting (system=100, tool=80, user=60, assistant=40)
- Adaptive truncation using per-message chars-per-token ratio
- Priority-sorted message summarisation (high-priority roles appear first in summaries)

### Added – Neural engine training enhancements
- `TrainingConfig` – early stopping (patience + delta), LR scheduling (constant/step/cosine), dataset shuffling, validation split
- `TrainingResult` – loss history, validation loss history, early stopping flag, best validation loss
- Model weight serialization (`saveWeights()`)
- `listNetworks()` – enumerate registered network names

### Added – SWIM protocol metrics
- `getMetrics()` – protocol performance metrics: ping/ack counts, suspect/dead/revival events, gossip stats
- Ping latency tracking with p99 percentile (last 200 samples)
- Protocol health score [0-100] (alive ratio, suspect rate, ack success, dead penalty)
- Protocol period counter and uptime tracking

### Fixed
- `dashboardApi.ts` – `Thenable.catch` → `Promise.resolve().catch()` for VS Code SecretStorage calls
- `better-sqlite3` upgraded from ^9.4.3 to ^12.6.2 for Node.js 24 compatibility
- `memoryEngine.ts` – `hybridSearch()` was iterating `ftsResults` twice instead of including `vecResults`
- `sessionManager.ts` – removed unnecessary null coalescing on `_lastCheckpointTs`
- `package-lock.json` versions synced with `package.json` for both spectral-extension and stonedrift-mesh

### Changed
- Bumped spectral-extension to v3.8.0
- Bumped stonedrift-mesh to v3.8.0
- Updated implementation plan: Phases 3–5 marked as COMPLETE with file references

### Added – Production hardening (v3.8.0 continued)
- **Retry logic with circuit breaker** – `AiProviderClient` now retries failed requests with exponential backoff + jitter (configurable: maxRetries, baseDelay, maxDelay, retryable HTTP statuses)
- **Circuit breaker** (3-state: Closed/Open/HalfOpen) – protects against cascading provider failures; auto-recovers after reset timeout
- **Rate limiter** – per-provider sliding-window rate limiting with concurrency control (`RateLimiter`, `RateLimiterRegistry`)
- **Diagnostics telemetry** – local performance monitoring with p50/p95/p99 latencies, subsystem health scoring (healthy/degraded/unhealthy), throughput tracking
- **Connection health monitor** – periodic provider availability checks with latency tracking, uptime ratio, and automatic failover data
- **`spectral.diagnostics` command** – opens a full diagnostics report showing provider health, subsystem stats, and operation metrics

### Added – SWIM protocol enhancements
- `reviveNode(id)` – manually resurrect dead nodes with incarnation bump
- `getConvergenceEstimate()` – epidemic dissemination convergence timing (O(log N) rounds)
- `compactGossipQueue()` – remove stale gossip entries for departed nodes
- `getMemberSummary()` – alive/suspect/dead/left/total counts for dashboard display

### Added – Neural engine model validation & batch inference
- `validateNetwork()` – detect NaN/Inf outputs, range analysis, multi-sample validation probes
- `regressBatch()` – batch regression inference with per-sample timing
- `evaluateClassifier()` – accuracy computation with per-class metrics on labelled test sets
- `validateModel()` added to `NeuralEngineApi` cross-extension interface
- `ModelManager.validate()` / `ModelManager.regressBatch()` – manager-level shortcuts

### Added – Test suite
- 34 unit tests covering `estimateTokens`, `buildContextWindow`, `MemoryEngine.scoreImportance`, `scoreMessageImportance`, `CircuitBreaker`, `DiagnosticsTelemetry`, `RateLimiter`, `EmbeddingEngine`

### Added – VS Code Core (Phase 1) implementation in vscode/ tree
- **Session State Service** (`vscode/src/vs/platform/sessionState/`) – `ISessionStateService` with auto-snapshots every 30s, crash recovery via clean shutdown flag, up to 100 snapshots in StorageService
- **Memory Pool** (`vscode/src/vs/base/common/memoryPool.ts`) – `ObjectPool<T>`, `ArrayPool` (power-of-two bucketed Float32Arrays), `StringPool` (intern deduplication) with reuse tracking
- **GC Optimizer** (`vscode/src/vs/base/common/gcOptimizer.ts`) – periodic V8 GC triggering, heap before/after stats, freed-bytes tracking, runtime `--expose_gc` detection
- **Memory Monitor** (`vscode/src/vs/platform/diagnostics/common/memoryMonitor.ts`) – 4-level pressure detection (Low/Medium/High/Critical at 512/1024/1536 MB), growth rate tracking, auto-GC on high pressure
- **AI Instructions Service** (`vscode/src/vs/platform/aiInstructions/`) – `IAiInstructionsService` with instruction interception, override pipeline (enable/disable/replace), injection history, type-level config (Mode/System/Extension/User)
- All modules registered in `vscode/src/vs/workbench/workbench.common.main.ts` via `registerSingleton`

### Added – STONEDRIFT commands
- `stonedrift.swim.metrics` – opens SWIM protocol metrics report (member counts, convergence estimate)
- `stonedrift.node.health` – opens full node health report with per-node status, SWIM state, running status

### Added – Neural engine model export
- `ModelManager.exportModelInfo(name)` – export single model metadata/topology
- `ModelManager.exportManifest()` – export full engine manifest with all model specs

### Added – Session management commands (spectral-extension)
- `spectral.session.import` – import a session from JSON file
- `spectral.session.delete` – delete a session and all its messages
- `spectral.session.chain` – visualize session chain (linked continuations via previousSessionId)
- `spectral.memory.prune` – decay-prune stale memories below configurable threshold
- `spectral.memory.remember` – manually store a memory with importance and tags
- Extended `SpectralApi` with `exportSession`, `importSession`, `getFormattedContext`, `listSessions`, `getMemoryStats`

### Added – STONEDRIFT mesh commands
- `stonedrift.swim.compact` – compact gossip queues across all node protocols
- `stonedrift.node.revive` – resurrect dead nodes via quick pick
- `stonedrift.mesh.convergence` – show epidemic convergence estimate

### Added – Neural engine commands
- `lacky.neural.validate` – validate model outputs (NaN/Inf detection, range analysis)
- `lacky.neural.exportManifest` – export full engine manifest as JSON

### Added – Test suite expansion
- 44 unit tests (up from 34): added context window edge cases, memory scoring edge cases, token estimation edge cases

---

## [3.7.1] – 2026-02-26

### Added – spectral-extension complete wiring
- `extension.ts` – full VS Code entry point: all 16 commands, AttachmentManager, ToolRegistry (registerBuiltinTools), VisionEngine fully wired
- Commands added: `spectral.attachment.add/list/delete`, `spectral.tool.list/run`, `spectral.vision.analyze/extractCode`
- `package.json` – bumped to v3.7.0; 8 new settings: `spectral.ollama.url`, `spectral.vision.provider/ollamaModel/apiKey/timeoutMs`, `spectral.attachments.maxAttachmentMB`

### Added – lacky-neural-engine
- `src/inference.ts` – `InferenceService`: hardware detection, single/batch classify, regress, train, Float32 utilities
- `src/model_manager.ts` – `ModelManager`: 6 built-in code intelligence specs, load/unload lifecycle, auto warm-up, status reporting, parameter count
- `src/index.ts` – VS Code `activate()`/`deactivate()` with status bar, 4 commands (`lacky.neural.status/classify/loadModel/unloadModel`), auto-warm-up of all built-in networks
- `package.json` – bumped to v2.0.0, added 4 VS Code commands with `AI` category

### Added – stonedrift-mesh dashboard TOOLS tab
- `media/dashboard.html` – `loadTools()`, `loadToolParams()`, `execTool()` JS functions: full tool registry browser, param docs, JSON args template, live execution result
- Tab click handler wired to call `loadTools()` when TOOLS tab is activated

### Added – stonedrift-mesh tool system
- `src/views/dashboardApi.ts` – `tool.list` and `tool.run` API commands
- 11 built-in MESH_TOOLS: `mesh_status`, `node_chat`, `memory_search`, `memory_store`, `ollama_list_models`, `ollama_list_running`, `ollama_pull_model`, `translate`, `restart_node`, `checkpoint_databases`, `generate_image`, `mesh_health`
- `translate` API command using `translategemma` model
- `_translate()` private method for language translation

### Added – VSCode core patches
- `patches/005-phase1-unrestricted-context.patch` – removes token budget enforcement, context window truncation, session rate limits, and per-session request caps from VS Code core chat/inline chat services

### Fixed – Pre-existing TypeScript errors
- `networkGuard.ts` – unescaped apostrophe in iptables comment string
- `extension.ts` – `MemoryEntry.nodeId` → `MemoryEntry.originNode`
- `meshManager.ts` – `TwinConsciousness.start()`/`stop()` → `stopAll()` (correct API)
- `swimProtocol.ts` – `undefined` map delete guarded with `next.done` check
- `dashboardApi.ts` – `req.history` cast to `OllamaMessage[]`; `role: 'user' as const`
- `spectral-extension` – removed 6 unused variable warnings across 6 files for clean `noUnusedLocals:true` compile

### Updated – Documentation
- `README.md` – MCP server section, LQX-20 section, real-time dashboard feature list, patch 005 table, `docs/` reference table
- `docs/ARCHITECTURE.md` – inference.ts/model_manager.ts layer, tool system, translation, full tool list

---

## [3.7.0] – 2026-02-26

### Added – Installer (WiX 3.14 / MinGW64)
- `installer/wxs/Product.wxs` – WiX 3.14 MSI: embedded CAB, MajorUpgrade, feature tree, registry, shortcuts
- `installer/wxs/Bundle.wxs` – WiX Burn EXE bootstrapper: chains Node.js 20 + VC++ Redist + Spectral MSI
- `installer/wxs/Components.wxs` – all file components, env var kills, shortcut components
- `installer/wxs/UI.wxs` – WixUI_InstallDir base with custom branding
- `installer/ca/spectral_ca.cpp` – Win32 C++ custom action DLL (MinGW64): SetEnvironmentVariables, RegisterSpectralModels (WinHTTP→Ollama /api/create), CleanupInstall
- `installer/ca/CMakeLists.txt` – MinGW64 cmake build for CA DLL, static libgcc/libstdc++
- `installer/build-installer.ps1` – full PS build pipeline: native→CA DLL→TS→candle→light (MSI)→Burn (EXE)→signtool

### Added – Build system
- `extensions/lacky-neural-engine/CMakeLists.txt` – MinGW64 detection, NASM win64, Win32 API linking, AVX2+FMA+F16C+BMI2, static runtime, DirectML/OpenCL
- `extensions/lacky-neural-engine/toolchain-mingw64.cmake` – MinGW64 toolchain: MSYS2/standalone discovery, NASM, static linking flags
- `setup.ps1` – complete rewrite: MinGW64/NASM/cmake-js/WiX discovery, builds everything, pulls Ollama models
- `setup.sh` – extended: colour output, --models-only/--build-only flags, full build pipeline

### Added – Image generation
- `imageGenerationEngine.ts` – ComfyUI (full workflow, ControlNet, img2img, NDJSON progress) + A1111 (txt2img/img2img/upscale/interrogate/ControlNet) + Ollama fallback; auto-detect; localhost-only

### Added – Network isolation
- `networkGuard.ts` – env var kills, HTTP/HTTPS monkey-patch (blocks external with ECONNREFUSED), iptables/nftables/hosts generators
- `ollamaClient.ts` – assertLocalUrl() guard on every HTTP call

### Added – Spectral model variants
- `modelfileCreator.ts` – registers spectral-* via Ollama /api/create with SYSTEM "" (removes restriction preambles)
- All OllamaClient methods use resolveModel() (falls back to base if spectral not registered)

### Added – Dashboard
- `media/dashboard.html` – 79KB webview, 80s retro cosmic cyberspace theme, 8 tabs: MESH/FORGE/CHAT/VISION/MEMORY/TOOLS/MONITOR/SETTINGS
- VISION tab: ComfyUI/A1111/Ollama selector, generation params, ControlNet, img2img, gallery, preview, download, interrogate
- `dashboardProvider.ts` – WebviewPanel, 3s status push
- `dashboardApi.ts` – full message router for all backend modules

### Changed – Ollama models
- gpt-oss:20b: 256k ctx, native thinking (all 14 nodes)
- nemotron-3-nano: 1M ctx, tool calling
- translategemma: 128k ctx
- dolphin3: 128k ctx, uncensored US (Eric Hartford), assigned to Spectre/Alex/Flux/Spectre-Origin

### Added – Documentation
- docs/MODELS.md, docs/ARCHITECTURE.md, docs/SWIM-PROTOCOL.md, docs/BUILD-WINDOWS.md
- README.md full rewrite

---

## [3.6.0] – 2026-02-20

### Added – STONEDRIFT Mesh
- SWIM gossip protocol: ping/ping-req/ack, suspicion, incarnation, infection dissemination
- MeshManager: IPC bus, round-robin probing, lifecycle
- Dual database: collective.db + per-node WAL/SHM, AES-256-GCM, FTS5
- Twin consciousness: 7 pairs, auto-sync, emergency override
- NodeBase: chat, toolLoop, translate, vision, searchMemory, auto-remember, CoT extraction
- 14 node personalities with Tolkien elvish names, system prompts, specialties
- Full Ollama client: streaming, tools, thinking, vision, translation, embeddings, pull

---

## [3.5.0] – 2026-02-15

### Added – spectral-extension
- 25GB SQLite WAL/SHM memory engine, AES-256-GCM
- FTS5 + cosine similarity vector search
- nomic-embed-text semantic embeddings
- Unlimited context window manager
- Session manager with auto-restore
- Edit history with branching
- Tool registry + 20+ built-in tools
- Vision engine (llava)
- Attachment manager (encrypted, code blocks, downloads)

---

## [3.0.0] – 2026-02-10

### Added – lacky-neural-engine
- C++17 + NASM AVX2 SIMD neural operations
- N-API bindings Node.js 18/20/22
- Hardware detection (CPU caps, GPU)
- GPU backends: DirectML, CUDA, OpenCL, Metal
- INT8/INT4/NF4/FP16 quantization

---

## [2.0.0] – 2026-02-05

### Added – VSCode patches
- 001: extended session state persistence
- 002: GC tuning + memory pool
- 003: AI system instruction plumbing
- 004: Spectral Edition workbench registration

---

## [1.0.0] – 2026-02-01

### Added
- Repository scaffolding, setup.sh / setup.ps1, VSCode 1.109.5 source automation
