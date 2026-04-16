# mcpier-catalog

[![validate](https://github.com/spranab/mcpier-catalog/actions/workflows/validate.yml/badge.svg)](https://github.com/spranab/mcpier-catalog/actions/workflows/validate.yml)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

Community catalog feeds for [Pier](https://github.com/spranab/mcpier) — a self-hosted MCP control plane.

Pier subscribes to three sources out of the box:

| Source | What | Authority |
|---|---|---|
| **[registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io)** | The official MCP Registry — hundreds of MCPs, reverse-DNS namespaces | ✓ **registry** (unforgeable per namespace) |
| **[mcpier-catalog](./catalog.json)** | Curated starter set (this repo) | ✓ **curated** (maintainer-reviewed in PR) |
| **[mcpier-community](./community.json)** | Third-party vendor integrations (this repo) | ✓ **curated** |

Plus any git repo with a `pier.yaml` at its root installs via `pier install-git <url>`.

## The two feeds in this repo

### `catalog.json` — curated starter set (10 entries)

The happy path for someone new to Pier. Anthropic reference servers + author-maintained MCPs. Tight vetting. Every entry gets used regularly.

- `filesystem`, `memory`, `sequential-thinking` (Node reference)
- `fetch`, `git`, `time` (Python reference)
- `playwright` (Microsoft — browser automation)
- `context7` (Upstash — live library docs)
- `brainstorm-mcp` (multi-model AI brainstorming)
- `yantrikdb` (persistent cognitive memory via SSE)

### `community.json` — third-party vendor integrations (13 entries)

Platform vendors and common integrations. Looser curation — each vendor maintains their own MCP upstream; this feed points at them.

- **SCM**: `github`, `gitlab`
- **Databases**: `postgres`, `sqlite`
- **Platforms**: `stripe`, `cloudflare`, `notion`, `sentry`, `figma`, `slack`
- **Maps + search**: `google-maps`, `brave-search`
- **Browsers**: `puppeteer`

Both feeds are opt-in per subscription. Disable one without affecting the other from the UI's **Sources** panel.

## Subscribe

Either file can be subscribed individually or both together:

```bash
# Both (Pier's default)
PIER_CATALOG_URLS="https://raw.githubusercontent.com/spranab/mcpier-catalog/main/catalog.json,\
https://raw.githubusercontent.com/spranab/mcpier-catalog/main/community.json"
```

Or run your own catalog — fork this repo, or build a `catalog.json` from scratch. No coordination required.

## How entries resolve to installable MCPs

Each catalog entry is a lightweight pointer. The actual install formula (runtime, package, secrets, env) lives in one of two places:

1. **Author-maintained `pier.yaml` in the MCP's own repo.** See [examples/pier.yaml](examples/pier.yaml) (Node) and [examples/pier-python.yaml](examples/pier-python.yaml) (Python). This is the preferred pattern — MCP authors self-describe and updates land without a catalog PR.

2. **Catalog-maintained formula in [`formulas/`](./formulas).** Used when the upstream MCP hasn't adopted Pier yet (e.g. the official `modelcontextprotocol/servers` monorepo, where each sub-package doesn't ship its own `pier.yaml`). Points a catalog entry's `formula_url` at `raw.githubusercontent.com/.../formulas/<name>.yaml`.

Example catalog entry pointing at an author-maintained formula:

```json
{
  "name": "brainstorm-mcp",
  "source": "github:spranab/brainstorm-mcp",
  "description": "Multi-model AI brainstorming — make GPT, Gemini, DeepSeek, and Claude debate in rounds.",
  "tags": ["ai", "llm", "node"],
  "homepage": "https://github.com/spranab/brainstorm-mcp",
  "verified": true
}
```

The `"verified": true` flag above is *self-declared by this catalog* — it means a maintainer reviewed the PR. For unforgeable authority, see the MCP Registry integration below.

## Authority model

The `"verified": true` flag in this repo's entries is **catalog-attested**: someone with merge rights reviewed the PR. It's a soft trust signal tied to this catalog's reputation.

The MCP Registry at [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io) has a **stronger** trust primitive: reverse-DNS namespaces (`com.stripe/mcp`, `io.github.<owner>/...`) are proved via GitHub OAuth or DNS TXT records at publish time. That's what Pier shows as the green `✓ registry` badge. Entries from this catalog show `✓ curated` in amber — accurate about what they are.

## Submit an MCP

If you're the MCP author:

1. Add a `pier.yaml` at your repo root ([examples/pier.yaml](examples/pier.yaml)).
2. Open a PR here adding an entry to `catalog.json` or `community.json`.
3. CI validates the JSON and checks your entry shape — see [`.github/workflows/validate.yml`](./.github/workflows/validate.yml).
4. Once merged, any Pier instance refreshing its catalog picks it up.

If the upstream MCP isn't yours and doesn't have a `pier.yaml`:

1. Add a formula to [`formulas/`](./formulas) describing how to install it.
2. Add an entry with `"formula_url": "https://raw.githubusercontent.com/spranab/mcpier-catalog/main/formulas/<name>.yaml"`.
3. Same PR review + CI.

## Repo layout

```
mcpier-catalog/
├── catalog.json          ← curated starter feed
├── community.json        ← third-party vendor feed
├── formulas/             ← catalog-maintained install formulas
│   ├── brainstorm-mcp.yaml
│   ├── mcp-server-time.yaml
│   └── ...
├── examples/             ← templates for MCP authors
│   ├── pier.yaml
│   └── pier-python.yaml
└── .github/workflows/
    └── validate.yml      ← CI validation on PRs
```

## License

MIT.
