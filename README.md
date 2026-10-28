# Aura Widget

**A home-screen music widget for Android that controls any player:** YouTube, YouTube Music, Spotify, Apple Music,
SoundCloud, Zing MP3, the stock player and more. Five hand-tuned themes, a live progress bar and an output-device
picker. [Tiếng Việt](README.vi.md)

## Themes

| Theme | Look |
|---|---|
| Liquid Glass | Frosted layers, light rims and reflections over your wallpaper |
| Nothing | Dot-matrix type (Doto) and monochrome accents |
| Material You | Colours from your wallpaper, M3 wave progress bar |
| AMOLED | True black, minimal |
| Album blur | The current cover art, blurred, as the background |

## Features

- **Follows whatever is playing.** `NotificationListenerService` + `MediaSessionManager` pick up the active media
  session and switch automatically to the app that just started playing.
- **Controls:** play/pause, previous/next; tap the cover to open the source app.
- **Progress bar** drawn as a bitmap per theme, updated every second while playing and paused when the screen is off
  to save battery.
- **Output picker:** a bottom sheet with every audio device (speaker, Bluetooth, wired, USB, HDMI, cast), a volume
  slider and a shortcut to the system output switcher.

## Build

Open the project in Android Studio (Ladybug 2024.2+) and run `app`, or from the command line:

```sh
./gradlew assembleDebug    # app/build/outputs/apk/debug/app-debug.apk
```

AGP 8.7.3, Kotlin 2.0.21, compileSdk 35, minSdk 26.

## First run

1. Open **Aura Widget** → **Grant permission** → enable *Notification access*.
   On Android 13+ with a sideloaded APK, if the switch is greyed out ("Restricted setting"), go to
   *App info → ⋮ → Allow restricted settings*.
2. Pick a theme → **Add widget to home screen** (or long-press the home screen → Widgets → Aura Player).
3. Play music in any app; the widget updates on its own.

## Code map

```
media/MediaListenerService.kt   media session capture, 1 s ticker, audio-device tracking
media/MediaRepository.kt        current controller + MediaSnapshot (title, art, position…)
widget/WidgetUpdater.kt         RemoteViews per theme, light per-second updates
widget/ProgressRenderer.kt      per-theme progress bar rendering
widget/BitmapUtils.kt           rounded art, album blur, accent colour (Palette)
widget/AuraWidgetProvider.kt    widget taps → transportControls
output/OutputSheetActivity.kt   output picker bottom sheet (Compose)
output/OutputSwitcher.kt        opens the right system output switcher per Android version
ui/MainActivity.kt              permissions, theme picker, live widget preview
tools/gen_layouts.py            generates the five widget layouts (edit theme values here, then rerun)
```

## Android limits worth knowing

- **Lock screen:** third-party apps cannot place widgets on the phone lock screen; Aura lives on the home screen.
- **Liquid Glass:** launchers do not let widgets blur the wallpaper behind them, so the glass is simulated with
  translucent layers, light rims and highlights. It looks best on wallpapers with moderate contrast.
- **Output switching:** apps cannot route another app's audio without the system permission
  `MEDIA_CONTENT_CONTROL`. Aura lists devices, adjusts volume and opens the system switcher: the app's media output
  dialog on Android 12–13, the shared `MediaRouter2` switcher on 14+, and the volume panel or Bluetooth settings on
  8–11 or on ROMs that do not support it.
- **Doto font** (Nothing theme) covers Latin and Latin Extended only; Vietnamese letters fall back to the system font.
- **Seeking by drag** is not possible on a widget (RemoteViews does not receive touch coordinates).

## Ideas

- Per-widget theme (configuration screen when adding a widget)
- Compact 4×1 layout with responsive RemoteViews (Android 12+)
- Like/shuffle buttons through `PlaybackState.customActions`

## Notes for Claude Code

`CLAUDE.md` (architecture, build, constraints), `docs/HANDOFF.md` (design decisions), `docs/TODO.md` (next steps),
`docs/design/` (HTML mockups, theme screenshots, references).
