# Cove Official Extension Registry

This repository serves as the official extension registry for [Cove](https://github.com/yourcove/cove). It contains metadata for all officially published extensions that can be discovered and installed through the Cove UI.

## Structure

```
index.json                              # Master index of all extensions
extensions/
  {extension-id}/
    metadata.json                       # Canonical extension metadata + version history
    README.md                           # Extension documentation
    icon.png                            # Optional icon (128x128 recommended)
```

## How It Works

1. The Cove application fetches `index.json` from this repository's `main` branch via GitHub raw content URLs
2. Users can browse and search extensions in Settings → Extensions → Find and Install Extensions
3. Cove resolves extension summary/version data from each extension's `metadata.json`
4. When a user installs an extension, Cove downloads the release ZIP from the extension's own GitHub repository
5. Extensions are extracted to the local extensions directory and loaded automatically

## Adding an Extension

### For Extension Authors

1. **Build your extension** following the [Cove Extension Development Guide](https://github.com/yourcove/cove/blob/main/docs/ARCHITECTURE.md)
2. **Create GitHub releases** with ZIP packages named `{extension-id}-{version}.zip`
3. **Submit a PR** to this repository adding your extension metadata:
  - Create `extensions/{your-extension-id}/metadata.json` as the source of truth
   - Create `extensions/{your-extension-id}/README.md`
4. **Include SHA-256 checksums** in every `versions[].checksum` (`sha256:<64-hex>` or plain 64-hex)
  - PR validation is strict: CI fails if the release asset is unreachable, hash cannot be computed, or checksum mismatches.

`index.json` is generated/synchronized by CI as an ID-only list. Do not manually duplicate version/summary fields there.

### metadata.json Schema

```json
{
  "id": "com.example.my-extension",
  "sourceManifestUrl": "https://raw.githubusercontent.com/your-org/your-repo/main/extensions/MyExtension/extension.json",
  "name": "My Extension",
  "description": "What this extension does.",
  "author": "Your Name",
  "url": "https://github.com/your-org/your-repo",
  "repositoryUrl": "https://github.com/your-org/your-repo",
  "categories": ["UI", "Analytics"],
  "dependencies": {},
  "screenshots": [],
  "versions": [
    {
      "version": "1.0.0",
      "releasedAt": "2026-01-01T00:00:00Z",
      "changelog": "Initial release",
      "checksum": "sha256:0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef",
      "downloadUrl": "https://github.com/your-org/your-repo/releases/download/v1.0.0/com.example.my-extension-1.0.0.zip"
    }
  ]
}
```

## Consolidation Rules

- `metadata.json` is the canonical source for extension identity and summary.
- `versions[]` is the canonical source for version-specific data (`version`, `releasedAt`, `changelog`, `downloadUrl`, `checksum`, `minCoveVersion` where needed).
- Top-level metadata fields like `version`, `updatedAt`, and top-level changelog are not required.
- CI can sync metadata summary fields from `sourceManifestUrl` to reduce duplication with extension source repos.

## Source Repo De-duplication

- `sourceManifestUrl` can point at the extension repo's `extension.json` so name/description/author/categories/minCoveVersion stay aligned automatically.
- This keeps registry metadata thin while preserving support for direct URL installs (which still read the extension package manifest directly).

## Registry Maintenance Rules

- `metadata.json` is the canonical source for `name`, `version`, `description`, `author`, `categories`, `minCoveVersion`, and `updatedAt`.
- CI derives `metadata.version` and `metadata.updatedAt` from the latest entry in `versions[]`.
- CI syncs canonical metadata fields into `index.json`, so maintainers do not manually duplicate version/date updates in both files.

### Extension ID Convention

Use reverse domain notation: `com.{org}.{extension-name}`

Official extensions use `cove.official.{name}`.

### Categories

Available categories: `UI`, `Backend`, `Theme`, `Color Palette`, `Style`, `Layout`, `Analytics`, `Tools`, `Library`, `Scraper`, `Metadata`, `Integration`, `Automation`, `Content Management`, `Search`, `Import`, `Export`, `Notification`, `Security`, `Media Player`, `Recommendation System`

## Repo Patterns for Extension Authors

Extensions can be organized in two ways:

### Single-Extension Repository
One repository per extension. Simpler CI, clear ownership.
- Example: [cove-extension-notification-settings](https://github.com/yourcove/cove-extension-notification-settings)

### Multi-Extension Repository
Multiple extensions in one repository. Saves repos, shared tooling.
- Example: [cove-extensions-ui](https://github.com/yourcove/cove-extensions-ui) (Custom Home Page + Scene Analytics)

## License

MIT
