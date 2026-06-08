# Filesystem Overview

- Date: 2026/06/07
- Summary: `claude-code-best` is a reverse-engineered / decompiled rebuild of Anthropic's official Claude Code CLI — an interactive AI coding assistant for the terminal. It runs on Bun (ESM, TSX with `react-jsx`) and is structured as a Bun-workspace monorepo: the `src/` tree holds the CLI entrypoint, conversation/query loop, Ink-based terminal UI, tool system, multi-provider API layer (Anthropic first-party plus Bedrock/Vertex/Foundry/OpenAI/Gemini/Grok), ACP/bridge/daemon modes, and a large set of slash commands; `packages/` holds workspace packages (forked Ink framework, built-in tools, computer-use MCP servers, native NAPI helpers, remote-control server, etc.). Functionality is gated by an extensive feature-flag system, with many secondary modules stubbed or feature-flagged off. TypeScript strict mode is enforced and `bun run precheck` must pass clean.

## Structure

```
claude-code-best/
├── .dockerignore          — Docker build exclusion list
├── .editorconfig          — Editor indentation/whitespace rules (aligned with Biome)
├── .gitignore             — Git-ignored paths (build output, deps, local state)
├── .impeccable.md         — "Impeccable" design context/brand guidance for Web UIs
├── .mintignore            — Mintlify docs-site ignore list
├── .npmrc                 — npm/bun registry & install config
├── .tool-versions         — asdf tool-version pins (Bun version)
├── AGENTS.md              — Agent-authoring guidance / instructions for AI agents
├── CLAUDE.md              — Primary guidance for Claude Code agents working in this repo
├── DEPENDENCY-OVERVIEW.md — Companion map: third-party dependency overview
├── DEV-LOG.md             — Running development log / change history
├── FILESYSTEM-OVERVIEW.md — This file: annotated directory map
├── Friends.md             — Community / acknowledgements notes
├── README.md              — Project README (Chinese primary)
├── README_EN.md           — English README
├── SECURITY.md            — Security policy / vulnerability reporting
├── biome.json             — Biome lint + format configuration
├── build.ts               — Bun.build() bundler script (code splitting, vendor copy)
├── bun.lock               — Bun dependency lockfile
├── bunfig.toml            — Bun runtime/test configuration
├── codecov.yml            — Codecov coverage upload config
├── contributors.svg       — Generated contributors graphic
├── docs.json              — Docs-site (Mintlify) navigation/config
├── knip.json              — knip unused-exports checker config
├── mint.json              — Mintlify docs-site config (legacy)
├── package.json           — Root manifest: scripts, deps, workspaces, overrides
├── progress.md            — Restoration progress tracker
├── tsconfig.base.json     — Shared TS compiler base config
├── tsconfig.json          — Root TS config (paths, strict mode)
├── vite.config.ts         — Vite alternative build pipeline config
├── .claude/               — Repo-local Claude Code config (agents, skills)   (dot-folder leaf, not expanded)
├── .git/                  — Git repository internals   (dot-folder leaf, not expanded)
├── .github/               — GitHub config: CI workflows + issue templates   (dot-folder leaf, not expanded)
├── .husky/                — Husky git hooks (pre-commit runs Biome)   (dot-folder leaf, not expanded)
├── .vscode/               — VS Code workspace settings (launch, tasks, extensions)   (dot-folder leaf, not expanded)
├── dist-nosplit/          — Prebuilt single-file (non-split) CLI bundle + vendor   (expanded)
│   ├── cli.js             — Built non-split CLI bundle
│   └── vendor/            — Copied vendor binaries (ripgrep, audio-capture)
├── docs/                  — Mintlify documentation site source   (expanded)
│   ├── auto-updater.md            — Auto-updater design doc
│   ├── external-dependencies.md   — Notes on external/native dependencies
│   ├── favicon.svg                — Docs site favicon
│   ├── lsp-integration.md         — LSP integration doc
│   ├── memory-leak-audit.md       — Memory leak audit notes
│   ├── memory-peak-analysis.md    — Memory peak / RSS analysis
│   ├── performance-reporter.md    — Performance reporter doc
│   ├── telemetry-remote-config-audit.md — Telemetry/remote-config audit
│   ├── agent/             — Agent-system documentation
│   ├── context/           — Context-building documentation
│   ├── conversation/      — Conversation/query-loop documentation
│   ├── design/            — Design-system / UI documentation
│   ├── diagrams/          — Architecture diagrams
│   ├── extensibility/     — Plugins/MCP/extensibility docs
│   ├── features/          — Per-feature docs (acp-link, bridge-mode, computer-use, coordinator-mode, voice, …)
│   ├── images/            — Doc images
│   ├── internals/         — Internal architecture docs
│   ├── introduction/      — Getting-started / intro docs
│   ├── logo/              — Logo assets
│   ├── safety/            — Safety/permissions docs
│   ├── task/              — Task-system docs
│   ├── test-plans/        — Manual/automated test plans
│   ├── testing/           — Testing-spec docs
│   └── tools/             — Tool-system docs
├── packages/              — Bun workspace packages (workspace:*)   (expanded)
│   ├── tsconfig.json      — Shared TS config for workspace packages
│   ├── @ant/              — Internal "@ant" scoped packages (forked Ink + computer-use + model-provider)
│   │   ├── claude-for-chrome-mcp/ — Chrome browser-control MCP server package
│   │   ├── computer-use-input/    — Keyboard/mouse simulation (darwin/win32/linux backends)
│   │   ├── computer-use-mcp/      — Computer Use MCP server (screenshot/input/clipboard/app mgmt)
│   │   ├── computer-use-swift/    — Screenshot + app management (per-platform backends)
│   │   ├── ink/                   — Forked Ink terminal-UI framework (components, hooks, keybindings, theme)
│   │   └── model-provider/        — Model-provider abstraction layer
│   ├── acp-link/          — ACP proxy server (WebSocket → ACP agent bridge, CLI `acp-link`)
│   │   ├── README.md      — Package readme
│   │   ├── package.json   — Package manifest
│   │   ├── tsconfig.json  — Package TS config
│   │   └── src/           — ACP-link implementation source
│   ├── agent-tools/       — Agent-facing tool set package
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── src/           — Agent-tool implementations
│   ├── audio-capture-napi/ — Native audio-capture NAPI addon (voice input)
│   ├── builtin-tools/     — Built-in tool set (60 tools, exported as @claude-code-best/builtin-tools)
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── src/           — Tool implementations (tools/ + shared/)
│   ├── color-diff-napi/   — Color-difference computation NAPI addon
│   ├── image-processor-napi/ — Image-processing NAPI addon
│   ├── mcp-client/        — MCP client library package
│   ├── modifiers-napi/    — Keyboard-modifier detection (macOS FFI)
│   ├── remote-control-server/ — Self-hosted Remote Control Server + Web UI (Docker)
│   │   ├── Dockerfile     — RCS container image
│   │   ├── README.md      — RCS readme
│   │   ├── components.json — Web UI component config (Radix/shadcn-style)
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   ├── src/           — RCS server source (handlers, relay, SSE, auth)
│   │   └── web/           — React 19 + Vite + Radix UI control-panel frontend
│   ├── url-handler-napi/  — URL-scheme handling (env-var + CLI arg reading)
│   └── weixin/            — WeChat (微信) integration package
├── scripts/               — Build/dev/tooling scripts   (expanded)
│   ├── check-bundle-integrity.ts — Verifies built bundle integrity
│   ├── defines.ts         — Central MACRO defines (version, build constants)
│   ├── dev-debug.ts       — Dev mode with debugger attach
│   ├── dev.ts             — Dev runner (injects feature flags + MACRO via bun -d)
│   ├── dump-prompt.ts     — Dumps the system prompt
│   ├── post-build.ts      — Vite post-build patching + vendor copy
│   ├── postinstall.cjs    — postinstall hook
│   ├── probe-local-wiring.ts — Probes local feature wiring
│   ├── probe-subscription-endpoints.ts — Probes subscription API endpoints
│   ├── rcs-ccb.sh         — RCS launch shell helper
│   ├── rcs.ts             — Remote Control Server launcher
│   ├── run-parallel.mjs   — Runs multiple postinstall scripts in parallel
│   ├── setup-chrome-mcp.mjs — Chrome MCP setup script
│   ├── smoke-test-commands.ts — Smoke-tests CLI commands
│   ├── verify-autofix-pr.ts — Verifies autofix-PR command
│   ├── vite-plugin-feature-flags.ts — Vite plugin injecting feature flags
│   └── vite-plugin-import-meta-require.ts — Vite plugin patching import.meta.require
├── spec/                  — Feature design specs   (expanded)
│   ├── feature_20260502_F001_fork-agent-redesign/ — Fork-agent redesign spec
│   └── feature_20260508_F001_tool-search/         — Tool-search feature spec
├── src/                   — Main CLI source tree   (expanded)
│   ├── QueryEngine.ts     — High-level conversation orchestrator wrapping query()
│   ├── Task.ts            — Task abstraction
│   ├── Tool.ts            — Tool interface type + lookup utilities
│   ├── commands.ts        — Slash-command registry
│   ├── context.ts         — Builds system/user context (git, date, CLAUDE.md, memory)
│   ├── cost-tracker.ts    — Token/cost tracking
│   ├── costHook.ts        — Cost hook integration
│   ├── dialogLaunchers.tsx — Launchers for interactive dialogs
│   ├── history.ts         — Conversation history persistence
│   ├── interactiveHelpers.tsx — Interactive REPL helper utilities
│   ├── main.tsx           — Commander.js CLI definition (~5674 lines, all subcommands)
│   ├── projectOnboardingState.ts — Project onboarding/trust state
│   ├── query.ts           — Core API query loop (streaming, tool calls, turn loop)
│   ├── replLauncher.tsx   — Launches the REPL screen
│   ├── setup.ts           — Misc setup helpers
│   ├── tasks.ts           — Task registry/dispatch
│   ├── tools.ts           — Tool registry (assembles tool list, feature-gated)
│   ├── __tests__/         — Top-level unit tests
│   ├── assistant/         — Assistant session chooser/discovery/history
│   ├── bootstrap/         — Session-global singletons (session id, cwd, model overrides)
│   ├── bridge/            — Remote Control / Bridge mode (BRIDGE_MODE feature)
│   ├── buddy/             — "Buddy" companion UI/notifications (BUDDY feature)
│   ├── cli/               — CLI I/O plumbing (print, bg sessions, transports, handlers)
│   ├── commands/          — Slash-command implementations (200+ command dirs/files)
│   ├── components/        — Ink terminal-UI components (~150 components)
│   ├── constants/         — Shared constants (betas, oauth, keys, tools, messages)
│   ├── context/           — React context providers (stats, mailbox, overlays, voice)
│   ├── coordinator/       — Coordinator/multi-worker mode
│   ├── daemon/            — Long-running daemon supervisor (DAEMON feature)
│   ├── entrypoints/       — Process entrypoints (cli.tsx, init, mcp, sdk)
│   ├── environment-runner/ — BYOC environment-runner entry
│   ├── hooks/             — React hooks (permissions, suggestions, away-summary, …)
│   ├── jobs/              — Template job classification/state
│   ├── keybindings/       — Keybinding parsing/resolution/context
│   ├── memdir/            — Memory directory / relevant-memory discovery
│   ├── migrations/        — One-time settings/model migrations
│   ├── modes/             — Persona/mode definitions + store
│   ├── moreright/         — "More right" panel hook
│   ├── native-ts/         — Native-TS helpers (file-index)
│   ├── outputStyles/      — Output-style loading
│   ├── plugins/           — Plugin system (builtin + bundled plugins)
│   ├── proactive/         — Proactive suggestion engine
│   ├── query/             — Query helpers (config, deps, token budget, transitions)
│   ├── remote/            — Remote session manager + permission bridge + WebSocket
│   ├── schemas/           — Shared zod schemas (hooks)
│   ├── screens/           — Top-level Ink screens (REPL, Doctor, ResumeConversation)
│   ├── self-hosted-runner/ — Self-hosted runner entry
│   ├── server/            — Direct-connect / headless server + session manager
│   ├── services/          — Service layer (API providers, MCP, auth, lsp, voice, memory)
│   ├── skills/            — Skill loading (bundled + MCP skills)
│   ├── ssh/               — SSH remote session manager/deploy/probe
│   ├── state/             — Central AppState store + selectors
│   ├── tasks/             — Task implementations (agent/shell/workflow/remote tasks)
│   ├── types/             — Shared TypeScript types + .d.ts declarations
│   ├── upstreamproxy/     — Upstream proxy relay
│   ├── utils/             — Utility modules (~373 files: model providers, claudemd, ripgrep, …)
│   ├── vim/               — Vim-mode motions/operators/text-objects
│   └── voice/             — Voice-mode enablement
├── teach-me/              — Learner/teaching materials   (expanded)
│   ├── learner-profile.md — Learner profile notes
│   └── vllm/              — vLLM teaching materials
├── tests/                 — Shared/integration tests   (expanded)
│   ├── integration/       — 6 integration tests (cli-arguments, context-build, message-pipeline, tool-chain, …)
│   └── mocks/             — Shared mocks/fixtures (log, debug, axios, file-system, api-responses)
└── vendor/                — Vendored native binaries/source   (expanded)
    ├── audio-capture/     — Prebuilt audio-capture binary
    └── audio-capture-src/ — Audio-capture source
```
