# Electron distribution example

Small `electron-builder` v26 + GitHub Actions reference for **macOS arm64/x64** and **Linux x64 (AppImage, DEB, RPM)**. Based on an older Electron release pipeline; not a drop-in config for another app.

## Before trying it

- Pin `electron` and `electron-builder` in `package-lock.json`. Assumes `npm ci` and `npm run build:app` compile the app into `out/`; change those commands/paths to match your repo.
- Replace the placeholder `appId`, `productName`, category, `files`, icons, etc. Keep the builder output (`dist/`) separate from your compiled app output (`out/`).
- GitHub secrets for macOS signing/notarization: `MAC_CSC_LINK` (base64 `.p12` **Developer ID Application** cert), `MAC_CSC_KEY_PASSWORD`, `APPLE_ID`, `APPLE_APP_SPECIFIC_PASSWORD`, `APPLE_TEAM_ID`. Tag builds fail if secrets are missing; manual workflow dispatch also requires them.
- `build/entitlements.mac.plist` contains the minimal Electron JIT entitlement. Add only other permissions your app actually needs, and test any native modules/helper binaries.
- Test macOS artifacts on Intel and Apple Silicon. Especially check that existing **x64 installations update or migrate correctly**: shipping an arm64 DMG by itself does *not* move existing Rosetta users over to arm64.
- On Linux, **install and run** the packages on the distros you support. CI only checks package metadata, not GUI startup, codecs, sandboxing or playback/DRM.

## Packaging choice

- **AppImage:** straightforward direct download; check auto-update strategy. With v26, the default runtime still needs FUSE2 on the user's machine. Newer v26 builds can opt into the static runtime with `toolsets: { appimage: "1.0.3" }` (test it with your pinned version).
- **Flatpak:** worthwhile if you want Flathub; needs a tested sandbox/portal configuration and separate publishing process. Not included here.
- **DEB/RPM:** convenient for native installations. Uploading `.deb`/`.rpm` to GitHub Releases is **not** an APT/DNF repository; signed repos and package-manager updates are separate work.
- **Snap:** makes sense if Ubuntu/Snap Store is an important channel.

I'd probably start with AppImage for direct downloads and test Flatpak next. That decision depends more on the audience/update requirements than on which format is technically easiest to produce.

## Scope

This example produces signed/notarized macOS builds and Linux packages, then uploads them to one GitHub Release on a `v*` tag. It doesn't set up app-specific auto-update feeds/metadata, key rotation, Flatpak/Snap publishing, repo hosting, streaming codecs/DRM, or Windows signing. You'd need to integrate those with the existing product pipeline.

Docs: [electron-builder v26](https://www.electron.build/v26/docs/) · [macOS notarization](https://www.electron.build/v26/docs/notarization/) · [AppImage runtime](https://www.electron.build/v26/docs/appimage/)
