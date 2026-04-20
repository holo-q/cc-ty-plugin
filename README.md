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

Three binaries must be reachable on `PATH`: `ty`, `basedpyright-langserver`, and `cc-lsp-now`. Install them via whichever channel fits your system.

### Cross-platform (recommended)

```bash
uv tool install ty
uv tool install basedpyright          # installs basedpyright-langserver
uv tool install cc-lsp-now            # MCP bridge
```

`uv tool install` puts each binary on `PATH` independently — no venv activation, no shell rc edits.

### Arch / CachyOS (AUR)

```bash
yay -S ty-bin                         # or: paru -S ty-bin
yay -S basedpyright-bin
yay -S cc-lsp-now-git                 # not yet published — use uv tool install for now
```

Arch users who want to swap basedpyright for pylance locally:

```bash
yay -S pylance-language-server        # AUR — local-machine use only
```

Then set `LSP_REPLACE="basedpyright-langserver=pylance-language-server"` in your user settings, not in the plugin config.

### Debian / Ubuntu

```bash
pipx install ty
pipx install basedpyright
pipx install cc-lsp-now
```

(`pipx` is the apt-friendly equivalent of `uv tool install`.)

### Fedora

```bash
uv tool install ty
uv tool install basedpyright
uv tool install cc-lsp-now
```

(No ty/basedpyright packages in dnf as of writing — `uv tool install` is the path.)

### macOS (Homebrew)

```bash
brew install uv
uv tool install ty
uv tool install basedpyright
uv tool install cc-lsp-now
```

### From source (contributors)

```bash
git clone https://github.com/holo-q/cc-lsp-now
uv tool install --editable cc-lsp-now
```

Edits to the local checkout are picked up on next MCP server spawn.

## More Information

- [ty Documentation](https://docs.astral.sh/ty/)
- [cc-lsp-now](https://github.com/holo-q/cc-lsp-now) — the LSP-to-MCP bridge
- [claude-code#40282](https://github.com/anthropics/claude-code/issues/40282) — tracking built-in LSP improvements
