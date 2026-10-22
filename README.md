# VoxSwell updates

Public update channel for VoxSwell. The plugin reads `latest.json` and only trusts it when `latest.json.sig` verifies against the update key compiled into the plugin. The private key never leaves the maintainer's computer, so a compromised GitHub account cannot push an installer to customers.

## Publishing a release

1. Download the installers from the `voxswell-plugin` build artifacts (Windows `*-Setup.exe`, macOS `*.pkg`).
2. Create a release here with tag `v<version>` and upload both installers.
3. Write `latest.json` (schema 2):

```json
{
  "schema": 2,
  "version": "1.0.1",
  "mandatory": false,
  "notes": "What changed",
  "platforms": {
    "windows": { "url": "https://github.com/Vox-Swell/voxswell-updates/releases/download/v1.0.1/<file>.exe", "size": 0, "sha256": "..." },
    "macos":   { "url": "https://github.com/Vox-Swell/voxswell-updates/releases/download/v1.0.1/<file>.pkg", "size": 0, "sha256": "...", "architectures": ["arm64", "x86_64"] }
  }
}
```

   `make-manifest.py` in the plugin repo fills in sizes and SHA-256 values. `"mandatory": true` or `"minSupportedVersion"` hide the LATER button; use them only for critical fixes.
4. Sign: `node ../voxswell-plugin/updates/sign-manifest.mjs latest.json` (uses `secrets/update-signing-private.pem`).
5. Commit `latest.json` and `latest.json.sig` together, unchanged.
