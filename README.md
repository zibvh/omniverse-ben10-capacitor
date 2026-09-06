# Omniverse: Open City — Android build

A Ben 10-inspired 3D open-world game (Three.js), wrapped as an Android app
with [Capacitor](https://capacitorjs.com/).

## Get the APK

Every push builds a fresh debug APK automatically via GitHub Actions:

- **Actions tab** → latest "Build Android APK" run → **Artifacts** →
  `app-debug`, or
- **Releases** (right sidebar) → latest release → `app-debug.apk` asset —
  this one's a direct download link, no GitHub account needed.

## Project layout

- `www/` — the actual game (`index.html`) plus `models/` (drop `.glb`
  character models here — see `www/models/README.md` for the exact spec).
  `www/libs/` (three.js + GLTFLoader) is fetched fresh from npm during the
  build and isn't stored in the repo.
- `android/` — **not stored here**. `.github/workflows/build-apk.yml`
  regenerates it on every run via `npx cap add android`, so there's no
  native-project boilerplate or binary files to keep in sync.
- `capacitor.config.json` — app id (`com.omniverse.opencity`) and name
  ("Omniverse Open City").

## Building locally

```
npm install
npx cap add android
npx cap copy android
cd android && ./gradlew assembleDebug
```

Needs a JDK + the Android SDK on your machine (Android Studio provides
both). Full manual-build walkthrough, including Android Studio steps, is
in this repo's first commit message / can be regenerated on request.

## Adding real character models

Nothing in `www/models/` yet but placeholder notes — the game runs fine
without them (procedural placeholder meshes fill in automatically). Drop
`.glb` files in following `www/models/README.md`'s naming spec, push, and
the next build will pick them up.
