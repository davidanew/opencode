# opencode Agent Guidelines

## Build/Test Commands

- **Install**: `bun install`
- **Dev**: `bun run dev` (runs main TypeScript server)
- **Typecheck**: `bun run typecheck` (checks all packages)
- **Single test**: `bun test packages/opencode/test/tool/tool.test.ts`
- **Install git hooks**: `./scripts/hooks`
- **Generate Go SDK**: `bun run stainless` (after API changes)

## Code Style

- **Runtime**: Bun with TypeScript ESM modules, package manager `bun@1.2.14`
- **Formatting**: Prettier with `semi: false`, 2-space indentation, LF line endings
- **Imports**: Relative imports for local modules, named imports preferred
- **Types**: Zod schemas for validation, avoid `any` type
- **Naming**: camelCase variables/functions, PascalCase classes/namespaces
- **Variables**: Prefer `const`, avoid `let`, single-word names when possible
- **Control flow**: Avoid `else` statements and `try`/`catch` where possible
- **Error handling**: Use Result patterns, avoid throwing exceptions in tools
- **APIs**: Use Bun APIs like `Bun.file()` when possible

## Architecture

- **Monorepo**: Workspaces in `packages/` (opencode, tui, web)
- **Tools**: Implement `Tool.Info` interface with `execute()` method
- **Context**: Pass `sessionID` in tool context, use `App.provide()` for DI
- **Storage**: Use `Storage` namespace for persistence
- **API**: Go TUI ↔ TypeScript server via stainless SDK
