# ty-lsp

Python language server plugin for Claude Code using [ty](https://github.com/astral-sh/ty) — an extremely fast type checker by Astral.

## How it works

The plugin runs a two-layer LSP setup:

1. **`lspServers` (built-in Claude Code LSP integration)** — registers ty so Claude Code's native machinery handles push diagnostics automatically (the `<new-diagnostics>` reminders after edits). This path is kept because the built-in LSP hooks into Claude's feedback loop in ways an MCP tool cannot.

2. **`mcpServers` (cc-lsp-now bridge)** — spawns ty as a subprocess and exposes the full LSP protocol as 15 MCP tools with symbol-name addressing, multi-target batching, provenance headers, and per-method fallback. [basedpyright](https://github.com/DetachHead/basedpyright) is configured as the fallback for operations ty doesn't yet implement (call hierarchy, `willRenameFiles`, etc.).

3. **PreToolUse hook** — denies Claude Code's built-in `LSP()` tool with a redirect banner pointing the model at the MCP tools. The built-in tool only exposes ~9 of 20+ LSP methods, and some (`workspaceSymbol`) are buggy enough to be actively misleading.

The bridge will be progressively phased out as Claude Code's built-in LSP implementation matures. Track progress at [claude-code#40282](https://github.com/anthropics/claude-code/issues/40282).

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

Plus: push diagnostics via the native `lspServers` integration, surfaced automatically after edits.

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
