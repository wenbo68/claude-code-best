# Dependency Overview

This document covers the root `package.json` dependencies (runtime, dev, and optional) for `claude-code-best`. Internal workspace packages (`workspace:*`) are listed separately.

---

## Taxonomy

### 1. Anthropic / AI API SDKs

| Package | Version | Role |
|---------|---------|------|
| `@anthropic-ai/sdk` | ^0.81.0 | Core Anthropic API client — used in `src/services/api/claude.ts` for streaming chat completions |
| `@anthropic-ai/bedrock-sdk` | ^0.29.0 | AWS Bedrock provider adapter |
| `@anthropic-ai/vertex-sdk` | ^0.16.0 | Google Vertex AI provider adapter |
| `@anthropic-ai/foundry-sdk` | ^0.2.3 | Anthropic Foundry provider adapter |
| `@anthropic-ai/claude-agent-sdk` | ^0.2.114 | Claude agent SDK (ACP integration) |
| `@anthropic-ai/mcpb` | ^2.1.2 | Anthropic-internal MCP bindings |
| `@anthropic-ai/sandbox-runtime` | ^0.0.44 | Sandbox runtime support |
| `@agentclientprotocol/sdk` | ^0.19.0 | ACP (Agent Client Protocol) SDK — WebSocket agent bridge |
| `openai` | ^6.34.0 | OpenAI-compatible API client (used in `src/services/api/openai/` for Ollama/DeepSeek/vLLM compat layer) |
| `@modelcontextprotocol/sdk` | ^1.29.0 | Official MCP SDK — used by `packages/mcp-client/` for tool discovery and invocation |

---

### 2. AWS / Cloud Provider SDKs

| Package | Version | Role |
|---------|---------|------|
| `@aws-sdk/client-bedrock` | ^3.1037.0 | AWS Bedrock service client |
| `@aws-sdk/client-bedrock-runtime` | ^3.1037.0 | Bedrock streaming inference |
| `@aws-sdk/client-sts` | ^3.1037.0 | AWS STS (token exchange for Bedrock auth) |
| `@aws-sdk/credential-provider-node` | ^3.972.36 | AWS credential resolution in Node/Bun |
| `@aws-sdk/credential-providers` | ^3.1037.0 | Additional AWS credential providers |
| `@smithy/core` | ^3.23.15 | AWS Smithy runtime (transitive, pinned for compatibility) |
| `@smithy/node-http-handler` | ^4.5.3 | Smithy HTTP handler for Node/Bun |
| `@azure/identity` | ^4.13.1 | Azure identity credentials (used by Vertex/Azure provider paths) |
| `google-auth-library` | ^10.6.2 | Google OAuth2 / service account auth (Vertex AI) |

---

### 3. Terminal UI (Ink / React)

| Package | Version | Role |
|---------|---------|------|
| `react` | ^19.2.5 | React 19 — used as the rendering foundation for the Ink TUI |
| `react-reconciler` | ^0.33.0 | React reconciler API — used by the forked Ink framework (`packages/@ant/ink`) |
| `react-compiler-runtime` | ^1.0.0 | React Compiler memoization runtime (`_c()` calls in decompiled components) |
| `@ant/ink` (workspace) | * | Forked Ink TUI framework with custom components, hooks, keybindings, theme |
| `chalk` | ^5.6.2 | Terminal color utility — used throughout for colored output |
| `strip-ansi` | ^7.2.0 | Remove ANSI escape codes from strings |
| `wrap-ansi` | ^10.0.0 | Word-wrap strings with ANSI codes |
| `@alcalzone/ansi-tokenize` | ^0.3.0 | Tokenize ANSI-escape strings for layout calculations |
| `figures` | ^6.1.0 | Terminal-safe Unicode symbols |
| `cli-boxes` | ^4.0.1 | Box-drawing characters for terminal UI frames |
| `get-east-asian-width` | ^1.5.0 | CJK character width calculation for correct text layout |
| `emoji-regex` | ^10.6.0 | Detect emoji in strings (width calculations) |
| `bidi-js` | ^1.0.3 | Bidirectional text (RTL) support |
| `asciichart` | ^1.5.25 | ASCII line charts for stats display |
| `supports-hyperlinks` | ^4.4.0 | Detect terminal hyperlink support |
| `qrcode` | ^1.5.4 | Generate QR codes in terminal (auth flows) |

---

### 4. CLI Framework

| Package | Version | Role |
|---------|---------|------|
| `@commander-js/extra-typings` | ^14.0.0 | Commander.js with full TypeScript type inference — drives `src/main.tsx` CLI definition |

---

### 5. Build & Bundling

