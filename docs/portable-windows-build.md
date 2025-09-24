# Portable Windows Build Workflow

This document describes the simplified GitHub Actions workflow for building Windows portable versions of Freelens.

## Overview

The `portable-windows-build.yaml` workflow creates minimal Windows portable executables and zip files for Freelens, without the complexity of signing, installers, or publishing found in the full release workflow.

## Features

- **Platforms**: Windows Server 2022 only
- **Architectures**: AMD64 (x64) and ARM64
- **Output Formats**: 
  - Portable executable (`.exe`)
  - ZIP archive (`.zip`)
- **Artifacts**: Uploaded to GitHub Actions (30-day retention)
- **No Code Signing**: Removes Azure Trusted Signing complexity
- **No Installers**: Excludes MSI and NSIS formats

## Trigger Options

### Manual Trigger
```bash
# Go to Actions tab in GitHub and manually trigger the workflow
```

### Automatic Trigger
Push to the `feature/portable-build` branch:
```bash
git checkout -b feature/portable-build
git push origin feature/portable-build
```

## Build Process

1. **Setup**: Checkout code, install Node.js, pnpm, and Windows dependencies (yq)
2. **Caching**: Cache Electron and Electron Builder downloads
3. **Dependencies**: Install pnpm packages with retry logic
4. **Cross-compilation**: For ARM64 builds, rebuild native modules
5. **Resources**: Generate tray icons and download binaries (with skip option for network issues)
6. **Electron Build**: Use `portable zip` targets instead of `msi nsis`
7. **File Naming**: Normalize filenames to include architecture (e.g., `-windows-amd64.exe`)
8. **Upload**: Create GitHub Actions artifacts for download

## Artifacts

The workflow produces artifacts named:
- `freelens-windows-amd64-portable` (contains .exe and .zip for x64)
- `freelens-windows-arm64-portable` (contains .exe and .zip for ARM64)

## Key Differences from Full Release

| Feature | Full Release | Portable Build |
|---------|-------------|----------------|
| Platforms | Windows, macOS, Linux | Windows only |
| Formats | MSI, NSIS, portable, zip | portable, zip only |
| Code Signing | Azure Trusted Signing | None |
| Output | GitHub Releases | GitHub Actions artifacts |
| Publishing | NPM, APT repository | None |
| Triggers | Git tags | Manual + feature branch |

## Troubleshooting

### Network Issues
If binary downloads fail, the workflow sets `LENS_SKIP_DOWNLOAD_BINARIES=true` to skip external dependencies.

### Build Failures
Check the "List files before upload" step to see what files were actually generated.

### Missing Artifacts
The workflow will fail if no `.exe` or `.zip` files are found in the expected location.

## Usage

1. Go to the **Actions** tab in the GitHub repository
2. Select **Portable Windows Build** workflow  
3. Click **Run workflow**
4. Choose the branch (usually `main` or `feature/portable-build`)
5. Wait for completion (~30-60 minutes)
6. Download artifacts from the workflow run summary page

The portable executables can run directly on Windows systems without installation.
