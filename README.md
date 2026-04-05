# repo-scout-skill

A Claude Code skill that performs comprehensive GitHub repo analysis — architecture, security scanning, strategic value assessment, competitor discovery, and optional Traditional Chinese audio review.

## Features

- **Architecture analysis** via DeepWiki MCP (no clone needed for overview)
- **OWASP Top 10 security scan** with file path, line number, and fix suggestions
- **5-dimension strategic value assessment** — cost savings, efficiency, new projects, revenue, community impact
- **Competitor discovery** — auto-find similar repos by stars (`--discover`)
- **Traditional Chinese audio summary** via NotebookLM (`--audio`)
- Reports written in **Traditional Chinese (繁體中文)**

## Installation

```bash
claude skills add https://github.com/bingjyun/repo-scout-skill
```

## Dependencies

| Dependency | Required | Purpose | Install |
|---|---|---|---|
| DeepWiki MCP | **Yes** | Repo architecture overview without cloning | `claude mcp add deepwiki -- npx @anthropic-ai/deepwiki-mcp@latest` |
| NotebookLM MCP | Only for `--audio` | Generate Traditional Chinese audio review | `claude mcp add notebooklm -- npx notebooklm-mcp@latest` |
| Trail of Bits skills | No | Enhanced security scanning (skill works without it) | `claude plugin marketplace add trailofbits/skills` |

## Usage

```
/repo-scout <url-or-path> [url2] [url3] [--discover] [--audio]
```

**Examples:**

```bash
# Single repo full analysis
/repo-scout https://github.com/user/repo

# Compare two repos
/repo-scout https://github.com/a https://github.com/b

# Auto-discover similar repos
/repo-scout https://github.com/user/repo --discover

# Include Traditional Chinese audio overview
/repo-scout https://github.com/user/repo --audio

# Full power mode
/repo-scout https://github.com/user/repo --discover --audio
```

## Output

- **Single repo**: `<repo-name>-scout-report.md` — full analysis in Traditional Chinese
- **Multi-repo**: `<category>-comparison-report.md` — comparison matrix
- **With `--audio`**: `<repo-name>-audio-review.mp3` — podcast-style audio summary

## License

MIT
