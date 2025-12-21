# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
pnpm install          # Install dependencies
pnpm build            # Build with ncc to dist/
pnpm test             # Run tests with coverage
pnpm lint             # Check linting and formatting
pnpm lint:fix         # Fix linting and formatting issues
pnpm all              # Run lint:fix, build, and test
```

Run a single test file:
```bash
pnpm test -- __tests__/validator.test.ts
```

## Architecture

This is a GitHub Action that validates PowerOn files against Jack Henry Symitar systems. The action connects to a Symitar host (via SSH or HTTPS) and validates PowerOn source files.

### Core Flow

1. **main.ts** - Entry point. Parses GitHub Action inputs, validates configuration, and calls `validatePowerOns()`

2. **validator.ts** - Core validation logic:
   - `validatePowerOns()` - Main orchestrator that validates API key, determines files to validate, and delegates to connection-specific handlers
   - `validateWithHTTPs()` / `validateWithSSH()` - Connection-specific validation using `@libum-llc/symitar` client
   - `getChangedFilesFromGit()` - Uses git diff to find changed PowerOn files when `target-branch` is provided

3. **subscription.ts** - API key validation against Libum license server with retry logic

### Key Dependencies

- `@libum-llc/symitar` - Proprietary client library for Symitar communication (SymitarHTTPs, SymitarSSH classes)
- `@actions/core` - GitHub Actions toolkit for inputs/outputs/logging

### File Detection Modes

- **With target-branch**: Uses `git diff` to find changed files in `poweron-directory`
- **Without target-branch**: Compares local directory against files deployed on host via Symitar client
