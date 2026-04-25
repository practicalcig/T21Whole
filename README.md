# Wholeness — Android App

Native Android wrapper for the Wholeness life tracker. Your full HTML/CSS/JS app
runs inside a configured WebView, with `localStorage` enabled so all user data
(goals, logs, name) persists across app launches just like on the web.

## What's inside

```
wholeness-android/
├── .github/workflows/build.yml         ← cloud build (no local setup needed)
├── app/
│   ├── src/main/
│   │   ├── AndroidManifest.xml
│   │   ├── java/com/team21/wholeness/MainActivity.java   ← WebView host
│   │   ├── assets/web/index.html                         ← your full app
│   │   └── res/                                          ← icons, theme, strings
│   └── build.gradle
├── build.gradle
├── settings.gradle
├── gradle.properties
├── gradle/wrapper/
│   ├── gradle-wrapper.jar              ← committed, don't regenerate
│   └── gradle-wrapper.properties
├── gradlew                             ← Linux/macOS launcher
└── gradlew.bat                         ← Windows launcher
```

The Gradle wrapper is **already committed** to the project. You don't need to
run `gradle wrapper` — the wrapper jar will download Gradle 8.7 itself the
first time you build.

## Build the APK — 3 ways

### Option A — GitHub Actions (recommended, zero local setup)

1. Create a free GitHub account at https://github.com
2. Create a new repository (any name; keep it private if you prefer)
3. Upload all the files from this folder to the repo. Easiest way:
   - On the repo page, click **"uploading an existing file"**
   - Drag the entire `wholeness-android` folder contents in
   - **Important:** make sure the hidden `.github/` folder is included.
     If you're zipping/unzipping, use a tool that preserves dotfiles.
4. Click **Commit changes**. The workflow runs automatically.
5. Open the **Actions** tab → click the running build → wait ~5 minutes
6. When it finishes (green check), scroll down to **Artifacts** and download
   `wholeness-debug-apk` — this contains your `app-debug.apk`

### Option B — Android Studio

1. Install Android Studio (Hedgehog or newer) from
   https://developer.android.com/studio
2. **File → Open** → select the `wholeness-android/` folder
3. Accept SDK component installations when prompted
4. Wait for the initial Gradle sync (5–10 minutes the first time)
5. **Build → Build Bundle(s) / APK(s) → Build APK(s)**
6. Click **locate** in the notification — your APK is at:
   `app/build/outputs/apk/debug/app-debug.apk`

### Option C — Command line

Requires JDK 17+ and the Android SDK with `cmdline-tools` and
`build-tools;34.0.0` installed. Set `ANDROID_HOME` to your SDK path.

```bash
cd wholeness-android
chmod +x gradlew                # Linux/macOS only
./gradlew assembleDebug         # or `gradlew.bat assembleDebug` on Windows

# APK output:
ls app/build/outputs/apk/debug/app-debug.apk
```

The first run downloads Gradle 8.7 (~150 MB) and the Android Gradle Plugin
dependencies (~200 MB). Subsequent builds are much faster.

## Install the APK on a phone

1. Transfer `app-debug.apk` to your Android device (email, USB, Google Drive, etc.)
2. On the device, tap the file. Android will warn that it's from an unknown
   source — go to **Settings → Apps → Special access → Install unknown apps**
   and enable it for the file manager you're using.
3. Tap **Install**. The Wholeness icon appears in your launcher.

## Updating your web app

To push a new version of the HTML to the APK:

1. Replace `app/src/main/assets/web/index.html` with your new file
2. Bump `versionCode` and `versionName` in `app/build.gradle`
3. Rebuild — user data (`localStorage`) survives app updates because the
   `applicationId` stays the same.

## Notes on the WebView setup

- **`localStorage` is enabled** — all your existing storage code (`whn_<n>_…`
  keys) works unchanged.
- **Google Fonts load over HTTPS.** The first launch needs internet for fonts;
  after that they're cached and the app works offline.
- **Hardware back button** navigates WebView history before exiting.
- **Status bar and navigation bar** are tinted dark to match the splash and
  avoid white flashes on launch.
- **Splash screen** shows the gold "Wholeness" wordmark on dark background
  while the WebView warms up.
- **`minSdk = 21`** (Android 5.0+, ~99% of active devices).
- **No third-party dependencies** — pure Android framework.

## Customising

- **App name:** `app/src/main/res/values/strings.xml`
- **Colors:** `app/src/main/res/values/colors.xml`
- **Launcher icons:** regenerate with `python3 generate_icons.py` (requires
  Pillow: `pip install Pillow`)
- **Package name:** change `applicationId` in `app/build.gradle`, the
  `package` attribute in `AndroidManifest.xml`, and the folder structure under
  `app/src/main/java/`

## For Google Play Store

The included builds are signed with Android's debug key — they install on
any device but can't be uploaded to Play. To create a Play-ready release:

1. In Android Studio: **Build → Generate Signed Bundle / APK → Android App
   Bundle**
2. Create a new keystore (back it up — you can never replace a lost keystore
   for a published app)
3. Upload the resulting `.aab` to Google Play Console

## License

© Innocent Forteh · Team21 Academy · Inno4te Publishing
