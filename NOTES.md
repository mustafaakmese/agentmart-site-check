# Notes for maintainers

Which document each file follows, the choices behind them, the checks that ran, and what is not tested yet.
Every vendor page below was read on 23 October 2026.

## One plugin, one server, every app

All three MCP files name one server, `agentmart-site-check`, at `https://agentmart.ciphyr.ai/mcp/check`. That is
the same address as the AgentMart Site Check connector in Claude's directory. Anthropic asks for the same URL in a
plugin and its connector, so people who have both see one set of tools.

| File | App | Format it follows | Source |
|---|---|---|---|
| `.claude-plugin/plugin.json` | Claude Code, Claude's plugin directory | Claude Code plugin manifest; the directory listing fields `icon`, `documentationUrl`, `supportUrl`, `privacyPolicyUrl`, `termsOfServiceUrl` | https://code.claude.com/docs/en/plugins-reference |
| `.mcp.json` | Claude Code | remote server with `type` `http` and an https `url` | https://code.claude.com/docs/en/plugins-reference and https://claude.com/docs/plugins/build |
| `.claude-plugin/marketplace.json` | Claude Code; Copilot CLI and VS Code also look here | one entry, `source` `./` (the plugin is the repository root), same name as the manifest | https://code.claude.com/docs/en/plugin-marketplaces, https://code.claude.com/docs/en/plugins/marketplace-reference.md and https://code.claude.com/docs/en/plugins/publish.md |
| `plugin.json` | VS Code, Copilot CLI, Codex; Antigravity also reads it | Agent Plugins 1.0.0 (closed schema), plus `extensions.com.openai.interface` for Codex | https://agent-plugins.org/specification, https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference, https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference, https://developers.openai.com/codex/plugins/build |
| `mcp.json` | VS Code, Copilot CLI, Codex | Agent Plugins 1.0.0 MCP file: `type` `streamable-http`, `url` | https://agent-plugins.org/specification |
| `.agents/plugins/marketplace.json` | Codex | one entry, local `path` `./`, `policy` and `category` on the entry | https://developers.openai.com/codex/plugins/build |
| `mcp_config.json` | Google Antigravity | remote server as `serverUrl` (no `type`, no `httpUrl`) | https://antigravity.google/docs/mcp and https://antigravity.google/docs/plugins |
| `skills/check-website-setup/SKILL.md` | every app above | Agent Skills: `name` matches the folder, `description` at most 1,024 characters | https://agentskills.io/specification |

## Choices

- **Codex listing fields** in `plugin.json` keep OpenAI's submission limits: display name and short description
  at most 30 characters, long description at most 4,000, at most 3 starter prompts of at most 128 characters,
  capabilities at most 20 of at most 120 characters, a square logo of at least 48 by 48 pixels
  (https://developers.openai.com/plugins/deploy/submission). The logo is the 512 by 512 PNG.
- **No `extensions` inside a server entry of `mcp.json`.** Codex documents per-server sign-in settings there, but
  the published Agent Plugins 1.0.0 MCP schema allows only `type`, `url` and `headers` in a remote server entry,
  and VS Code and Copilot CLI read the same file. This server needs no sign-in, so nothing is lost.
- **No `displayName` or `logo` for Antigravity.** Antigravity's own schema has them, but the shared `plugin.json`
  must also match the closed Agent Plugins schema. Antigravity shows the name `agentmart-site-check`.
- **No `.antigravity-plugin/` folder.** Antigravity's schema mentions it, but its plugin docs do not say which
  manifest wins when both exist.
- **No `.codex-plugin/`, `.cursor-plugin/`, `.devin-plugin/` or `gemini-extension.json`.** Codex prefers the root
  `plugin.json`; the other apps are not shelves for this plugin today.
- **Nothing that loads into every session or runs code:** no `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `rules/`,
  `hooks/`, `hooks.json`, `commands/`, `agents/` or `bin/`. A top-level `bin/` would also stop claude.ai and
  Cowork from installing the plugin.
- **README and SECURITY.md** describe everything the plugin sends and fetches, as Claude's directory scan asks.
  Image paths are never written in backticks or code blocks (a rule of Claude's directory validation).
- **Neutral wording** for awesome-copilot: no prices, no sales or signup links, the affiliation stated once.
  Rules: https://raw.githubusercontent.com/github/awesome-copilot/main/CONTRIBUTING.md and
  https://github.com/github/awesome-copilot/discussions/968

## Checks run on 23 October 2026

- `claude plugin validate` (Claude Code 2.1.270) on the repository: passes, with 5 warnings, one for each
  directory listing field. Claude Code's manifest reference says version 2.1.281 and later no longer report
  these fields, and that earlier versions do, so a `--strict` run on 2.1.270 fails on them alone.
- `claude plugin validate --strict` on a copy without those 5 fields: the marketplace, the plugin manifest and the
  skills all pass with no warning.
- Install from a local copy in an empty home folder: `claude plugin marketplace add`, then
  `claude plugin install agentmart-site-check@agentmart-site-check`. `claude plugin details` lists 1 skill and
  1 MCP server, about 176 tokens always loaded. `claude mcp list` shows the plugin's server as Connected.
- JSON Schema checks: `plugin.json` against the Agent Plugins 1.0.0 plugin schema and Antigravity's v1 plugin
  schema (https://antigravity.google/schemas/v1/plugin.json); `mcp.json` against the Agent Plugins 1.0.0 MCP
  schema. All pass.
- Our own checks: the same name, version, description, author, license and keywords in both plugin manifests; one
  server address in all three MCP files; the tool names, titles and arguments in the skill equal the server's
  published tools; every agentmart.ciphyr.ai address answers; no long dashes, prices, secrets or shop links;
  safe file names and sizes; a README of at least 40 words.

## Not tested yet

- Copilot CLI: `copilot plugin install` from the repository.
- awesome-copilot's own checks: `vally lint` and its Copilot CLI install test, which run on submission.
- VS Code: "Chat: Install Plugin From Source".
- Antigravity: `agy plugin install` from the repository and `agy plugin validate`. The `agy` command is not on
  the machine these notes were written on.
- Codex: `codex plugin marketplace add` with an entry path of `./`, and how it shows the PNG logo.
- Claude's directory: the portal's Validate step and its security scan.

## Releasing a new version

Raise `version` in `.claude-plugin/plugin.json` and `plugin.json` together (Claude Code keeps users on a version
until it changes), then tag the commit `v` plus that version. awesome-copilot reviews an immutable tag or a full
40-character commit SHA.
