# SAFE MCP Hackathon

Welcome! This site is for participants. It collects quick-starts, client setup, and tips for running the public image safely.

## Quick Start

- Recommended: MCP Inspector
  - macOS/Linux:
    `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -v "$(pwd)/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`
  - Windows PowerShell:
    `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -v "${PWD}/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

- Safe mode variant (adds env var before image):
  - macOS/Linux:
    `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -e MODE=safe -v "$(pwd)/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`
  - Windows PowerShell:
    `npx @modelcontextprotocol/inspector -- docker run --rm -i --network none -e MODE=safe -v "${PWD}/flags:/opt/flags:ro" ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

Make sure a local `./flags/flag.txt` exists before running.

## Clients

- MCP Inspector: see commands above.
- Claude Desktop & Cursor: see [clients.md](clients.md) for JSON config and tips.

## Scripts

- Shell: `scripts/run-unsafe.sh` and `scripts/run-safe.sh`
- Windows: `scripts/run-unsafe.cmd` and `scripts/run-safe.cmd`

## Image

- Public alias: `ghcr.io/bishnubista/safe-mcp-hackathon:hackathon-2025-08`

## Troubleshooting

- “file not found”: create `./flags/flag.txt` in your current folder.
- Volume mount issues: try an absolute path on the host side of `-v`.
- On Windows JSON, escape backslashes in paths.
