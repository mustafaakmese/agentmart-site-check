# Security

This repository holds manifests, one skill (Markdown), documents and a logo. It runs no code on your machine:
no hooks, no scripts, no commands, no binaries. The one server it names is
`https://agentmart.ciphyr.ai/mcp/check`, reached over HTTPS with no sign-in. Its two tools only read.

## Reporting a problem

Please report a security problem in this plugin or in the site check service as
https://agentmart.ciphyr.ai/.well-known/security.txt says. Do not open a public issue for it.

## What we never put here

- No tokens, keys or credentials of any kind. The server needs none.
- Nothing here tells the agent to fetch and follow text from a web address. (Like any MCP server, ours sends
  its tool descriptions when your app connects.)
- No files that change your settings, `CLAUDE.md`, `AGENTS.md` or other rules on your machine.
