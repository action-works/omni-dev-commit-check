# Omni-Dev Commit Check Action

A GitHub Action that validates commit messages against project guidelines using AI-powered analysis from [omni-dev](https://github.com/rust-works/omni-dev).

## Features

- AI-powered commit message validation
- Support for multiple AI providers (Anthropic Claude, OpenAI, Ollama, AWS Bedrock)
- Customizable commit guidelines
- Multiple output formats (text, JSON, YAML)
- Configurable severity levels and exit codes
- Batch processing for large commit ranges

## Quick Start

```yaml
name: Commit Check
on: [pull_request]

jobs:
  check-commits:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required for commit history

      - uses: rust-works/omni-dev-commit-check@v1
        with:
          anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

## Inputs

### API Keys

| Input               | Description                          | Required |
|---------------------|--------------------------------------|----------|
| `anthropic-api-key` | Anthropic API key for Claude models  | No*      |
| `openai-api-key`    | OpenAI API key                       | No*      |

*At least one API key is required unless using Ollama.

### Provider Selection

| Input        | Description                       | Default |
|--------------|-----------------------------------|---------|
| `use-openai` | Use OpenAI instead of Anthropic   | `false` |
| `use-ollama` | Use Ollama for local inference    | `false` |

### Model Configuration

| Input   | Description                                                   | Default          |
|---------|---------------------------------------------------------------|------------------|
| `model` | AI model to use (e.g., `claude-opus-4-5-20251101`, `gpt-4.1`) | Provider default |

### Ollama Configuration

| Input             | Description        | Default                   |
|-------------------|--------------------|---------------------------|
| `ollama-base-url` | Ollama server URL  | `http://localhost:11434`  |
| `ollama-model`    | Ollama model name  | -                         |

### AWS Bedrock Configuration

| Input                | Description                 | Default |
|----------------------|-----------------------------|---------|
| `use-bedrock`        | Use AWS Bedrock for Claude  | `false` |
| `bedrock-base-url`   | AWS Bedrock endpoint URL    | -       |
| `bedrock-auth-token` | AWS Bedrock auth token      | -       |

### Commit Range Configuration

| Input          | Description                                      | Default         |
|----------------|--------------------------------------------------|-----------------|
| `commit-range` | Commit range to check (e.g., `HEAD~5..HEAD`)     | Auto-detected   |
| `base-branch`  | Base branch to compare against                   | `main`/`master` |

### Guidelines Configuration

| Input         | Description                                      | Default         |
|---------------|--------------------------------------------------|-----------------|
| `guidelines`  | Path to custom commit guidelines file            | Auto-discovered |
| `context-dir` | Custom context directory for loading guidelines  | `.omni-dev/`    |

### Output Configuration

| Input    | Description                              | Default |
|----------|------------------------------------------|---------|
| `format` | Output format: `text`, `json`, or `yaml` | `text`  |

### Behavior Configuration

| Input            | Description                                           | Default |
|------------------|-------------------------------------------------------|---------|
| `strict`         | Exit with error code if any warnings found            | `false` |
| `quiet`          | Only show errors/warnings, suppress info-level output | `false` |
| `verbose`        | Show detailed analysis including model info           | `false` |
| `show-passing`   | Include passing commits in output                     | `false` |
| `no-suggestions` | Skip generating corrected message suggestions         | `false` |

### Performance Configuration

| Input        | Description                                  | Default |
|--------------|----------------------------------------------|---------|
| `batch-size` | Number of commits to process per AI request  | `4`     |

### Version Configuration

| Input     | Description                                | Default  |
|-----------|--------------------------------------------|----------|
| `version` | omni-dev version to use (e.g., `0.12.0`)   | `latest` |

### Main Branch Behavior

| Input          | Description                                                                          | Default |
|----------------|--------------------------------------------------------------------------------------|---------|
| `skip-on-main` | Skip commit check when running on main/master branch (cache will still be populated) | `true`  |

### Cache Configuration

| Input          | Description                                                | Default |
|----------------|------------------------------------------------------------|---------|
| `cache-prefix` | Prefix to prepend to the cache key for the omni-dev binary | `''`    |

### Installation Method

| Input                 | Description                                                                         | Default |
|-----------------------|-------------------------------------------------------------------------------------|---------|
| `use-prebuilt-binary` | Download pre-built binary from GitHub releases instead of building from source      | `true`  |

By default, the action downloads pre-built binaries from [omni-dev releases](https://github.com/rust-works/omni-dev/releases), which is significantly faster than building from source.

**Available platforms for pre-built binaries:**
- Linux (x64)
- macOS (ARM64)
- Windows (x64)

If a pre-built binary is not available for your platform or version, the action will display a warning with available options and automatically fall back to building from source via `cargo install`.

To explicitly disable binary downloads and always build from source:

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    use-prebuilt-binary: false
```

## Outputs

| Output            | Description                                             |
|-------------------|---------------------------------------------------------|
| `result`          | Full check result in the specified format               |
| `exit-code`       | Exit code (0=success, 1=errors, 2=warnings with strict) |
| `total-commits`   | Total number of commits checked (JSON format only)      |
| `passing-commits` | Number of commits that passed (JSON format only)        |
| `error-count`     | Number of errors found (JSON format only)               |
| `warning-count`   | Number of warnings found (JSON format only)             |

## Exit Codes

| Code | Meaning                                    |
|------|--------------------------------------------|
| 0    | Success: All commits pass                  |
| 1    | One or more commits have errors            |
| 2    | Warnings found (only with `strict: true`)  |
| 3    | No commits found in range                  |

## Examples

### Basic Usage with Claude

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Using OpenAI

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    openai-api-key: ${{ secrets.OPENAI_API_KEY }}
    use-openai: true
    model: gpt-4.1
```

### Strict Mode with Custom Guidelines

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    strict: true
    guidelines: .github/commit-guidelines.md
```

### Check Specific Commit Range

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    commit-range: ${{ github.event.pull_request.base.sha }}..${{ github.sha }}
```

### JSON Output for Further Processing

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  id: check
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    format: json

- name: Process results
  if: always()
  run: |
    echo "Total commits: ${{ steps.check.outputs.total-commits }}"
    echo "Errors: ${{ steps.check.outputs.error-count }}"
```

### Using AWS Bedrock

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  with:
    use-bedrock: true
    bedrock-base-url: ${{ secrets.BEDROCK_URL }}
    bedrock-auth-token: ${{ secrets.BEDROCK_TOKEN }}
    model: anthropic.claude-3-opus-20240229-v1:0
```

### PR Comment with Results

```yaml
- uses: rust-works/omni-dev-commit-check@v1
  id: check
  continue-on-error: true
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    format: text
    verbose: true

- uses: actions/github-script@v7
  if: github.event_name == 'pull_request'
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: `## Commit Message Check\n\n\`\`\`\n${{ steps.check.outputs.result }}\n\`\`\``
      })
```

## Custom Guidelines

Create a `.omni-dev/commit-guidelines.md` file in your repository:

```markdown
# Project Commit Guidelines

## Severity Levels

| Severity | Sections             |
|----------|----------------------|
| error    | Format, Subject Line |
| warning  | Content, Body        |
| info     | Style                |

## Format
- Use conventional commit format: `type(scope): description`
- Valid types: feat, fix, docs, style, refactor, test, chore, ci

## Subject Line
- Keep under 72 characters
- Use imperative mood ("add" not "added")
- Do not end with a period

## Content
- Reference issue numbers where applicable
- Explain the "why" not just the "what"
```

## Requirements

- Repository must be checked out with `fetch-depth: 0` for full commit history
- Rust toolchain (only required if using `use-prebuilt-binary: false` or if pre-built binaries are not available for your platform)
- At least one AI provider configured

## License

MIT
