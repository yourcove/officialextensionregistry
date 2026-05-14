# Cove Official Extension Registry

This repository serves as the official extension registry for [Cove](https://github.com/yourcove/cove). It contains metadata for all officially published extensions that can be discovered and installed through the Cove UI.

## Structure

```
index.json                              # Master index of all extensions
extensions/
  {extension-id}.json                   # Canonical extension metadata + version history
```

## How It Works

1. The Cove application fetches `index.json` from this repository's `main` branch via GitHub raw content URLs
2. Users can browse and search extensions in Settings → Extensions → Find and Install Extensions
3. Cove resolves extension summary/version data from each `extensions/{extension-id}.json` file
4. When a user installs an extension, Cove downloads the release ZIP from the extension's own GitHub repository
5. Extensions are extracted to the local extensions directory and loaded automatically

## Adding an Extension

### For Extension Authors

1. **Build your extension** following the [Cove Extension Development Guide](https://github.com/yourcove/cove/blob/main/docs/ARCHITECTURE.md)
2. **Create GitHub releases** with ZIP packages named `{extension-id}-{version}.zip`
3. **Submit a PR** to this repository adding your extension metadata:
  - Create `extensions/{your-extension-id}.json` as the source of truth
4. **Do not hand-write generated release fields.** CI computes `versions[].checksum` from `downloadUrl` during PR validation and stamps missing `versions[].releasedAt` when the PR merges to `main`.

`index.json` is generated/synchronized by CI as an ID-only list. Do not manually duplicate version/summary fields there.

### Extension Entry Schema

```json
{
  "id": "com.example.my-extension",
  "sourceManifestUrl": "https://raw.githubusercontent.com/your-org/your-repo/main/extensions/MyExtension/extension.json",
  "name": "My Extension",
  "description": "What this extension does.",
  "author": "Your Name",
  "repositoryUrl": "https://github.com/your-org/your-repo",
  "categories": ["ui", "analytics"],
  "dependencies": {},
  "screenshots": [],
  "versions": [
    {
      "version": "1.0.0",
      "changelog": "Initial release",
      "minCoveVersion": "1.0.0",
      "downloadUrl": "https://github.com/your-org/your-repo/releases/download/v1.0.0/com.example.my-extension-1.0.0.zip"
    }
  ]
}
```

## Consolidation Rules

- `extensions/{extension-id}.json` is the canonical source for extension identity and summary.
- `versions[]` is the canonical source for version-specific data (`version`, `releasedAt`, `changelog`, `downloadUrl`, `checksum`, `minCoveVersion` where needed).
- Top-level release fields like `version`, `minCoveVersion`, `releasedAt`, `checksum`, `downloadUrl`, and `updatedAt` are not allowed.
- CI can sync metadata summary fields from `sourceManifestUrl` to reduce duplication with extension source repos.

## Source Repo De-duplication

- `sourceManifestUrl` can point at the extension repo's `extension.json` so name/description/author/categories/minCoveVersion stay aligned automatically.
- This keeps registry metadata thin while preserving support for direct URL installs (which still read the extension package manifest directly).

## Registry Maintenance Rules

- `extensions/{extension-id}.json` is the canonical source for `name`, `description`, `author`, `repositoryUrl`, `categories`, and `versions[]`.
- `minCoveVersion` lives on each `versions[]` entry because compatibility can change between extension releases.
- CI computes `versions[].checksum` from `versions[].downloadUrl` and writes it back to the PR branch when possible.
- CI sets missing `versions[].releasedAt` on merge to `main`.
- CI syncs the ID-only `index.json`, so maintainers do not manually duplicate summary/version fields there.

### Extension ID Convention

Use reverse domain notation: `com.{org}.{extension-name}`

Official extensions use `cove.official.{name}`.

### Categories

Use lowercase kebab-case categories. Common categories: `ui`, `ai`, `scraper`, `downloader`, `metadata`, `metadata-consumer`, `integration`, `automation`, `content-management`, `media-player`, `library`, `theme`, `layout`, `analytics`, `tools`, `search`, `import`, `export`, `notification`, `security`.

Scrapers and downloaders are ordinary extensions in this registry. Do not create separate scraper or downloader registries.

## Repo Patterns for Extension Authors

Extensions can be organized in two ways:

### Single-Extension Repository
One repository per extension. Simpler CI, clear ownership. Use the Cove single-extension template.

### Multi-Extension Repository
Multiple extensions in one repository. Use this for official Cove-provided packs that share CI or tests. `cove-extensions-ui` is the current official multi-extension repo, but its long-term successor should be named for all official extensions, not only UI extensions.

## License

MIT
