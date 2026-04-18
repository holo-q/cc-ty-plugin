# ty-lsp

Python language server plugin for Claude Code using [ty](https://github.com/astral-sh/ty) — an extremely fast type checker by Astral.

## How it works

This plugin provides the full LSP protocol through [cc-lsp-now](https://github.com/holo-q/cc-lsp-now), a standalone MCP bridge that spawns ty as a subprocess and exposes 14 LSP operations as MCP tools. basedpyright is configured as a fallback for operations ty doesn't yet support (call hierarchy).

### Why MCP instead of Claude Code's built-in `lspServers`?

Claude Code's native LSP integration (`lspServers` in plugin.json) is incomplete — it only exposes ~9 of 20+ LSP methods, and some of those are buggy (e.g. `workspaceSymbol` returns 0 results when the server clearly supports it). The MCP bridge gives us the full protocol, proper formatting, and a fallback chain.

**The `lspServers` config is intentionally disabled.** It will be re-enabled and cc-lsp-now progressively phased out as Claude Code's built-in LSP implementation becomes capable and reliable. Track progress at [claude-code#40282](https://github.com/anthropics/claude-code/issues/40282).

## Tools provided

| Tool | LSP Method |
|------|-----------|
| `lsp_diagnostics` | `textDocument/diagnostic` |
| `lsp_hover` | `textDocument/hover` |
| `lsp_definition` | `textDocument/definition` |
| `lsp_references` | `textDocument/references` |
| `lsp_workspace_symbols` | `workspace/symbol` |
| `lsp_type_definition` | `textDocument/typeDefinition` |
| `lsp_completion` | `textDocument/completion` |
| `lsp_signature_help` | `textDocument/signatureHelp` |
| `lsp_document_symbols` | `textDocument/documentSymbol` |
| `lsp_formatting` | `textDocument/formatting` |
| `lsp_rename` | `textDocument/rename` |
| `lsp_prepare_rename` | `textDocument/prepareRename` |
| `lsp_code_actions` | `textDocument/codeAction` |
| `lsp_call_hierarchy_incoming` | `callHierarchy/incomingCalls` |
| `lsp_call_hierarchy_outgoing` | `callHierarchy/outgoingCalls` |

## Prerequisites

```bash
uv tool install ty                    # primary LSP
pip install basedpyright              # fallback for call hierarchy
uv tool install --editable cc-lsp-now # MCP bridge (or: pip install cc-lsp-now)
```

## More Information

- [ty Documentation](https://docs.astral.sh/ty/)
- [cc-lsp-now](https://github.com/holo-q/cc-lsp-now) — the LSP-to-MCP bridge
- [claude-code#40282](https://github.com/anthropics/claude-code/issues/40282) — tracking built-in LSP improvements
