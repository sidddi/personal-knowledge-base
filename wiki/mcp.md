---
title: MCP (Model Context Protocol)
topic: MCP
subtopic: architecture
status: stable
last_updated: 2026-04-20
sources:
  - https://modelcontextprotocol.io
  - https://github.com/github/github-mcp-server
related:
  - wiki/ai-agents.md
  - wiki/mcp-scalability.md
---

## TL;DR
Communication standard between an LLM client and a server that exposes tools, resources, and prompts. Eliminates the need to write manual JSON schemas for each tool. MCP tools are reusable across any compatible client.

## What It Is
A protocol that separates:
- **Who defines the tools** → the MCP server (once)
- **Who calls them** → the client / host (any compatible application)

Complements tool use: MCP defines *who does the work*, tool use is the *calling mechanism*.

**MCP vs MCP Server** — important distinction:
- **MCP** is the protocol/standard itself: the specification for how AI systems communicate with data sources and external tools.
- **MCP Server** is a concrete implementation that exposes specific functionality following the protocol. Each server provides a set of tools the model can use.

**Analogy**: MCP is like HTTP (the protocol); MCP Servers are like specific web servers; tools are the endpoints/routes each server exposes.

## How It Works

### Full system hierarchy (top to bottom)

```
1. Host/Client System    (Claude Desktop, VS Code, custom application)
2. AI Agent              (the model — Claude, GPT — decides which tools to use)
3. MCP Protocol          (standard communication layer)
4. MCP Servers           (concrete implementations):
     ├── Filesystem MCP Server
     ├── Database MCP Server (PostgreSQL, SQLite)
     ├── API MCP Server (GitHub, Slack, Google Drive)
     └── Custom MCP Server
5. Tools                 (specific functions each server exposes)
6. Resources/Data        (files, databases, final external APIs)
```

### Architecture: 3 layers (client-server perspective)

```
Host (Claude Desktop / VS Code / Claude Code)
  └── Client (communication layer)
        └── MCP Server (exposes tools, resources, prompts)
```

### The 3 server primitives

| Primitive | Controlled by | Function |
|-----------|--------------|----------|
| **Tools** | The model | Claude decides when to call them. Additional capabilities: run code, query API, read/write files |
| **Resources** | The application | The app decides when to use them. Brings data into context: list documents, inject content into prompt |
| **Prompts** | The user | The user decides (button, slash command). Executes predefined and optimized workflows |

### Communication flow
```
1. Client → Server: list_tools
2. Server → Client: list of available tools
3. Claude decides to use a tool
4. Client → Server: call_tool(name, arguments)
5. Server executes and returns result
6. Claude formulates the final response
```

### vs. Manual tool use

| | Without MCP | With MCP |
|--|-------------|---------|
| Schema | Write JSON by hand | SDK auto-generates with `@mcp.tool` |
| Reusability | Per project | Any compatible client |
| Maintenance | Every change = rewrite schema | Change the server, all clients update |

## Practical Implications
- MCP is not magic: the server must exist and be well maintained
- MCP server security is critical — it is the attack surface
- Third-party MCP servers: audit them before connecting (access to sensitive data)

### Real use cases
- **Sentry MCP**: Claude accesses production errors in real time
- **Jira MCP**: Claude reads tickets directly
- **Slack MCP**: Claude sends notifications on task completion
- **GitHub MCP** (`ghcr.io/github/github-mcp-server`): access to repositories, read/write files

### Deploying GitHub MCP on a VPS (Docker pattern)
```bash
docker run -d \
  --name github-mcp \
  --restart unless-stopped \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=${GITHUB_PAT} \
  ghcr.io/github/github-mcp-server
```
Recommended PAT: Fine-grained token, `Contents: Read and Write` on the specific repo (minimum necessary).

## At Work / Domain Context
MCP is rapidly becoming the standard for connecting LLMs to enterprise systems. Key considerations:
- An MCP server that accesses internal databases — who audits it?
- How to manage OAuth authentication in MCP servers for employees?
- Copilot Studio doesn't speak MCP natively (uses Power Platform connectors), but the ecosystem is converging.

## Open Questions
- State of MCP adoption in the Microsoft ecosystem (Azure AI, Copilot Studio)?
- How to deploy an internal MCP server to connect legacy systems?
- Security model for multi-tenant MCP servers?

## Sources
- **modelcontextprotocol.io**: official spec. Architecture, 3 primitives, communication flow, SDK documentation.
- **github/github-mcp-server**: reference implementation. Docker deployment, PAT scopes, available tools.
