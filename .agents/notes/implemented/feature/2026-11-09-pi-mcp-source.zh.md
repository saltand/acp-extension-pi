# Pi MCP 来源开关

状态：已实现
翻译：最新

[English](2026-11-09-pi-mcp-source.md)

## 决策

默认情况下，会话只使用 Lody 为它选择的 MCP 服务器（见
[deferred MCP 笔记](2026-11-06-deferred-mcp-tool-search.zh.md)）。有些用户想要
Lody 内置的 `lody` 服务器，再加上自己已经给 Pi 配置的 MCP。`LODY_PI_MCP_SOURCE=pi`
提供这个选项；默认值 `acp` 不变，其他取值会发出提示并按 `acp` 处理。

`pi` 模式下，`registerMcp` 只注册名为 `lody` 的 ACP 服务器（Lody 内置，workspace
服务器不能使用这个名字），仍为 `deferred`；并且调用 `createMcpExtension` 时不传
`loadConfig`，因此 Pi 会读取 agent 目录以及受信任项目里的 `mcp.json`。会有一条提示说明
忽略了多少个 workspace 服务器。

## 影响

- `mcp.json` 里的服务器保持各自的 `exposure`。Pi 默认是 `codemode`，同时会激活 codemode
  工具；想用 `tool_search` 就写 `"deferred"`。
- `mcp.json` 里叫 `lody` 的服务器会覆盖内置的那个（Pi 的优先级规则）。
- `pi mcp login` 写入 `mcp-auth.json` 的 OAuth 凭据会生效，已登录的服务器无需在会话里再登录。
- 适配器不校验也不脱敏 `mcp.json` 的服务器，由 Pi 负责。
- 在 Lody 界面里选中的服务器在该模式下不会使用，提示里会说明。

## 验证

单测覆盖来源解析、`lody` 过滤和两种提示。smoke 用 `LODY_PI_MCP_SOURCE=pi` 启动会话，
带一个 workspace 服务器、一个 `lody` 服务器和一个 `mcp.json` 服务器，断言只有后两个可调用。
在 `dist` 里关掉该模式，这条断言会失败。
