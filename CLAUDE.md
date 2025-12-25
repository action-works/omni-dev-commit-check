# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a GitHub Action that validates commit messages using AI-powered analysis from the [omni-dev](https://github.com/rust-works/omni-dev) CLI tool. It supports multiple AI providers (Anthropic Claude, OpenAI, Ollama, AWS Bedrock).

## Repository Structure

- `action.yml` - The composite GitHub Action definition (the core of this project)
- `README.md` - User documentation with examples and input/output reference
- `.omni-dev/` - Project guidelines for commits and PRs
- `.github/pull_request_template.md` - PR template

## How It Works

The action is a composite action that:
1. Installs `omni-dev` via `cargo install` (uses the specified version or latest)
2. Runs `omni-dev git commit message check` with arguments constructed from action inputs
3. Parses output and sets GitHub Action outputs (including JSON parsing with jq for structured output)

## Key Technical Details

- **Exit codes**: 0 = success, 1 = errors, 2 = warnings (with strict mode), 3 = no commits found
- **Environment variables**: API keys and provider configs are passed via env vars (e.g., `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `CLAUDE_CODE_USE_BEDROCK`)
- **Commit range detection**: Auto-detected from base branch if not explicitly specified
- **Guidelines discovery**: Defaults to `.omni-dev/commit-guidelines.md` in the target repository

## Commit and PR Guidelines

This project uses conventional commits with required scopes. See `.omni-dev/commit-guidelines.md` for details.

**Scopes**: `action`, `docs`, `ci`

**Example commits**:
```
feat(action): add verbose output flag
fix(action): handle missing API key gracefully
docs(docs): add Bedrock usage example
```
