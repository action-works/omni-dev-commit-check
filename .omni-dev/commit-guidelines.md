# omni-dev-commit-check Commit Guidelines

This project follows conventional commit format with specific requirements.

## Severity Levels

| Severity | Sections                                                               |
|----------|------------------------------------------------------------------------|
| error    | Commit Format, Types, Scopes, Subject Line, Accuracy, Breaking Changes |
| warning  | Body Guidelines                                                        |
| info     | Subject Line Style                                                     |

## Commit Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

## Types

Required. Must be one of:

| Type       | Use for                                               |
|------------|-------------------------------------------------------|
| `feat`     | New features or enhancements to existing features     |
| `fix`      | Bug fixes                                             |
| `docs`     | Documentation changes only                            |
| `refactor` | Code refactoring without behavior changes             |
| `chore`    | Maintenance tasks, dependency updates, config changes |
| `test`     | Test additions or modifications                       |
| `ci`       | CI/CD pipeline changes                                |

## Scopes

Required. Use scopes defined in `.omni-dev/scopes.yaml`:

- `action` - GitHub Action definition and composite steps
- `docs` - Documentation (README, examples, guidelines)
- `ci` - GitHub workflows and automation

## Subject Line

- Keep under 72 characters total
- Use imperative mood: "add feature" not "added feature" or "adds feature"
- Be specific: avoid vague terms like "update", "fix stuff", "changes"

## Subject Line Style

- Use lowercase for the description
- No period at the end

## Accuracy

The commit message must accurately reflect the actual code changes:

- **Type must match changes**: Don't use `feat` for a bug fix, or `fix` for new functionality
- **Scope must match files**: The scope should reflect which area of code was modified
- **Description must be truthful**: Don't claim changes that weren't made
- **Mention significant changes**: If you add new inputs, outputs, or change behavior, mention it

## Body Guidelines

For significant changes (new inputs/outputs or behavior changes), include a body:

- Explain what was changed and why
- Describe the approach taken
- Note any breaking changes or migration requirements
- Use bullet points for multiple related changes
- Reference issues in footer: `Closes #123` or `Fixes #456`

## Breaking Changes

For breaking changes:
- Add `!` after type/scope: `feat(action)!: change default batch-size`
- Include `BREAKING CHANGE:` footer with migration instructions

## Examples

### Simple change
```
fix(action): handle missing API key gracefully
```

### Feature with body
```
feat(action): add AWS Bedrock provider support

Adds new inputs for AWS Bedrock configuration:
- use-bedrock: Enable Bedrock provider
- bedrock-base-url: Bedrock endpoint URL
- bedrock-auth-token: Authentication token

Closes #5
```

### Documentation update
```
docs(docs): add examples for JSON output processing
```

### Breaking change
```
feat(action)!: change default output format to JSON

BREAKING CHANGE: The default format is now JSON instead of text.
Users relying on text output must add `format: text` to their workflow.
```
