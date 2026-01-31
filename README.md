<!-- IGNORE:TK - This file documents the TK-TODO check action -->
# TK-TODO Check Action

A reusable GitHub Action that checks for `TK-TODO` markers in your codebase. These markers indicate temporary code or placeholders that must be resolved before merging.

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
        uses: aaronsteers/tk-todo-check-action@v1
```

### With Custom Options

<!-- IGNORE:TK - example code -->
```yaml
- name: Check for TK-TODO markers
  uses: aaronsteers/tk-todo-check-action@v1
  with:
    fail-on-found: "false"  # Don't fail, just report
```

### Using Outputs

<!-- IGNORE:TK - example code -->
```yaml
- name: Check for TK-TODO markers
  id: todo-check
  uses: aaronsteers/tk-todo-check-action@v1
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
