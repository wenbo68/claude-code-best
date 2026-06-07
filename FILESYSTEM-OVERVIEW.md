# Filesystem Overview

## Summary

`claude-code-best` (v2.6.11) is a reverse-engineered, community-maintained fork of Anthropic's official Claude Code CLI tool. It is an interactive AI coding assistant that runs entirely in the terminal, built on the Bun runtime with React/Ink for the TUI. The project restores and extends features that were stripped or feature-flagged in the original decompiled codebase, while adding OpenAI/Gemini/Grok provider compatibility, a self-hosted Remote Control Server (RCS), ACP (Agent Client Protocol) support, voice mode, computer-use automation, and more. TypeScript strict mode is enforced throughout.

---

## Root Directory

```
claude-code-best/
├── .claude/                  # Claude Code agent config (commands, agents, skills, settings)
├── .dockerignore             # Docker build exclusions
├── .editorconfig             # Editor formatting standards (2-space indent)
├── .github/                  # GitHub Actions CI/CD workflows
├── .gitignore                # Git exclusions
├── .husky/                   # Git hook scripts (pre-commit lint-staged)
├── .impeccable.md            # Design context for Web UIs (brand, color, principles)
├── .mintignore               # Mintlify docs exclusions
├── .npmrc                    # npm/bun registry config
├── .tool-versions            # asdf tool version pins (bun)
├── .vscode/                  # VS Code workspace settings
├── AGENTS.md                 # Agent/tool documentation for AI assistants
├── CLAUDE.md                 # Primary codebase instructions for Claude Code
├── DEV-LOG.md                # Chronological development log
├── Friends.md                # Community/contributor acknowledgements
├── README.md                 # Main project README (Chinese)
├── README_EN.md              # English README
├── SECURITY.md               # Security policy
├── biome.json                # Biome lint/format config (covers src/, scripts/, packages/)
├── build.ts                  # Bun.build() entry — code-splitting build with feature flags
├── bun.lock                  # Bun lockfile
├── bunfig.toml               # Bun runtime config
├── codecov.yml               # Codecov coverage upload config
├── contributors.svg          # Auto-generated contributor avatars
├── docs.json                 # Mintlify docs navigation config (new format)
├── docs/                     # Mintlify documentation source (MDX pages)
├── knip.json                 # Knip unused-exports config
├── mint.json                 # Mintlify docs config (legacy)
├── package.json              # Root package manifest; Bun workspaces
├── packages/                 # Monorepo workspace packages
├── progress.md               # Feature restoration progress tracker
├── scripts/                  # Build, dev, and utility scripts
├── spec/                     # Specification / design docs
├── src/                      # Main application source (TypeScript/TSX)
├── teach-me/                 # teach-me skill assets
├── tests/                    # Integration tests and shared mocks
├── tsconfig.base.json        # Shared TypeScript base config
├── tsconfig.json             # Root TypeScript config (references workspace packages)
├── vendor/                   # Vendored binaries (audio-capture, ripgrep)
└── vite.config.ts            # Vite build config (alternative code-splitting pipeline)
```

---

## `src/` — Main Application Source

```
src/
├── QueryEngine.ts            # High-level conversation orchestrator (compaction, attribution)
├── Task.ts                   # Task data model
├── Tool.ts                   # Tool interface, findToolByName, toolMatchesName
├── __tests__/                # Unit tests for root-level modules
├── assistant/                # Assistant-mode helpers
├── bootstrap/                # Module-level singletons (session ID, CWD, token counts)
├── bridge/                   # Remote Control / Bridge mode (feature-gated: BRIDGE_MODE)
├── buddy/                    # Buddy feature (companion agent)
├── cli/                      # CLI parsing helpers
├── commands.ts               # Slash-command registry
├── commands/                 # Individual slash command implementations
├── components/               # 149 Ink/React terminal UI components
├── constants/                # App-wide constants (CORE_TOOLS whitelist, etc.)
├── context.ts                # System prompt builder (git, CLAUDE.md, memory)
├── context/                  # Context helpers
├── coordinator/              # Multi-worker coordinator mode
├── cost-tracker.ts           # Token cost tracking
├── costHook.ts               # Cost hook integration
├── daemon/                   # Long-running daemon/supervisor (feature-gated: DAEMON)
├── dialogLaunchers.tsx       # Helper to launch Ink dialog overlays
├── entrypoints/              # CLI entry files (cli.tsx, init.ts, etc.)
├── environment-runner/       # BYOC environment runner
├── history.ts                # Conversation history persistence
├── hooks/                    # React hooks for UI
├── interactiveHelpers.tsx    # Interactive CLI helper functions
├── jobs/                     # Background job management
├── keybindings/              # Keyboard shortcut configuration
├── main.tsx                  # Commander.js CLI definition (~5700 lines)
├── memdir/                   # Memory directory management
├── migrations/               # Config/state migration scripts
├── modes/                    # Operation modes (plan, auto, etc.)
├── moreright/                # Additional right-panel UI
├── native-ts/                # Native TypeScript bridges
├── outputStyles/             # Terminal output style utilities
├── plugins/                  # Plugin system (install/unload/marketplace)
├── proactive/                # Proactive suggestion system
├── projectOnboardingState.ts # Per-project onboarding state
├── query.ts                  # Core API query loop (~3000 lines)
├── query/                    # Query helpers and sub-modules
├── remote/                   # Remote session support
├── replLauncher.tsx          # REPL screen launcher
├── schemas/                  # Zod/JSON schemas
├── screens/                  # Full-screen Ink screens (REPL.tsx, etc.)
├── self-hosted-runner/       # Self-hosted runner support
├── server/                   # Embedded HTTP server mode
├── services/                 # Service layer (API clients, MCP, search, ACP, skills)
├── setup.ts                  # One-time setup / auth initialization
├── skills/                   # Skill loading and search
├── ssh/                      # SSH remote connection support
├── state/                    # Zustand-style app state (AppState, store, selectors)
├── tasks.ts                  # Task registry
├── tasks/                    # Task implementation helpers
├── tools.ts                  # Tool registry (assembles tool list from builtin-tools)
├── types/                    # Global type declarations (global.d.ts, message.ts, etc.)
├── upstreamproxy/            # Upstream proxy support
├── utils/                    # Utility modules (200+ files: model, fs, string, debug…)
├── vim/                      # Vim keybinding mode
└── voice/                    # Voice mode (push-to-talk, Anthropic OAuth)
```

