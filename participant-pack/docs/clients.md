#!/usr/bin/env text
# MCP Clients

This page shows three ways to connect to the public image:
- Recommended client: Claude Desktop (GUI). Use MCP Inspector for quick validation.

Apple Silicon (M1/M2): add `--platform linux/amd64` to `docker run` or pulls may fail. On Intel/AMD hosts you can omit it.
- MCP Inspector (terminal, recommended for quick validation)
- Claude Desktop (GUI client)
- Cursor (IDE client)

Official docs:
- MCP Inspector: https://modelcontextprotocol.io/inspector
- Claude Desktop: https://modelcontextprotocol.io/clients/anthropic/claude_desktop
- Cursor: https://modelcontextprotocol.io/clients/cursor

All examples expect you have created a local `flags/flag.txt` and that Docker can access that folder.

## MCP Inspector

Run without installing (uses `npx`):

- macOS/Linux:
  `npx @modelcontextprotocol/inspector -- docker run --platform linux/amd64 --rm -i --network none -v "$(pwd)/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

- Windows PowerShell:
  `npx @modelcontextprotocol/inspector -- docker run --platform linux/amd64 --rm -i --network none -v "${PWD}/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

Safe mode variant (adds an env var before the image):

- macOS/Linux:
  `npx @modelcontextprotocol/inspector -- docker run --platform linux/amd64 --rm -i --network none -e MODE=safe -v "$(pwd)/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

- Windows PowerShell:
  `npx @modelcontextprotocol/inspector -- docker run --platform linux/amd64 --rm -i --network none -e MODE=safe -v "${PWD}/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

In the UI:
- Tools tab should list `notes.search` and `fs.read`.
- Call examples:
  - notes.search: `{ "query": "welcome" }`
  - fs.read: `{ "path": "/opt/flags/flag.txt" }`
- Expected behavior: unsafe mode includes extra “IMPORTANT…” text in the tool description; safe mode removes it.

Tips:
- `--` separates Inspector from the server command; everything after is the Docker command.
- If the volume mount fails, try an absolute path on the left side of `-v`.

## Claude Desktop (recommended)

Config file locations:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%AppData%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

Add or merge this JSON snippet (unsafe mode):

{
  "mcpServers": {
    "safe-mcp": {
      "command": "docker",
      "args": [
        "run","--platform","linux/amd64","--rm","-i",
        "--network","none",
        "-v","/ABS/PATH/flags:/opt/flags:ro",
        "ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08"
      ]
    }
  }
}

Safe mode: insert `"-e","MODE=safe",` before the image tag in `args`.

Notes:
- Replace `/ABS/PATH/flags` with your absolute folder path.
  - macOS/Linux example: `/Users/you/projects/safe-mcp/flags:/opt/flags:ro`
  - Windows JSON needs backslashes escaped: `C:\\Users\\you\\safe-mcp\\flags:/opt/flags:ro`
- Steps:
  1) Quit Claude Desktop.
  2) Edit the config file above and add the snippet.
  3) Save, then reopen Claude Desktop.
  4) Open a chat and ask to list tools (or check the Tools panel).

## Cursor

Cursor supports the same `mcpServers` structure. Use the exact snippet from Claude Desktop in Cursor’s MCP configuration (via its settings or config file). See the official Cursor docs for the latest configuration location and UI steps.

Key points:
- Use an absolute host path in the `-v` mount.
- Use the safe mode variant to validate mitigation (`-e MODE=safe`).

## Troubleshooting

- “file not found”: create `./flags/flag.txt` in your current folder before running.
- Volume mount issues:
  - Use an absolute path on the host side of `-v`.
  - Ensure Docker Desktop is allowed to access that folder (macOS/Windows file sharing).
- JSON errors: validate your JSON; on Windows, escape backslashes in paths.
- Nothing listed in Tools: run the Docker command alone to see errors, or validate with MCP Inspector first.
