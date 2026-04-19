# Cove Official Extension Registry

This repository serves as the official extension registry for [Cove](https://github.com/yourcove/cove). It contains metadata for all officially published extensions that can be discovered and installed through the Cove UI.

## Structure

```
index.json                              # Master index of all extensions
extensions/
  {extension-id}/
    metadata.json                       # Full extension metadata
    README.md                           # Extension documentation
    icon.png                            # Optional icon (128x128 recommended)
```

## How It Works

1. The Cove application fetches `index.json` from this repository's `main` branch via GitHub raw content URLs
2. Users can browse and search extensions in Settings → Extensions → Find and Install Extensions
3. When a user installs an extension, Cove downloads the release ZIP from the extension's own GitHub repository
4. Extensions are extracted to the local extensions directory and loaded automatically

## Adding an Extension

### For Extension Authors

1. **Build your extension** following the [Cove Extension Development Guide](https://github.com/yourcove/cove/blob/main/docs/ARCHITECTURE.md)
2. **Create GitHub releases** with ZIP packages named `{extension-id}-{version}.zip`
3. **Submit a PR** to this repository adding your extension metadata:
   - Add an entry to `index.json`
   - Create `extensions/{your-extension-id}/metadata.json`
   - Create `extensions/{your-extension-id}/README.md`

### metadata.json Schema

```json
{
  "id": "com.example.my-extension",
  "name": "My Extension",
  "version": "1.0.0",
  "description": "What this extension does.",
  "author": "Your Name",
  "url": "https://github.com/your-org/your-repo",
  "repositoryUrl": "https://github.com/your-org/your-repo",
  "categories": ["UI", "Analytics"],
  "downloads": 0,
  "updatedAt": "2026-01-01T00:00:00Z",
  "minCoveVersion": "1.0.0",
  "dependencies": {},
  "changelog": "## 1.0.0\n- Initial release",
  "screenshots": [],
  "versions": [
    {
      "version": "1.0.0",
      "releasedAt": "2026-01-01T00:00:00Z",
      "changelog": "Initial release",
      "minCoveVersion": "1.0.0",
      "downloadUrl": "https://github.com/your-org/your-repo/releases/download/v1.0.0/com.example.my-extension-1.0.0.zip"
    }
  ]
}
```

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
