# AgentMart Site Check

Check a website's public setup from your coding agent. Give it a domain such as example.com. It reads what any
visitor or AI agent can see from the outside and lists each item as pass, warn, fail, info or not checked, with
plain steps to fix:

- HTTPS: whether the site answers over HTTPS, moves http visitors to HTTPS, and when its certificate expires
- security headers: Strict-Transport-Security, Content-Security-Policy, frame, content type and referrer headers,
  and security.txt
- email sender records: SPF and DMARC
- whether privacy, terms and contact pages can be found
- files for AI agents: llms.txt, robots.txt, MCP discovery files, and whether the site's MCP server answers and
  labels its tools

It is a small plugin with one skill and one remote MCP server. The server changes nothing on the sites it checks and needs no account and
no sign-in. It is made by Ciphyr Tech OÜ (Estonia), the company that runs AgentMart, and it runs on our servers.

## Use it

After you install it, ask your agent in plain words, for example:

- "Check example.com with AgentMart Site Check."
- "Does my domain have SPF and DMARC records?"
- "When does the HTTPS certificate of my website expire?"
- "Does my site publish llms.txt, and does its MCP server at https://example.com/mcp answer?"
- "Show me that site check again."

The skill `check-website-setup` tells the agent when to use the two tools, how to read the result, and when not
to use them.

## When not to use it

- It is not a security audit. It does not look for vulnerabilities, test logins or forms, or say anything about
  a company's servers, code, staff or how it handles data. A result describes that moment only.
- It checks public domain names only, not IP addresses, localhost or private networks.
- It is not for checking long lists of domains: each caller gets a limited number of fresh checks an hour, and
  each domain at most 4.

## Install

Tested by us: Claude Code (validation and an install from a local copy). The other lines follow each app's own
documentation and are not tested by us yet. If one fails, please open an issue.

| App | How |
|---|---|
| Claude Code | `claude plugin marketplace add mustafaakmese/agentmart-site-check`, then `claude plugin install agentmart-site-check@agentmart-site-check` |
| GitHub Copilot CLI | `copilot plugin install mustafaakmese/agentmart-site-check` |
| VS Code agent plugins (preview) | run "Chat: Install Plugin From Source" and paste `https://github.com/mustafaakmese/agentmart-site-check` |
| Google Antigravity | `agy plugin install https://github.com/mustafaakmese/agentmart-site-check` |
| Codex | `codex plugin marketplace add mustafaakmese/agentmart-site-check`, then `/plugins` |
| Any MCP client | add the remote server `https://agentmart.ciphyr.ai/mcp/check` (streamable HTTP, no sign-in) |

On claude.ai, Claude Desktop and Cowork, the same server is in Claude's directory as the AgentMart Site Check
connector: https://claude.ai/directory/connectors/agentmart-site-check

## What it runs, sends and fetches

- No code runs on your machine: no hooks, no scripts, no commands, no binaries, and no files that change your
  settings or rules.
- One remote MCP server, `https://agentmart.ciphyr.ai/mcp/check`, with two tools: `run_check` and
  `get_check_result`. Your app talks to it directly over HTTPS.
- What your agent sends to it: the domain to check, and an MCP address on that domain if you give one.
- What the server then reads: that site's public pages, headers and files, with at most 24 ordinary requests
  per check (one of them over plain http, to see whether the site moves visitors to HTTPS), and public DNS
  records. If the site names an MCP server, the check sends it the standard MCP opening messages, asks for its
  list of tools and ends the session. Its requests name themselves AgentMart-Check and follow the site's
  robots.txt rules for AgentMart-Check. It never logs in, fills in forms or changes anything on the site.
- What it keeps: the domain, the time and the result, for 7 days, so a result can be shown again by its
  check_id. A result is our own fixed wording and numbers; no text from the checked site is copied into it. For
  rate limits, request counters hold the caller's IP address for a short time. Details:
  https://agentmart.ciphyr.ai/privacy

Site owners can stop checks of their domain by others with a DNS TXT record `agentmart-check=no` on
`_agentmart-check.<their domain>`, or by writing to us.

## Files

| Path | Read by |
|---|---|
| `.claude-plugin/plugin.json`, `.mcp.json` | Claude Code and Claude's plugin directory |
| `.claude-plugin/marketplace.json` | Claude Code, Copilot CLI and VS Code: this repository as a marketplace with one plugin |
| `plugin.json`, `mcp.json` | Agent Plugins 1.0: VS Code, Copilot CLI and Codex (Antigravity also reads `plugin.json`) |
| `.agents/plugins/marketplace.json` | Codex: this repository as a marketplace with one plugin |
| `mcp_config.json` | Google Antigravity |
| `skills/check-website-setup/SKILL.md` | every app above |
| assets folder | the logo, as SVG and as a 512 by 512 PNG |
| `NOTES.md` | for maintainers: which document each manifest follows, the checks run, and what is not tested yet |

## Security

See `SECURITY.md`. To report a security problem: https://agentmart.ciphyr.ai/.well-known/security.txt

## License

The files in this repository (manifests, the skill, the documents) are under the MIT license in `LICENSE`. The
AgentMart name and logo are not licensed under MIT. The site check service itself is proprietary and run by
Ciphyr Tech OÜ under https://agentmart.ciphyr.ai/terms.

## Support

How the check works, in full: https://agentmart.ciphyr.ai/c. Questions: https://agentmart.ciphyr.ai/support
