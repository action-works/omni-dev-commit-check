# Pull Request Guidelines

This document provides guidelines for writing effective pull request descriptions for the omni-dev-commit-check GitHub Action.

## Overview

A well-written pull request serves multiple purposes:

- **Documentation**: Explains what changed and why
- **Review Aid**: Helps reviewers understand context and focus areas
- **Project History**: Creates searchable record for future reference
- **Quality Assurance**: Ensures all necessary steps are completed

### Key Principles

1. **Clarity**: Write for reviewers who may not have context
2. **Completeness**: Include all necessary information for review
3. **Conciseness**: Be thorough but avoid unnecessary verbosity
4. **Actionability**: Provide clear testing and validation steps

## AI-Generated PR Descriptions

**CRITICAL FOR AI ASSISTANTS**: When generating PR descriptions, you MUST:

1. **ALWAYS REPLACE PLACEHOLDERS**: Never leave placeholder text like "TODO", "FIXME", or generic instructions
2. **FILL IN ACTUAL CONTENT**: Every section must contain specific information based on actual code changes
3. **BE SPECIFIC ABOUT CHANGES**: List exact files, inputs, and features modified
4. **NO GENERIC TEXT**: Avoid generic statements like "various improvements"
5. **REMOVE TEMPLATE COMMENTS**: Delete all HTML comments and instructional text
6. **ANALYZE THE DIFFS**: Read through all commit diffs to understand what changed
7. **PROVIDE REAL EXAMPLES**: If the template asks for test workflows, provide actual YAML
8. **COMPLETE ALL CHECKLISTS**: Mark items as checked `[x]` only if actually completed

### What NOT to Do

**NEVER do this:**
```markdown
## Description
<!-- Provide a clear overview of changes -->
Add description here.

## Changes Made
**Action Definition:**
-
-
```

**ALWAYS do this instead:**
```markdown
## Description
This PR adds support for the `--quiet` flag, allowing users to suppress info-level output in their CI logs. Only errors and warnings will be displayed when this option is enabled.

## Changes Made
**Action Definition:**
- Added `quiet` input with default value `false` in action.yml
- Updated run step to pass `--quiet` flag when input is true
- Added output example to README showing quiet mode behavior
```

### AI Generation Checklist

Before returning a PR description, verify:
- [ ] All HTML comments and instructional text removed
- [ ] All placeholder bullet points replaced with actual content
- [ ] Specific file names and inputs mentioned where relevant
- [ ] Test workflow examples are actual YAML, not placeholders
- [ ] Checklist items accurately reflect what was done
- [ ] No sections left empty (either fill them or remove them)
- [ ] Description explains WHAT changed and WHY based on actual commits

## PR Title Guidelines

### Format
Use conventional commit format:
```
<type>(<scope>): <description>
```

### Types
- `feat`: New features or enhancements (new inputs, outputs, providers)
- `fix`: Bug fixes
- `docs`: Documentation changes (README, examples)
- `refactor`: Code restructuring without functional changes
- `chore`: Maintenance tasks, dependency updates
- `ci`: CI/CD pipeline changes

### Scope Examples
- `action`: Changes to action.yml
- `docs`: Documentation updates
- `ci`: Workflow changes

### Title Best Practices

**Good Examples:**
- `feat(action): add Ollama provider support`
- `fix(action): handle empty commit range gracefully`
- `docs(docs): add AWS Bedrock configuration example`

**Avoid:**
- `Update action` (too vague)
- `Fix bug` (no context)
- `Add new feature` (too verbose)

## Template Section Guidelines

### Description Section
- Start with one-sentence summary
- Explain the problem being solved
- Describe the solution approach
- Mention user-facing impact

### Type of Change Section
- Select all applicable types
- Use "Breaking change" sparingly and document migration
- Choose "Bug fix" only for actual defects

### Changes Made Section
- Be specific about what was changed
- Group related changes (Action Definition, Documentation, CI)
- Focus on significant changes

**Example:**
```markdown
## Changes Made

**Action Definition:**
- Added `use-ollama` input to enable Ollama provider
- Added `ollama-base-url` and `ollama-model` inputs
- Updated environment variable mapping for Ollama configuration

**Documentation:**
- Added Ollama configuration section to README
- Added example workflow for self-hosted Ollama
```

### Testing Section
- Include both automated testing (if applicable) and manual testing
- Provide specific workflow examples users can copy
- Note edge cases tested

**Example:**
```markdown
## Testing

**Manual Testing:**
- [x] Tested with Anthropic API key
- [x] Tested with OpenAI API key
- [x] Verified error handling for missing API key
- [x] Tested commit range detection in PR context

**Example Test Workflow:**
```yaml
- uses: rust-works/omni-dev-commit-check@this-branch
  with:
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    verbose: true
```
```

### Breaking Changes Section
Only use for actual breaking changes:
- Provide clear migration instructions
- Include before/after examples
- Explain backward compatibility status

## Common Patterns

### Adding a New Input
```markdown
## Description
Adds `batch-size` input to control how many commits are processed per AI request, allowing users to optimize for rate limits or response quality.

## Changes Made
**Action Definition:**
- Added `batch-size` input with default value `4`
- Updated ARGS array to include `--batch-size` parameter

**Documentation:**
- Added batch-size to inputs table in README
- Added performance tuning section with batch-size guidance
```

### Bug Fix
```markdown
## Description
Fixes an issue where the action would fail silently when no commits were found in the specified range. Now properly exits with code 3 and displays an informative message.

## Root Cause
The commit range calculation wasn't handling the case where HEAD equals the base branch.

## Solution
Added explicit check for empty commit range before invoking omni-dev.
```

### Documentation Update
```markdown
## Description
Adds comprehensive examples for using the action with different AI providers and output formats.

## Changes Made
**Documentation:**
- Added OpenAI configuration example
- Added AWS Bedrock example with required IAM setup
- Added JSON output processing example with jq
- Updated inputs table with clearer descriptions
```
