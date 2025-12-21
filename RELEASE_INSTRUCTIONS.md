# Release Instructions

## Creating a GitHub Release

1. **Create the tar.gz file:**
   ```bash
   cd reconstruct-templates
   tar -czf templates-v0.1.tar.gz commands/ rules/
   ```

2. **Go to GitHub Releases:**
   - Visit: https://github.com/blkinnovate/reconstruct-templates/releases/new

3. **Create Release:**
   - **Tag:** `v0.1` (must match exactly)
   - **Title:** `v0.1 - Initial Release`
   - **Description:** (optional)
   - **⚠️ IMPORTANT:** Make sure "Set as the latest release" is checked
   - **⚠️ IMPORTANT:** Make sure "Set as a pre-release" is UNCHECKED (unless it's a beta)

4. **Upload Asset:**
   - Click "Attach binaries by dropping them here or selecting them"
   - Upload `templates-v0.1.tar.gz`
   - The file should be named exactly: `templates-v0.1.tar.gz`

5. **Publish Release:**
   - Click "Publish release"
   - The release must be **published** (not a draft) for the CLI to find it

## Testing After Release

```bash
# Test with latest
node reconstruct-cli/dist/cli/index.js init --assistant cursor --mode project --template-version latest

# Test with specific version
node reconstruct-cli/dist/cli/index.js init --assistant cursor --mode project --template-version v0.1
```

## Troubleshooting

- **"No releases found"**: Release is still a draft or not published
- **"Release not found"**: Tag name doesn't match (must be exactly `v0.1`)
- **"Template asset not found"**: tar.gz file not uploaded or has wrong name
- **"Failed to download asset"**: Network issue or asset not accessible

