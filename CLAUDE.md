# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**opencode** is an open-source AI coding agent for the terminal, similar to Claude Code but with key differences:
- 100% open source (MIT licensed)
- Provider-agnostic (supports Anthropic, OpenAI, Google, and local models)
- Terminal-focused UI built by neovim users
- Client/server architecture enabling remote operation

## Development Commands

### Prerequisites
- **Bun** (package manager and runtime)
- **Go 1.24.x** (for TUI development)
- **Node.js** (for TypeScript/JavaScript tooling)

### Essential Commands

```bash
# Install dependencies
bun install

# Run development mode
bun run dev

# Type checking (all packages)
bun run typecheck

# Run tests
bun test

# Run specific test
bun test packages/opencode/test/tool/tool.test.ts

# Install git hooks
./scripts/hooks

# Generate Go SDK after API changes
bun run stainless
```

### Package-Specific Commands

**OpenCode CLI** (`packages/opencode/`):
```bash
bun run dev                    # Run development server
bun run ./src/index.ts        # Alternative dev run
bun run typecheck             # Type checking
```

**TUI** (`packages/tui/`):
```bash
go build ./cmd/opencode       # Build binary
go test ./...                 # Run all tests
```

**Web** (`packages/web/`):
```bash
bun run dev                   # Development server
bun run build                 # Production build
bun run preview               # Preview production build
```

## Architecture & Code Structure

### Monorepo Structure
- **`packages/opencode/`** - Main TypeScript server (Bun runtime)
  - Tools implementation (bash, edit, grep, ls, etc.)
  - Authentication providers (Anthropic, GitHub Copilot)
  - LSP and MCP support
  - Session management
- **`packages/tui/`** - Go-based Terminal UI
  - Bubble Tea v2 framework with Lipgloss v2 styling
  - Communicates with server via generated SDK
- **`packages/web/`** - Astro-based documentation site

### Key Architectural Patterns

1. **Namespace-based organization**: Use patterns like `Tool.define()`, `Session.create()`
2. **Dependency Injection**: Use `App.provide()` for context passing
3. **Tool Implementation**: Implement `Tool.Info` interface with `execute()` method
4. **API Communication**: Go TUI ↔ TypeScript server via Stainless-generated SDK
5. **Storage**: Use `Storage` namespace for persistence
6. **Logging**: Use `Log.create({ service: "name" })` pattern

### Code Style Guidelines

**TypeScript (packages/opencode)**:
- Runtime: Bun with TypeScript ESM modules
- Formatting: Prettier with `semi: false`, 2-space indentation, LF line endings
- Prefer `const` over `let`, avoid `any` type
- Use Zod schemas for validation
- Use Result patterns for error handling (avoid throwing exceptions)
- Avoid `else` statements and `try`/`catch` where possible
- Use Bun APIs like `Bun.file()` when available
- Keep logic in single functions unless composable/reusable
- Avoid unnecessary destructuring
- Prefer single-word variable names where appropriate

**Go (packages/tui)**:
- Go 1.24+ with standard formatting (`gofmt`)
- Follow Go conventions: PascalCase exports, camelCase private
- Return errors explicitly, use `fmt.Errorf` for wrapping
- Use table-driven tests

### Testing

- **TypeScript**: Bun's built-in test runner
  - Tests in `packages/opencode/test/`
  - Use `describe()` and `test()` blocks
  - Snapshot testing with `toMatchSnapshot()`
  - Async/await for asynchronous operations

- **Go**: Standard Go testing
  - Use table-driven tests
  - `t.TempDir()` for file operations

### Important Notes

1. **Main branch**: `dev` (not `main`)
2. **API Changes**: When modifying server endpoints in `packages/opencode/src/server/server.ts`, regenerate the Go SDK using `bun run stainless`
3. **Configuration**: Project uses `opencode.json` for configuration
4. **Hooks**: Supports experimental hooks for file editing and session completion
5. **Themes**: JSON-based theming system in TUI with override hierarchy

### Common Development Tasks

1. **Adding a new tool**:
   - Implement `Tool.Info` interface in `packages/opencode/src/tool/`
   - Add Zod schema for validation
   - Update tool exports
   - Write tests in `packages/opencode/test/tool/`

2. **Modifying API**:
   - Update endpoint in `packages/opencode/src/server/server.ts`
   - Run `bun run stainless` to regenerate Go SDK
   - Update TUI client code as needed

3. **Running in development**:
   - Use `bun run dev` for hot reloading
   - Server runs on port configured in environment
   - TUI connects to server via SDK