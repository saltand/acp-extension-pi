# Pi MCP source switch

Status: implemented
Translation: current

[中文](2026-11-09-pi-mcp-source.zh.md)

## Decision

By default a session uses exactly the MCP servers Lody selected for it
([deferred MCP note](2026-11-06-deferred-mcp-tool-search.md)). Some users instead want
Lody's built-in `lody` server plus the MCP servers they already configured for Pi.
`LODY_PI_MCP_SOURCE=pi` selects that; the default `acp` is unchanged and any other value is
reported as a notice and treated as `acp`.

In `pi` mode `registerMcp` registers only the ACP server named `lody` (Lody's built-in one;
workspace servers cannot take that name) as `deferred`, and calls `createMcpExtension`
without `loadConfig`, so Pi reads `mcp.json` from its agent directory and from a trusted
project. A notice names how many workspace servers were ignored.

## Consequences

- Servers from `mcp.json` keep their own `exposure`. Pi's default is `codemode`, which also
  activates the codemode tool; write `"deferred"` to use `tool_search`.
- An `mcp.json` server named `lody` overrides the built-in one (Pi's precedence).
- OAuth credentials stored by `pi mcp login` in `mcp-auth.json` apply, so signed-in servers
  work without an in-session sign-in.
- The adapter does not validate or redact `mcp.json` servers; Pi does.
- Servers selected in Lody's UI are not used in this mode, which the notice states.

## Verification

Unit tests cover source parsing, the `lody` filter and both notices. The smoke script starts a
session with `LODY_PI_MCP_SOURCE=pi`, a workspace server, a `lody` server and an `mcp.json`
server, and asserts that only the last two are callable. Disabling the mode in `dist` makes
that assertion fail.
