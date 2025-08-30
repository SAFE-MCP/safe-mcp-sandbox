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
  1) Quit Claude Desktop completely.
  2) Open the config file above for your OS. If it doesn't exist, create it with `{}`.
  3) Paste the JSON snippet above and adjust the absolute flags path.
  4) Save the file. For safe mode, insert `"-e","MODE=safe"` before the image tag.
  5) Reopen Claude Desktop.
  6) Verify: open a chat and ask to list tools (or check the Tools panel). You should see `safe-mcp` with tools like `notes.search` and `fs.read`.

## Cursor

Cursor supports the same `mcpServers` structure. Use the UI in current versions (refer to official docs for exact labels): https://modelcontextprotocol.io/clients/cursor

Step-by-step (UI):
1) Open Cursor Settings.
2) Search for "Model Context Protocol" or "MCP".
3) Open the MCP Servers configuration (button like "Open MCP Servers").
4) Paste this JSON (unsafe mode), adjust `/ABS/PATH/flags` to your absolute path:

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

Verify:
- Open the MCP/Tools view and confirm `safe-mcp` appears with tools like `notes.search` and `fs.read`.
- Ask to list tools or run a benign query to confirm it responds.

Key points:
- Use an absolute host path in the `-v` mount.
- On Apple Silicon, include `--platform linux/amd64`.
- Use the safe mode variant to validate mitigation (`-e MODE=safe`).

## Troubleshooting

- “file not found”: create `./flags/flag.txt` in your current folder before running.
- Volume mount issues:
  - Use an absolute path on the host side of `-v`.
  - Ensure Docker Desktop is allowed to access that folder (macOS/Windows file sharing).
- JSON errors: validate your JSON; on Windows, escape backslashes in paths.
- Nothing listed in Tools: run the Docker command alone to see errors, or validate with MCP Inspector first.
