# ty-lsp

Python language server plugin for Claude Code using [ty](https://github.com/astral-sh/ty) - an extremely fast type checker by Astral.

## Features

- 10-100x faster than mypy and Pyright
- Comprehensive diagnostics with rich contextual information
- Full-featured language server with code navigation, completions, code actions, auto-import, inlay hints, etc.
- Fine-grained incremental analysis for instant feedback

## Supported Extensions

`.py`, `.pyi`

## Installation

Install ty via pip:

```bash
pip install ty
```

Or with uv:

```bash
uv pip install ty
```

Or run directly with uvx:

```bash
uvx ty check
```

## More Information

- [ty Documentation](https://docs.astral.sh/ty/)
- [GitHub Repository](https://github.com/astral-sh/ty)
- [Astral](https://astral.sh)