| Package | Version | Role |
|---------|---------|------|
| `vite` | ^8.0.8 | Alternative build pipeline (`build:vite`); code-splitting for Bun/Node memory optimization |
| `rollup` | ^4.60.2 | Rollup bundler (used by Vite internally) |
| `typescript` | ^6.0.3 | TypeScript compiler — strict mode enforced; `tsc --noEmit` in `precheck` |
| `@types/bun` | ^1.3.12 | Bun runtime type declarations |
| `@types/node` | ^25.6.0 | Node.js type declarations |
| `@types/react` | ^19.2.14 | React 19 type declarations |
| `@types/react-reconciler` | ^0.33.0 | React reconciler type declarations |

---

### 6. Linting & Formatting

| Package | Version | Role |
|---------|---------|------|
| `@biomejs/biome` | ^2.4.12 | All-in-one linter + formatter (replaces ESLint + Prettier); configured via `biome.json` |
| `husky` | ^9.1.7 | Git hooks manager — runs lint-staged on pre-commit |
| `lint-staged` | ^16.4.0 | Run Biome only on staged files during commit |
| `knip` | ^6.4.1 | Detect unused exports/dependencies (`bun run check:unused`) |

---

### 7. HTTP & Networking

| Package | Version | Role |
|---------|---------|------|
| `ws` | ^8.20.0 | WebSocket client/server — used in bridge/RCS/ACP communication |
| `axios` | ^1.15.2 | HTTP client — used for skill store, vault, and schedule API calls |
| `undici` | ^7.25.0 | Fast Node.js HTTP client (used where fetch is not available) |
| `https-proxy-agent` | ^8.0.0 | HTTPS proxy support for API calls behind corporate proxies |
| `@claude-code-best/mcp-chrome-bridge` | ^3.0.1 | Chrome DevTools Protocol bridge (runtime dep) |

---

### 8. File System & Process

| Package | Version | Role |
|---------|---------|------|
| `chokidar` | ^5.0.0 | File system watcher — used for watch mode and file change detection |
| `ignore` | ^7.0.5 | Parse `.gitignore` patterns for file filtering |
| `picomatch` | ^4.0.4 | Glob pattern matching |
| `cacache` | ^20.0.4 | Content-addressable disk cache |
| `proper-lockfile` | ^4.1.2 | File-level mutex locking |
| `env-paths` | ^4.0.0 | OS-appropriate app config/cache/data directories |
| `execa` | ^9.6.1 | Subprocess execution — used for shell commands, git, ripgrep |
| `tree-kill` | ^1.2.2 | Kill process trees (for cleaning up subprocesses) |
| `signal-exit` | ^4.1.0 | Reliable process exit hooks |
| `shell-quote` | ^1.8.3 | Parse and quote shell command strings |
| `fflate` | ^0.8.2 | Fast WASM-free compression/decompression |

---

### 9. Data Parsing & Serialization

| Package | Version | Role |
|---------|---------|------|
| `yaml` | ^2.8.3 | YAML parse/stringify — used for config files and MCP server configs |
| `jsonc-parser` | ^3.3.1 | Parse JSON with comments (JSONC) — used for tsconfig and settings files |
| `ajv` | ^8.18.0 | JSON Schema validator — validates tool inputs |
| `zod` | ^4.3.6 | Runtime schema validation + TypeScript type inference throughout the codebase |
| `plist` | ^3.1.0 | macOS plist parsing (computer-use, macOS integration) |
| `semver` | ^7.7.4 | Semantic versioning comparison (update checker) |

---

### 10. Text & Markdown Processing

| Package | Version | Role |
|---------|---------|------|
| `marked` | ^17.0.6 | Markdown parser — renders assistant markdown in terminal |
| `turndown` | ^7.2.4 | HTML-to-Markdown converter (web fetch tool) |
| `he` | ^1.2.0 | HTML entity encoder/decoder |
| `diff` | ^8.0.4 | Text diff computation — used for file edit display |
| `highlight.js` | ^11.11.1 | Syntax highlighting (runtime dep; used in docs/web UI) |
| `cli-highlight` | ^2.1.11 | Syntax highlighting for terminal output |
| `code-excerpt` | ^4.0.0 | Extract code excerpts with context lines |
| `indent-string` | ^5.0.0 | Add indentation to multi-line strings |
| `xss` | ^1.0.15 | XSS sanitization (web UI / RCS) |

---

### 11. Utilities & Data Structures

| Package | Version | Role |
|---------|---------|------|
| `lodash-es` | ^4.18.1 | ESM lodash utility functions |
| `lru-cache` | ^11.3.5 | LRU in-memory cache |
| `fuse.js` | ^7.3.0 | Fuzzy search — powers FuzzyPicker component and skill search |
| `p-map` | ^7.0.4 | Concurrent promise map with concurrency control |
| `auto-bind` | ^5.0.1 | Auto-bind class methods to instance |
| `type-fest` | ^5.6.0 | Collection of essential TypeScript utility types |
| `usehooks-ts` | ^3.1.1 | React hooks library (used in web UI components) |

