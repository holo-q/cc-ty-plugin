# hsp-py — DEPRECATED

> **This plugin is obsolete.** Use **[hsp](https://github.com/holo-q/hsp)** instead — a single unified plugin that routes Python, C#, and future languages through one MCP server with built-in language detection.

hsp-py was the original Python language server plugin for Claude Code. Its functionality has been absorbed into the unified HSP plugin, which ships built-in routes for both Python (ty + basedpyright) and C# (csharp-ls) with automatic file-extension routing, shared broker sessions, and the full graph-operator tool surface.

## Migration

1. Uninstall hsp-py
2. Install **[hsp](https://github.com/holo-q/hsp)**
3. Done — no configuration changes needed. HSP auto-detects Python projects via `.py`, `.pyi`, `pyproject.toml`, `setup.py`, and `setup.cfg` markers.

## Links

- **[hsp](https://github.com/holo-q/hsp)** — the unified LSP-to-MCP bridge (install this)
- [ty](https://github.com/astral-sh/ty) — the underlying Python LSP (still used by hsp's Python route)
- [basedpyright](https://github.com/DetachHead/basedpyright) — fallback LSP for call hierarchy and rename (still used by hsp)
