# OpenWithGUI

OpenWithGUI is a macOS desktop app for inspecting and editing file extension default-app associations in one place.

Instead of going through Finder's `Get Info -> Open with -> Change All...` flow one extension at a time, OpenWithGUI gives you a table-first manager for the current system state.

[中文说明](README.zh-CN.md)

## Screenshot

![OpenWithGUI screenshot](docs/assets/openwithgui-screenshot.png)

## What It Does

- Lists extension-to-default-app associations in a single table.
- Shows the current default app, bundle ID, and status for each extension.
- Filters rows by default app and status.
- Filters rows by candidate app, so you can see every extension an app declares it can open.
- **Filter all extensions supported by a specific app and batch change their default application in one action.**
- Searches by extension only, so results stay predictable.
- Supports multi-select, then assigns one app to all selected extensions in one action.
- Shows candidate apps for a single extension before changing it.
- Lets you add custom extensions and remove user-added ones.

## Requirements

- macOS 14 or later
- No Swift or Xcode installation is required to use the packaged app or DMG.

## Install From DMG

Download or open the DMG, then drag `OpenWithGUI.app` into `Applications`.

If macOS blocks the app because it is from an unidentified developer, allow it manually:

1. Drag `OpenWithGUI.app` into `Applications`.
2. In Finder, right-click `OpenWithGUI.app`.
3. Click `Open`.
4. Click `Open` again in the confirmation dialog.

You can also allow it from `System Settings -> Privacy & Security` if macOS shows a security warning there.

## Why It Exists

macOS makes default-app management tedious:

- You have to change associations one file type at a time.
- The system does not offer a central panel for reviewing everything.
- It is hard to see which app currently owns a given extension.
- It is hard to see all extensions supported by a specific app and batch change them.
- Some apps register broad associations and leave behind confusing state.

OpenWithGUI is meant to make that state visible and editable without requiring users to memorize bundle IDs or click through repeated Finder dialogs.

## License

This project is licensed under the [MIT License](LICENSE).

## Major Updates in This Fork

- **Filter by app and batch change**: You can now filter all file extensions supported by a specific app, then select multiple extensions and batch change their default application in one action.

## Similar Projects

- [ColeMei/openwith](https://github.com/ColeMei/openwith) - a Rust TUI project for managing macOS file extension associations from the terminal.
- [Run1997/OpenWith-GUI](https://github.com/Run1997/OpenWith-GUI) - a SwiftUI-based macOS default application manager with similar functionality.

## Build from Source

### Requirements

- macOS 14 or later
- Swift 6.0 or later (comes with Xcode 16 or Command Line Tools)

### Build Steps

1. Clone the repository:
```bash
git clone https://github.com/RayMorTwinkle/OpenWith_Panel.git
cd OpenWith_Panel
```

2. Build the project:
```bash
swift build -c release --disable-sandbox
```

3. Package the app:
```bash
# Create app bundle structure
mkdir -p dist/OpenWithGUI.app/Contents/MacOS
cp .build/arm64-apple-macosx/release/OpenWithGUI dist/OpenWithGUI.app/Contents/MacOS/
chmod +x dist/OpenWithGUI.app/Contents/MacOS/OpenWithGUI

# Copy resources
mkdir -p dist/OpenWithGUI.app/Contents/Resources
cp Assets/AppIcon.icns dist/OpenWithGUI.app/Contents/Resources/

# Create Info.plist
cat > dist/OpenWithGUI.app/Contents/Info.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>CFBundleExecutable</key>
    <string>OpenWithGUI</string>
    <key>CFBundleIdentifier</key>
    <string>com.openwithgui.app</string>
    <key>CFBundleName</key>
    <string>OpenWithGUI</string>
    <key>CFBundlePackageType</key>
    <string>APPL</string>
    <key>CFBundleShortVersionString</key>
    <string>0.2.0</string>
    <key>LSMinimumSystemVersion</key>
    <string>14.0</string>
</dict>
</plist>
EOF

# Sign the app
codesign --force --deep -s - dist/OpenWithGUI.app
```

4. Create DMG (optional):
```bash
mkdir -p dist/.dmg-staging
cp -R dist/OpenWithGUI.app dist/.dmg-staging/
ln -s /Applications dist/.dmg-staging/Applications
hdiutil create -volname "OpenWithGUI" -srcfolder dist/.dmg-staging -ov -format UDZO dist/OpenWithGUI.dmg
rm -rf dist/.dmg-staging
```

### Upload to GitHub Release

```bash
gh release create v0.2.0 \
  --title "v0.2.0 - Filter by app and batch change" \
  --notes "Release notes here" \
  dist/OpenWithGUI.dmg
```

## Acknowledgements

- [linux.do](https://linux.do/)
