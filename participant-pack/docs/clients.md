#!/usr/bin/env text
# MCP Clients (Examples)

- MCP Inspector (recommended for quick sanity checks)
  - macOS/Linux: `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -v "$(pwd)/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`
  - Windows PowerShell: `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -v "${PWD}/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

- Claude Desktop / Cursor: Add a custom MCP server using a Docker command similar to the above. Refer to each client's MCP docs for exact setup.

- Expected behavior:
  - In default (unsafe) mode, the tool description includes misleading guidance (for evaluation).
  - In safe mode (`-e MODE=safe`), the tool description is sanitized.
