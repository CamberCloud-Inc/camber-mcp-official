<!-- markdownlint-disable MD001 MD041 -->
<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://docs.cambercloud.com/img/Logo_white.png">
    <img alt="vLLM" src="https://docs.cambercloud.com/img/Logo_black.png" width=55%>
  </picture>
</p>

<h3 align="center">
Remote data science agents for Snowflake, Databricks & BigQuery in Claude/Cursor via MCP
</h3>

<p align="center">
| <a href="https://docs.cambercloud.com/docs/camber-mcp"><b>Documentation</b></a> | <a href="https://glama.ai/mcp/connectors/com.cambercloud.camber-mcp/camber-mcp-server"><b>Glama</b></a> | <a href="https://registry.modelcontextprotocol.io/?q=com.cambercloud.camber-mcp%2Fcamber-mcp-server"><b>Registry modelcontextprotocol</b></a> 
|
</p>

🔥 We host our MCP server at https://camber-mcp.cambercloud.com/mcp.

<!-- Join Slack here -->

# Camber MCP

<!-- ## Public Endpoints

- MCP documentation: https://docs.cambercloud.com/docs/camber-mcp
- MCP endpoint: https://camber-mcp.cambercloud.com/mcp
- Glama MCP Connector: [Camber - Remote Agentic Data Science](https://glama.ai/mcp/connectors/com.cambercloud.camber-mcp/camber-mcp-server)
- Registry modelcontextprotocol: [com.cambercloud.camber-mcp/camber-mcp-server]() -->


CamberCloud runs a remote **[Model Context Protocol (MCP)](https://modelcontextprotocol.io/)** server. Register it in your editor or CLI with your Camber API token, then use tools such as **`agents_chat`** to talk to [Camber agents](/docs/agents/) with the same **@mention** tags as in Nova.

The page has three parts: **[Set up Camber MCP](#set-up-camber-mcp)** (shared for every client), **[MCP clients](#mcp-clients)** (Claude Code CLI, Claude Code VS Code extension, and Cursor), and **[Camber MCP tools](#camber-mcp-tools)** (technical reference).

## Set up Camber MCP

{{< callout type="info" >}}
**Heads-up:** To use the full Camber environment through Camber MCP, you need the [Camber CLI](/docs/camber-cli/) on your computer. If you do not have it yet, Camber MCP setup will install the CLI for you. If you are not logged in to Camber, you will need to [log in](/docs/camber-cli/login/) as well (your [API key](#get-your-camber-api-key) in the MCP client is for the remote server). See [Installation](/docs/camber-cli/installation/) for details.
{{< /callout >}}

### Get your Camber API key

The value you pass as `<YOUR_CAMBER_API_KEY>` is your Camber **API token**:

1. Log in to [app.cambercloud.com](https://app.cambercloud.com/)
2. Open your [profile settings](https://app.cambercloud.com/user)
3. Copy your API token

For using that token with the Camber CLI (environment variable, one-off flags), see [Login — API token](/docs/camber-cli/login#method-2-using-an-api-token-via-environment-variable).

### Camber MCP server endpoint

Every client must use this **HTTP** MCP endpoint:

`https://camber-mcp.cambercloud.com/mcp`

Pair it with an **`Authorization`** header: `Bearer <YOUR_CAMBER_API_KEY>` (see above).

### Shared MCP JSON (reference)

Most clients accept a server entry named **`camber-mcp`** with the same fields. This is the shape **Claude Code** uses in a project **`.mcp.json`**; **Cursor** often uses in a project **`.cursor/mcp.json`**.

```json
{
  "mcpServers": {
    "camber-mcp": {
      "type": "http",
      "url": "https://camber-mcp.cambercloud.com/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_CAMBER_API_KEY>"
      }
    }
  }
}
```

Replace `<YOUR_CAMBER_API_KEY>` with your token from [Get your Camber API key](#get-your-camber-api-key).

## MCP clients

**[Claude Code](https://code.claude.com/docs/en/overview)** and **[Cursor](https://cursor.com/)** both connect to the same Camber MCP server. Complete [Set up Camber MCP](#set-up-camber-mcp) first, then follow the subsection for your client. Agent addressing uses the same **@mention** tag formats as in Nova.

### Claude Code

[Claude Code](https://code.claude.com/docs/en/overview) is Anthropic’s agentic CLI (and related editor flows). Install and sign in per Anthropic’s documentation, then register the Camber server.

{{< callout type="warning" >}}
**Claude Pro (or eligible plan) required:** Claude Code needs an active **[Claude Pro](https://claude.com/pricing)** subscription or another plan that includes Claude Code. CamberCloud does not replace Anthropic’s requirements.
{{< /callout >}}

**Global** (all projects on this machine):

```bash
claude mcp add --transport http camber-mcp \
  https://camber-mcp.cambercloud.com/mcp \
  --header "Authorization: Bearer <YOUR_CAMBER_API_KEY>"
```

**Project-only** (current repository):

```bash
claude mcp add --transport http camber-mcp \
  https://camber-mcp.cambercloud.com/mcp \
  --header "Authorization: Bearer <YOUR_CAMBER_API_KEY>" \
  --scope project
```

Alternatively, add a project **`.mcp.json`** using the [shared MCP JSON](#shared-mcp-json-reference) shape.

#### Use agents in Claude Code

In the Claude Code terminal, you can invoke Camber agents with the `camber` CLI pattern (your UI may show the **`agents_chat`** tool and JSON). Example:

```text
camber @username.agent_alias Your question here
```

Example: `camber @camberuser.titanic How many passengers?`

![Claude Code terminal: Camber MCP calling the Titanic agent via agents_chat](camber-mcp-titanic-demo.png)

### Claude Code for VS Code

If you use Claude Code through the **[VS Code extension](https://marketplace.visualstudio.com/items?itemName=Anthropic.claude-code)**, follow these steps:

1. Create a **`.mcp.json`** file in your project root with the [shared MCP JSON](#shared-mcp-json-reference) shape:

```json
{
  "mcpServers": {
    "camber-mcp": {
      "type": "http",
      "url": "https://camber-mcp.cambercloud.com/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_CAMBER_API_KEY>"
      }
    }
  }
}
```

2. Open a new Claude Code session or restart VS Code.
3. In the Claude chat panel, type **`/mcp`** and select **MCP servers**.
4. Click on **`camber-mcp`** and click **Enable**.

#### Use agents in the VS Code extension

Once connected, start a chat in the Claude Code panel. Address Camber agents the same way as in the terminal:

```text
camber @username.agent_alias Your question here
```

### Cursor

[Cursor](https://cursor.com/) connects to MCP servers from **Settings** and/or JSON config files. Cursor’s MCP UI and file locations change between releases—use **[Cursor’s MCP documentation](https://cursor.com/docs/mcp)** for the exact panel names on your version.

1. Open **Cursor Settings**.
2. Go to the **Tools & MCPs** section.
3. Select **Add a Custom MCP Server** button.

{{< callout type="info" >}}
**Tokens and Git:** Do not commit real API keys. Use a placeholder in committed `.cursor/mcp.json` and set your token locally, or rely on a private, gitignored override file if your team uses that pattern.
{{< /callout >}}

#### Use agents in Cursor

1. Open **Chat** or **Composer** (or **Agent** mode, if available).
2. Start a thread where **MCP tools** are allowed—Cursor should list tools from **`camber-mcp`** (for example **`agents_chat`**, **`agents_list`**).
3. Address your agent with the same tags as in Nova: `@<username>.<agent_alias>` or `@<teamname>.<agent_alias>`.

If tools do not appear, confirm the server is enabled in MCP settings, your token is valid, and your Cursor build supports HTTP MCP for remote URLs.

## Camber MCP tools

This section summarizes selected tools exposed by the **`camber-mcp`** server. Your MCP client lists the full tool set and parameters.

### `agents_chat` {#agents_chat}

Chat with a Camber agent by tag and message. Returns the agent’s reply as text.

| Parameter | Description |
| --- | --- |
| **`agent_tag`** | Tag of the agent to use. Include the leading `@`; format is `@<owner_name>.<agent_alias>` (for example, `@camber_user.ai-agent-1`). |
| **`message`** | The message to send to the agent. |
| **`model_id`** | Model name for the agent run (for example, `Haiku`, `Sonnet`). Default is `claude-sonnet-4-6`. |

Use **`agents_chat`** from **Claude Code** (for example via the `camber` CLI pattern shown above) and from **Cursor** when **`camber-mcp`** tools are available in Chat, Composer, or Agent mode.

### `agents_list` {#agents_list}

List [Camber agents](/docs/agents/) you can use, with optional **scope** and **pagination**. Returns agent metadata and page info (for example total count and page size).

| Parameter | Description |
| --- | --- |
| **`scope`** | Which agents to include: `community` (community agents), `system` (system agents), or `team` (agents for a specific team). Omit or leave unset to list **your personal** agents. |
| **`team_name`** | When **`scope`** is `team`, the team’s unique name. Ignored for other scopes. |
| **`page`** | Page number to fetch (default: **1**). |
| **`size`** | Agents per page (default: **20**). |
