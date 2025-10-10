[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/mezmo-mezmo-mcp-badge.png)](https://mseep.ai/app/mezmo-mezmo-mcp)

# Mezmo MCP Server (Model Context Protocol)

Mezmo MCP is a **remote Model Context Protocol (MCP) server** that lets AI assistants and IDE chat agents interact with the Mezmo observability platform via the [Model Context Protocol](https://modelcontextprotocol.info/). Use it for streamlined observability, log analysis, and root-cause analysis in your favorite tools.

Add Mezmo MCP and you can:

- 🕵️ Run advanced **Root-cause analysis** over recent logs
- 📦 List and describe **Pipelines**
- 📤 Export and filter **Logs** with powerful query syntax

---

## Table of Contents

- [Mezmo MCP Server (Model Context Protocol)](#mezmo-mcp-server-model-context-protocol)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Available Tools \& Examples](#available-tools--examples)
  - [Best Practices](#best-practices)
  - [Installation](#installation)
    - [Requirements](#requirements)
  - [Client Configurations](#client-configurations)
  - [Troubleshooting](#troubleshooting)
    - [1. `npx` argument-escaping bug](#1-npx-argument-escaping-bug)
    - [2. Lost connection to the Mezmo MCP server](#2-lost-connection-to-the-mezmo-mcp-server)
    - [3. 401/403 authentication errors](#3-401403-authentication-errors)
  - [Next Steps](#next-steps)

---

## Overview

Mezmo MCP is a remote MCP server that connects AI assistants and IDE chat to Mezmo so you can run advanced root-cause analysis, discover pipelines, and export logs without hosting anything yourself. It’s built for observability use cases and works across many popular MCP clients.

---


## Available Tools & Examples

- analyze_logs_for_root_cause_relative_time: analyze logs over a relative window
  - Example: `analyze my logs from the last 30 minutes and determine root cause for any issues that you find`
- analyze_logs_for_root_cause_time_range: analyze logs between specific timestamps
  - Example: `analyze errors between 2025-01-01T10:00:00Z and 2025-01-01T11:00:00Z`
- deduplicate_logs_relative_time: deduplicate similar logs over a relative window
  - Example: `deduplicate error logs from the last hour for app "checkout-service"`
- deduplicate_logs_time_range: deduplicate similar logs between specific timestamps
  - Example: `deduplicate logs from 2025-01-01T10:00:00Z to 2025-01-01T11:00:00Z`
- export_logs_relative_time: export raw logs over a relative window
  - Example: `export error logs from the last 30 minutes for app "my-app-frontend"`
- export_logs_time_range: export raw logs between specific timestamps
  - Example: `export logs from 2025-01-01T10:00:00Z to 2025-01-01T11:00:00Z with level error`
- list_pipelines: list all pipelines
  - Example: `list all my pipelines`
- get_pipeline: get details for a pipeline by ID
  - Example: `show me details for pipeline <pipeline id>`

---

## Best Practices

- Start broad for root-cause analysis
  - If the query is too narrow, our RCA can't do its thing. Cast a wide net, and if needed, specify app/service/level in subsequent queries.
- Prefer analyze_logs_for_root_cause or deduplicate_log tools for insights
  - These tools deduplicate and groups similar logs for better summaries, allowing the results to fit into finite LLM context windows.
- Use relative time ranges
  - Prefer values like `last_15_minutes`, `last_hour` when supported.
- Keep prompts simple; add filters gradually
  - Add `app`, `host`, `level`, and `query` filters step by step.
- Use export_logs for raw data only
  - For dashboards or offline analysis, use `export_logs`; otherwise prefer RCA.

---

## Installation

### Requirements

- A Mezmo **Service Key** (generate one in your Mezmo dashboard under Settings > API Keys; see [Mezmo docs](https://docs.mezmo.com/docs/ingestion-key#ingestion-and-service-keys) for details)
- Node.js ≥ 18 (only needed for clients that use the `mcp-remote` bridge)
- One of the supported MCP clients below

For every client we follow a simple rule:

1. **Supports remote URL? →** configure it with a `url` that points to `https://mcp.mezmo.com/mcp` and include the `Authorization` header.
2. **StdIO-only client? →** use the [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) bridge:

```bash
AUTH_HEADER="Bearer <SERVICE KEY>" npx mcp-remote https://mcp.mezmo.com/mcp \
  --header "Authorization:${AUTH_HEADER}"
```

---

## Client Configurations

Expand a section below to see setup instructions for your preferred editor or tool.

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Cursor</b></summary>

Cursor **natively supports remote MCP servers**, so you only need a remote configuration.

[![Install Mezmo MCP Server in Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=mezmo&config=eyJ1cmwiOiJodHRwczovL21jcC5tZXptby5jb20vbWNwIiwiaGVhZGVycyI6eyJBdXRob3JpemF0aW9uIjoiQmVhcmVyIDxTRVJWSUNFIEtFWT4ifX0=)

*Clicking the **Install MCP Server** badge opens Cursor and automatically adds the `mezmo` entry to your `~/.cursor/mcp.json` with a placeholder for the Service Key. After it’s created, edit the file and replace `<SERVICE KEY>` with your actual Mezmo service key. Restart Cursor for changes to take effect. The final configuration should look like the example below.*

```json
{
  "mcpServers": {
    "mezmo": {
      "url": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Windsurf</b></summary>

Windsurf also supports remote servers via the `serverUrl` field.

```json
{
  "mcpServers": {
    "mezmo": {
      "serverUrl": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Trae</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "url": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in VS Code</b></summary>

VS Code’s Copilot Chat supports remote MCP servers with HTTP transport.

```json
"mcp": {
  "servers": {
    "mezmo": {
      "type": "http",
      "url": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Visual Studio 2022</b></summary>

```json
{
  "mcp": {
    "servers": {
      "mezmo": {
        "type": "http",
        "url": "https://mcp.mezmo.com/mcp",
        "headers": {
          "Authorization": "Bearer <SERVICE KEY>"
        }
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Zed</b></summary>

Zed **does not support remote URLs** yet, so use the `mcp-remote` bridge.

```json
{
  "context_servers": {
    "Mezmo": {
      "command": {
        "path": "npx",
        "args": [
          "mcp-remote",
          "https://mcp.mezmo.com/mcp",
          "--header",
          "Authorization:${AUTH_HEADER}"
        ],
        "env": {
          "AUTH_HEADER": "Bearer <SERVICE KEY>"
        }
      },
      "settings": {}
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Gemini CLI</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Claude Code</b></summary>

Claude Code supports remote URLs:

```bash
claude mcp add --transport http mezmo https://mcp.mezmo.com/mcp \
  --header "Authorization: Bearer <SERVICE KEY>"
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Claude Desktop</b></summary>

```json
{
  "mcpServers": {
    "Mezmo": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in BoltAI</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install on Windows (cmd)</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Augment Code</b></summary>

Add a new MCP and enter:

```bash
AUTH_HEADER="Bearer <SERVICE KEY>" npx mcp-remote https://mcp.mezmo.com/mcp \
  --header "Authorization:${AUTH_HEADER}"
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Roo Code</b></summary>

Roo Code supports remote URLs:

```json
{
  "mcpServers": {
    "mezmo": {
      "type": "streamable-http",
      "url": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Zencoder</b></summary>

```json
{
  "command": "npx",
  "args": [
    "mcp-remote",
    "https://mcp.mezmo.com/mcp",
    "--header",
    "Authorization:${AUTH_HEADER}"
  ],
  "env": {
    "AUTH_HEADER": "Bearer <SERVICE KEY>"
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Amazon Q Developer CLI</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Qodo Gen</b></summary>

Qodo Gen supports remote URLs:

```json
{
  "mcpServers": {
    "mezmo": {
      "url": "https://mcp.mezmo.com/mcp",
      "headers": {
        "Authorization": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in JetBrains AI Assistant</b></summary>

```json
{
  "mcpServers": {
    "mezmo": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.mezmo.com/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <SERVICE KEY>"
      }
    }
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Warp</b></summary>

```json
{
  "Mezmo": {
    "command": "npx",
    "args": [
      "mcp-remote",
      "https://mcp.mezmo.com/mcp",
      "--header",
      "Authorization:${AUTH_HEADER}"
    ],
    "env": {
      "AUTH_HEADER": "Bearer <SERVICE KEY>"
    },
    "working_directory": null,
    "start_on_launch": true
  }
}
```

</details>

<!-- ------------------------------------------------------------------ -->
<details>
<summary><b>Install in Opencode</b></summary>

Opencode supports remote URLs:

```json
"mcp": {
  "mezmo": {
    "type": "remote",
    "url": "https://mcp.mezmo.com/mcp",
    "headers": {
      "Authorization": "Bearer <SERVICE KEY>"
    },
    "enabled": true
  }
}
```

</details>

---

## Troubleshooting

### 1. `npx` argument-escaping bug

Some clients pass command-line arguments to `npx` without quoting spaces. This can split the `Authorization` header (e.g. `Bearer` and the token become separate arguments) and cause authentication failures.

**Work-around:** store the header in an environment variable and pass it without spaces:

```json
{
  "command": "npx",
  "args": [
    "mcp-remote",
    "https://mcp.mezmo.com/mcp",
    "--header",
    "Authorization:${AUTH_HEADER}"
  ],
  "env": {
    "AUTH_HEADER": "Bearer <SERVICE KEY>"
  }
}
```

### 2. Lost connection to the Mezmo MCP server

If your client shows an error such as “server disconnected” or stops responding to MCP commands:

1. Disable or remove the **Mezmo** MCP entry in your client settings.
2. Re-enable (or re-add) the same entry, or simply restart the client.

This forces the client to establish a fresh connection to the Mezmo MCP backend.

### 3. 401/403 authentication errors

- Verify the `Authorization` header is present and formatted as `Bearer <SERVICE KEY>`.
- If using `npx mcp-remote`, prefer the environment variable approach to avoid splitting the header.
- Regenerate your Service Key in Mezmo and try again if issues persist.

---

## Next Steps

Once your client is configured you can immediately run natural-language commands such as

- `analyze my logs from the last 30 minutes and determine root cause for any issues that you find`
- `list all my pipelines`
- `show me details for pipeline <pipeline title>`
- `export error logs from the last 30 minutes for app "my-app-frontend"`

Enjoy streamlined observability with Mezmo + AI! 🎉
