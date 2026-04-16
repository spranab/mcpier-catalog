# mcpier-official catalog

The official community catalog for [Pier](https://github.com/spranab/mcpier) — a self-hosted MCP control plane.

This is a **curated starter set**, not a complete directory. The catalog holds ~10 well-chosen MCPs so you have something to explore on day one. For everything else, Pier supports **install from git** — paste any repo URL (or raw `pier.yaml` URL) into the Browse tab and it's one click away. The catalog is a starting point, not a gate.

Each entry is either a pointer to an MCP repo that ships its own `pier.yaml`, or a pointer to a formula hosted in this repo's [`formulas/`](./formulas) directory (useful for upstream projects that haven't adopted Pier yet).

## How it works

- `catalog.json` is an **index only**. It lists available MCPs with their source repos.
- The actual **install formula** (`pier.yaml`) lives inside each MCP's own repo, maintained by the MCP author. This way authors self-describe without catalog PRs.
- Pier servers fetch `catalog.json` from this repo's raw URL and merge entries into their Browse tab.

## Submit an MCP

1. Add a `pier.yaml` to the root of your MCP's repo. See [examples/pier.yaml](examples/pier.yaml).
2. Open a PR against this repo adding an entry to `catalog.json`:
   ```json
   {
     "name": "your-mcp",
     "source": "github:your-handle/your-mcp",
     "description": "One-line description.",
     "tags": ["relevant", "tags"],
     "homepage": "https://github.com/your-handle/your-mcp",
     "verified": true
   }
   ```
3. CI will validate that your `pier.yaml` parses against the Pier formula schema.
4. On merge, any Pier server refreshing its catalog will see your entry.

## Catalog subscription (free, client-side only)

Pier users point their instance at any catalog URL via `PIER_CATALOG_URLS` (comma-separated). The default is this repo's `catalog.json`. Anyone can run their own catalog — forks, company-internal, topic-specific — with no coordination.

## Trust model

- Entries with `"verified": true` reviewed by a maintainer in PR — green badge in the UI.
- Pier also allows **install from git** (paste a repo URL) — marked unverified, requires explicit user confirm.
- No package signing yet. Add sigstore later if supply-chain risk becomes real.

## License

MIT.