---

## `packages/` — Workspace Packages

```
packages/
├── @ant/
│   ├── claude-for-chrome-mcp/   # MCP server for Chrome browser control
│   ├── computer-use-input/      # Keyboard/mouse simulation (cross-platform backends)
│   ├── computer-use-mcp/        # Computer-use MCP server (screenshot, clipboard, apps)
│   ├── computer-use-swift/      # Screenshot + app management (macOS/Windows/Linux)
│   ├── ink/                     # Forked Ink TUI framework (components, hooks, keybindings)
│   └── model-provider/          # Model provider abstraction layer
├── acp-link/                    # ACP proxy server (WebSocket → ACP agent bridge)
├── agent-tools/                 # Agent-specific tool implementations
├── audio-capture-napi/          # Native audio capture (NAPI)
├── builtin-tools/               # 60 built-in tool implementations (FileRead, Bash, etc.)
├── color-diff-napi/             # Color difference calculation (NAPI, 11 tests)
├── image-processor-napi/        # Image processing (NAPI)
├── mcp-client/                  # MCP (Model Context Protocol) client library
├── modifiers-napi/              # Keyboard modifier key detection (macOS FFI)
├── remote-control-server/       # Self-hosted RCS with Web UI (React + Vite + Radix UI)
├── tsconfig.json                # Shared tsconfig for packages
├── url-handler-napi/            # URL scheme handler (env vars + CLI args)
└── weixin/                      # WeChat integration
```

---

## `scripts/` — Build & Dev Scripts

```
scripts/
├── check-bundle-integrity.ts    # Verify built bundle correctness
├── defines.ts                   # Centralized MACRO defines (version, feature flags)
├── dev-debug.ts                 # Dev mode with Bun debugger
├── dev.ts                       # Dev runner (injects -d flag defines)
├── dump-prompt.ts               # Dump system prompt to stdout
├── health-check.ts              # Project health check script
├── post-build.ts                # Vite post-build: globalThis.Bun patch + vendor copy
├── postinstall.cjs              # npm postinstall: download native binaries
├── probe-local-wiring.ts        # Debug local API wiring
├── probe-subscription-endpoints.ts  # Debug subscription API endpoints
├── production-test.ts           # Production smoke test runner
├── rcs-ccb.sh                   # Shell helper for RCS + CCB startup
├── rcs.ts                       # Start Remote Control Server
├── run-parallel.mjs             # Run two scripts in parallel (postinstall)
├── setup-chrome-mcp.mjs         # Set up Chrome MCP extension
├── smoke-test-commands.ts       # Smoke test slash commands
├── verify-autofix-pr.ts         # Verify autofix PR pipeline
├── vite-plugin-feature-flags.ts # Vite plugin: inject feature flag defines
└── vite-plugin-import-meta-require.ts  # Vite plugin: rewrite import.meta.require
```

---

## `tests/` — Test Infrastructure

```
tests/
├── integration/      # 6 integration test files (cli-arguments, context-build, etc.)
└── mocks/            # Shared mocks (api-responses, file-system, fixtures, log, debug)
```

---

## `docs/` — Documentation Source

Mintlify MDX pages covering features such as remote control self-hosting, multi-API compat layers (OpenAI/Gemini/Grok), computer use, voice mode, budget mode, and the testing spec.

---

## `vendor/`

Vendored binaries: `audio-capture/` and ripgrep per-platform builds. Copied to `dist/vendor/` at build time.

---

## `.claude/`

Claude Code agent configuration: custom commands (e.g. `create-overview`), agent definitions, skill definitions, and `settings.json` (permissions, hooks).
