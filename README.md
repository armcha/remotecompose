# Remote Compose Demo

A showcase of [AndroidX Remote Compose](https://developer.android.com/jetpack/androidx/releases/compose-remote) — server-driven UI for Android, rendered natively via Jetpack Compose.

**Live editor:** [Web Editor](https://armcha.github.io/remotecompose/)

https://github.com/user-attachments/assets/e6ac7586-1d85-4c68-a751-ca8ffb71a59f

## Architecture

```
 CREATION SIDE (JVM Server)              PLAYBACK SIDE (Android App)
 ┌────────────────────────┐              ┌────────────────────────┐
 │  Web Editor            │              │                        │
 │  edits JSON configs    │              │  RemoteComposePlayer   │
 │         │              │              │  renders binary docs   │
 │         ▼              │              │         ▲              │
 │  GitHub Action runs    │              │  Downloads ByteArray   │
 │  JVM converter         │   binary     │  directly              │
 │         │              │──  .rc  ────>│                        │
 │         ▼              │   files      │  No creation deps!     │
 │  RemoteComposeWriter   │              │  No JSON parsing!      │
 │  (pure JVM)            │              │  No @RestrictTo APIs!  │
 └────────────────────────┘              └────────────────────────┘
```

The Android app is a **pure player** — it downloads pre-built binary Remote Compose documents and renders them via `RemoteComposePlayer`. All document creation happens on a JVM server.

## How It Works

### 1. Edit UI in the web editor

The [web editor](https://armcha.github.io/remotecompose/) provides a drag-and-drop interface to build UI layouts with:
- **Text**, **Button**, **Spacer**, **Divider**, **Card**, **Row** elements
- Configurable properties: colors, font sizes, padding, corner radius, borders
- Click actions for navigation and host app events
- Multi-screen support (Home, Detail, Estimates, Estimate Detail)
- Live Compose/Wasm preview in a phone frame

### 2. Deploy

Click **Deploy** to push the JSON config to GitHub. A GitHub Action automatically:
1. Runs the JVM converter (`server/`)
2. Uses `RemoteComposeWriter` to generate binary `.rc` documents
3. Commits the `.rc` files back to the repo

The web editor shows real-time Action progress with a timer.

### 3. Android app renders

The Android app downloads the binary `.rc` files and renders them using `RemoteComposePlayer` (View-based player). No creation code, no JSON parsing — just binary bytes in, native UI out.

## Key Technologies

| Component | Technology |
|---|---|
| Android player | `RemoteComposePlayer` from `remote-player-view` |
| Binary format | Remote Compose wire format (binary operations) |
| JVM converter | `RemoteComposeWriter` from `remote-creation-core` + `remote-creation-jvm` |
| Web preview | Kotlin/Wasm + Compose Multiplatform |
| Web editor | Vanilla HTML/CSS/JS |
| CI/CD | GitHub Actions |
| Hosting | GitHub Pages |

## Related

- [remotecompose-android](https://github.com/armcha/remotecompose-android) — Android player app (pure player, no creation code)
- [Remote Compose documentation](https://github.com/androidx/androidx/tree/androidx-main/compose/remote/Documentation)
- [Remote Compose releases](https://developer.android.com/jetpack/androidx/releases/compose-remote)
