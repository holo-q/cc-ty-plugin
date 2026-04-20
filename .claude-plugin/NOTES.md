# ty-lsp plugin.json — ephemerality notes

Notes that would be inline comments if JSON allowed them.

## Architecture

**Dual-layer LSP.** `lspServers` gives Claude Code native push-diagnostics via ty (the `<new-diagnostics>` reminders). `mcpServers` gives Claude the full LSP protocol via cc-lsp-now, a standalone MCP adapter. The PreToolUse hook denies the built-in `LSP()` tool because it's incomplete and sometimes actively misleading (e.g. `workspaceSymbol` returns 0 results even when the server supports it).

## Phase-out plan

This plugin exists because Claude Code's built-in LSP only exposes ~9 of 20+ LSP methods and is buggy on some of them. As Anthropic closes the gap (see [claude-code#40282](https://github.com/anthropics/claude-code/issues/40282)), the plugin will shrink back toward pure `lspServers`. Track that issue before assuming any workaround here is permanent.

## Ephemeral config (delete when upstream improves)

- **`LSP_PREFER` entries for `callHierarchy` and `willRenameFiles`** — ty doesn't implement these as of 2026-04. When ty ships them, delete the `PREFER` entries so calls land on ty (faster, in-process incremental).
- **basedpyright fallback in `LSP_SERVERS`** — once ty covers everything we use, drop the fallback entirely.
- **The `PreToolUse` LSP redirect hook** — remove when Claude Code's built-in `LSP()` tool reaches parity and reliability with the MCP tools.

## Version coupling

The `LSP_SERVERS`/`LSP_PREFER`/`LSP_REPLACE` env syntax requires cc-lsp-now >= 2.1.0. Older versions used `LSP_COMMAND`/`LSP_FALLBACK_COMMAND` (still accepted by the bridge as a legacy code path).

## uvx from git

The MCP server and redirect hook are invoked via `uvx --from git+https://github.com/holo-q/cc-lsp-now ...`. This means users don't install cc-lsp-now manually — uvx fetches and caches it on first spawn. When cc-lsp-now lands on PyPI, the `--from git+...` prefix can be dropped.

## Matcher safety

The `PreToolUse` matcher `"LSP"` is an exact string match (because it contains only letters/digits/underscore). It does not collide with MCP tool names like `mcp__plugin_ty-lsp_ty-lsp-extended__lsp_hover` — those are fully qualified and the lowercase `lsp` substring is irrelevant to the exact-match comparison.
