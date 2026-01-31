<!-- IGNORE:TK - This file documents the TK-TODO check action -->
# TK-TODO Check Action

A reusable GitHub Action that checks for `TK-TODO` markers in your codebase. These markers indicate temporary code or placeholders that must be resolved before merging.

## Why TK-TODO?

The "TK" in TK-TODO comes from the publishing industry term ["to come" (TK)](https://en.wikipedia.org/wiki/To_come_(publishing)), used to mark where additional material will be added before publication. Since very few English words contain the letter combination "TK", it's easily searchable and visually distinctive in running text.

Traditional `TODO` comments in code have a fundamental ambiguity problem: there's no clear way to distinguish between a TODO that should block a PR from merging versus one that's simply a note for future developers. This leads to TODOs that were meant to be temporary slipping through code review and living in the codebase forever.

The `TK-TODO` convention solves this by providing an explicit, unambiguous signal. A TK-TODO doesn't block anything unless the PR author intentionally adds one, indicating "this cannot be merged yet." It's a deliberate choice to mark something that maybe can't be resolved immediately but absolutely should not slip through and be merged.

### Example Use Cases

- **Unreleased dependencies**: You're waiting for a library update to be published before your code can work in production
- **Documentation requirements**: Code that needs documentation written before it can be merged
- **Subject matter expert review**: Areas where you need confirmation from a domain expert before proceeding
- **Placeholder implementations**: Temporary code that must be replaced with a real implementation
- **Configuration values**: Hardcoded values that need to be moved to environment variables or config files
- **Test coverage**: Code paths that need tests written before the PR is complete

## Features

- Scans all tracked files for `TK-TODO` markers (case-insensitive)
- Supports suppression via `IGNORE:TK` on the same line
- Outputs count and list of found markers
- Configurable failure behavior

## Usage

### Basic Example

<!-- IGNORE:TK - example code -->
```yaml
name: TK-TODO Check

on:
  push:
    branches:
      - main
  pull_request: {}

permissions:
  contents: read

jobs:
  tk-todo-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Check for TK-TODO markers
        uses: aaronsteers/tk-todo-check-action@v1.0.0
```

### With Custom Options

<!-- IGNORE:TK - example code -->
```yaml
- name: Check for TK-TODO markers
  uses: aaronsteers/tk-todo-check-action@v1.0.0
  with:
    fail-on-found: "false"  # Don't fail, just report
```

### Using Outputs

<!-- IGNORE:TK - example code -->
```yaml
- name: Check for TK-TODO markers
  id: todo-check
  uses: aaronsteers/tk-todo-check-action@v1.0.0
  with:
    fail-on-found: "false"

- name: Report results
  run: |
    echo "Found ${{ steps.todo-check.outputs.found-count }} TK-TODO markers"
    echo "Markers:"
    echo "${{ steps.todo-check.outputs.found-markers }}"
```

## Inputs

<!-- IGNORE:TK - documentation table -->
| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `fail-on-found` | Whether to fail the action if TK-TODO markers are found | false | `true` |
| `exclude-patterns` | Glob patterns to exclude from the check (space-separated) | false | `""` |

## Outputs

<!-- IGNORE:TK - documentation table -->
| Name | Description |
|------|-------------|
| `found-count` | Number of TK-TODO markers found |
| `found-markers` | List of found TK-TODO markers (file:line format) |

## Suppressing Markers

To suppress a `TK-TODO` marker (for example, in documentation or test files), add `IGNORE:TK` (case-insensitive) on the same line:

<!-- IGNORE:TK - example code -->
```python
# TK-TODO: This needs to be fixed  # IGNORE:TK - documented in issue #123
```

<!-- IGNORE:TK - example code -->
```yaml
name: TK-TODO Check  # IGNORE:TK - this workflow checks for TK-TODO markers
```

## How It Works

The action uses `git ls-files` to scan only tracked files (respecting `.gitignore`), then searches for `TK-TODO` markers using grep. Lines containing `IGNORE:TK` are excluded from the results.

## License

This project is licensed under the terms of the [MIT License](LICENSE).