---

### 12. Observability (OpenTelemetry / Langfuse / Sentry)

| Package | Version | Role |
|---------|---------|------|
| `@opentelemetry/api` | ^1.9.1 | OTel API — tracing/metrics/logs interface |
| `@opentelemetry/sdk-trace-base` | ^2.7.0 | OTel trace SDK |
| `@opentelemetry/sdk-metrics` | ^2.7.0 | OTel metrics SDK |
| `@opentelemetry/sdk-logs` | ^0.215.0 | OTel logs SDK |
| `@opentelemetry/resources` | ^2.7.0 | OTel resource detection |
| `@opentelemetry/core` | ^2.7.0 | OTel core utilities |
| `@opentelemetry/semantic-conventions` | ^1.40.0 | OTel semantic conventions |
| `@opentelemetry/exporter-*` | ^0.215.0/^2.7.0 | OTLP exporters for traces/metrics/logs (gRPC, HTTP, Prometheus) |
| `@langfuse/otel` | ^5.1.0 | Langfuse OTel integration — sends traces to Langfuse dashboard |
| `@langfuse/tracing` | ^5.1.0 | Langfuse tracing helpers |
| `@sentry/node` | ^10.49.0 | Sentry error tracking (stub implementation — empty in this fork) |

---

### 13. Feature Flags & A/B Testing

| Package | Version | Role |
|---------|---------|------|
| `@growthbook/growthbook` | ^1.6.5 | GrowthBook feature flag / A/B testing SDK (stub — empty in this fork) |

---

### 14. Language Server Protocol (LSP)

| Package | Version | Role |
|---------|---------|------|
| `vscode-jsonrpc` | ^8.2.1 | JSON-RPC transport layer for LSP |
| `vscode-languageserver-protocol` | ^3.17.5 | Full LSP protocol types |
| `vscode-languageserver-types` | ^3.17.5 | LSP data types (Position, Range, Diagnostic, etc.) |

---

### 15. Image & Media Processing

| Package | Version | Role |
|---------|---------|------|
| `sharp` | ^0.34.5 | High-performance image processing (resizing screenshots for computer-use) |
| `image-processor-napi` (workspace) | * | Native NAPI image processing |
| `audio-capture-napi` (workspace) | * | Native audio capture for voice mode |
| `color-diff-napi` (workspace) | * | Native color difference computation |
| `modifiers-napi` (workspace) | * | macOS modifier key detection (FFI) |
| `url-handler-napi` (workspace) | * | URL scheme handler |

---

### 16. Internal Workspace Packages

| Package | Role |
|---------|------|
| `@ant/ink` | Forked Ink TUI framework |
| `@ant/computer-use-mcp` | Computer-use MCP server |
| `@ant/computer-use-input` | Keyboard/mouse simulation |
| `@ant/computer-use-swift` | Screenshot/app management |
| `@ant/claude-for-chrome-mcp` | Chrome browser control MCP |
| `@ant/model-provider` | Model provider abstraction |
| `@claude-code-best/builtin-tools` | 60 built-in tool implementations |
| `@claude-code-best/agent-tools` | Agent-specific tools |
| `@claude-code-best/mcp-client` | MCP client library |
| `@claude-code-best/weixin` | WeChat integration |
| `@anthropic/ink` | Alias for `@ant/ink` workspace |
| `packages/remote-control-server` | Self-hosted RCS with Web UI |
| `packages/acp-link` | ACP proxy server |

---

### 17. Optional Dependencies

| Package | Version | Role |
|---------|---------|------|
| `doubaoime-asr` | ^0.1.0 | ByteDance Doubao ASR (speech recognition) — optional voice input backend |

---

## Dependency Graph Highlights

- **Critical path**: `@anthropic-ai/sdk` → `src/services/api/claude.ts` → `src/query.ts` → `src/QueryEngine.ts` → `src/screens/REPL.tsx`
- **TUI rendering**: `react` + `react-reconciler` → `@ant/ink` → all `src/components/`
- **Tool execution**: `@claude-code-best/builtin-tools` → `src/tools.ts` → `src/query.ts`
- **MCP**: `@modelcontextprotocol/sdk` → `@claude-code-best/mcp-client` → `src/services/mcp/`
- **Build**: `vite` (primary) or `bun build.ts` (native); `scripts/defines.ts` centralizes version + feature flags
- **Observability**: OTel SDK + Langfuse are wired in but Sentry and GrowthBook are stubbed/empty
