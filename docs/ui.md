# UI Architecture

## Tech Stack
- **Framework**: JetBrains Compose Multiplatform
- **Target Platforms**: Desktop (macOS & Windows)

## Source Sets
- `app/composeApp/commonMain/`: Shared UI components, theme, state views, and screens.
- `app/composeApp/windowsMain/`: Windows-specific customizations (decorations, menu integration, direct window controls).
- `app/composeApp/macosMain/`: macOS-specific customizations (native window chrome, menu bar integration, system tray).
- `app/resources/`: Fonts, vector icons, color schemes, and localized strings.
