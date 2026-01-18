# SETUP.md — Development Environment Guide for `family-sync-tasklist` (Flutter/Dart)

---

## Quick Reference: Setup Steps Summary

| Step | Description | Windows 11 | macOS | Linux |
|------|-------------|:----------:|:-----:|:-----:|
| 1    | Install Git & Git Bash | ✅ | — | — |
| 2    | Install VS Code (minimal extensions) | ✅ | ✅ | ✅ |
| 3    | Install Flutter SDK | ✅ | ✅ | ✅ |
| 4    | Install Dart SDK (if needed) | ✅ | ✅ | ✅ |
| 5    | Configure PATH for Flutter/Dart | ✅ | ✅ | ✅ |
| 6    | Configure Git Bash as VS Code terminal | ✅ | — | — |
| 7    | Install Android SDK & Emulator | ✅ | ✅ | ✅ |
| 8    | Install iOS SDK & Xcode | — | ✅ | — |
| 9    | Enable desktop platforms (Windows/macOS/Linux) | ✅ | ✅ | ✅ |
| 10   | Configure environment variables | ✅ | ✅ | ✅ |
| 11   | Apply .gitignore template | ✅ | ✅ | ✅ |
| 12   | Validate setup & run tests | ✅ | ✅ | ✅ |
| 13   | CI checklist for contributors | ✅ | ✅ | ✅ |

---

## Introduction

Welcome to the `family-sync-tasklist` project! This guide will help you set up a robust, repeatable, and future-proof development environment on **Windows 11** (with cross-platform notes for macOS and Linux). The setup is aligned with the project’s architectural principles: **offline-first**, **data durability**, **eventual consistency**, **modularity**, and **cross-platform consistency**. All steps are designed to minimize friction, maximize reliability, and ensure that every contributor can build, test, and validate the app’s core behaviors—especially its offline and sync logic.

**Key tools:**  
- **VS Code** (minimal extensions)  
- **Git Bash** (terminal)  
- **Microsoft Edge** (browser for web testing)  
- **Flutter/Dart SDKs**  
- **Android SDK & Emulator**  
- **(macOS only) Xcode & iOS Simulator**  

---

## 1. Prerequisites

Before you begin, ensure you have:

- **Windows 11** (64-bit) with admin privileges  
- **Stable internet connection**  
- **Sufficient disk space** (at least 10 GB recommended for SDKs and emulators)  
- **Edge browser** (pre-installed on Windows 11)  

---

## 2. Install Git & Git Bash

**Why:**  
Git is required for version control and Flutter’s CLI. Git Bash provides a Unix-like shell, improving CLI compatibility and scripting on Windows.

**Steps:**  
1. Download Git for Windows: [git-scm.com/download/win](https://git-scm.com/download/win)
2. Run the installer.  
   - Select **Git Bash** as the default terminal emulator.
   - Accept defaults unless you have specific preferences.
3. After installation, open **Git Bash** and verify:
   ```bash
   git --version
   ```
   You should see the installed version.

**Cross-platform notes:**  
- On **macOS** and **Linux**, Git is typically pre-installed or available via package managers (`brew install git` or `sudo apt-get install git`).

---

## 3. Install Visual Studio Code (VS Code)

**Why:**  
VS Code is the recommended lightweight, cross-platform IDE for Flutter/Dart development.

**Steps:**  
1. Download VS Code: [code.visualstudio.com](https://code.visualstudio.com/)
2. Install using the default options.
3. Launch VS Code.

**Minimal Extensions (see next section for details):**  
- **Dart**  
- **Flutter**  
- (Optional) **Error Lens**, **Bracket Pair Colorizer 2**, **Flutter Intl** (for i18n), **Flutter Files** (for scaffolding), **Pubspec Assist** (for dependency management)

---

## 4. Install Flutter SDK

**Why:**  
Flutter is the core framework for building the app across all platforms.

### Windows 11

**Manual Installation (recommended for repeatability):**
1. Download the latest stable Flutter SDK ZIP:  
   [Flutter SDK Releases](https://docs.flutter.dev/install/manual)
2. Extract to a directory with no spaces or special characters, e.g.:  
   `C:\Users\<username>\develop\flutter`
3. Add Flutter to your **PATH**:
   - Open **System Properties** (`Win + Pause` or search "Environment Variables").
   - Click **Advanced System Settings > Environment Variables**.
   - Under **User variables**, select `Path` > **Edit**.
   - Add:  
     ```
     %USERPROFILE%\develop\flutter\bin
     ```
   - Move the Flutter entry to the top of the list for priority.
   - Click **OK** to save.

4. **Apply changes:**  
   Close and reopen all terminals and VS Code.

5. **Verify installation:**
   ```bash
   flutter --version
   dart --version
   ```

**Cross-platform notes:**

- **macOS:**  
  - Download and extract the Flutter SDK.
  - Add to PATH in `~/.zshrc` or `~/.zprofile`:
    ```bash
    export PATH="$HOME/develop/flutter/bin:$PATH"
    ```
  - Source the file or restart the terminal.
- **Linux:**  
  - Download and extract.
  - Add to PATH in `~/.bashrc` or `~/.zshenv`:
    ```bash
    export PATH="$HOME/develop/flutter/bin:$PATH"
    ```
  - Source the file or restart the terminal.

**Reference:**  
[Flutter Manual Install Guide][0†docs.flutter.dev][4†docs.flutter.dev][27†deadloq][17†codestudy][20†codestudy][14†geeksforgeeks][35†stackoverflow]

---

## 5. Install Dart SDK (if needed)

**Note:**  
The Dart SDK is bundled with Flutter. Only install separately if you need standalone Dart tooling.

**Standalone Dart Installation (Windows):**
1. Download Dart SDK: [Dart SDK Archive](https://dart.dev/tools/sdk/archive)
2. Extract to `C:\dart-sdk`
3. Add `C:\dart-sdk\bin` to your PATH (see above).

**Cross-platform notes:**  
- **macOS:**  
  ```bash
  brew tap dart-lang/dart
  brew install dart
  ```
- **Linux:**  
  ```bash
  sudo apt-get update
  sudo apt-get install apt-transport-https
  wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
  echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.list
  sudo apt-get update
  sudo apt-get install dart
  ```
  Add to PATH:
  ```bash
  export PATH="$PATH:/usr/lib/dart/bin"
  ```

**Reference:**  
[Dart Install Guide][12†dart-tutorial][15†geeksforgeeks]

---

## 6. Configure Git Bash as VS Code Terminal (Windows)

**Why:**  
Ensures a consistent, Unix-like CLI experience and avoids Windows shell quirks with Flutter CLI.

**Steps:**
1. Open VS Code.
2. Go to **File > Preferences > Settings** (or `Ctrl+,`).
3. Search for `terminal integrated shell`.
4. Click **Edit in settings.json**.
5. Add:
   ```json
   "terminal.integrated.profiles.windows": {
     "Git Bash": {
       "path": "C:\\Program Files\\Git\\bin\\bash.exe"
     }
   },
   "terminal.integrated.defaultProfile.windows": "Git Bash"
   ```
6. Save and restart VS Code.

**Reference:**  
[VS Code + Git Bash Integration][48†delftstack][49†geeksforgeeks]

---

## 7. Install Android SDK & Emulator

**Why:**  
Required for building and testing Android apps.

**Steps:**
1. Download and install **Android Studio**: [developer.android.com/studio](https://developer.android.com/studio)
2. During installation, ensure **Android SDK** and **Android Virtual Device (AVD)** are selected.
3. Launch Android Studio and open **SDK Manager**:
   - Install the latest **Android SDK Platform** (e.g., API 35+).
   - Under **SDK Tools**, ensure **Android SDK Command-line Tools** and **Android Emulator** are installed.
4. Open **AVD Manager** and create a new emulator (e.g., Pixel 5, Android 15).
5. Add the Android SDK to your PATH:
   - Find your SDK location (e.g., `C:\Users\<username>\AppData\Local\Android\Sdk`)
   - Add to PATH:
     ```
     %USERPROFILE%\AppData\Local\Android\Sdk\platform-tools
     ```
   - Optionally, set `ANDROID_HOME` and `ANDROID_SDK_ROOT` environment variables to the SDK path.

6. Accept Android licenses:
   ```bash
   flutter doctor --android-licenses
   ```
   Press `y` to accept all.

**Cross-platform notes:**
- **macOS/Linux:**  
  - Android Studio and SDK setup is similar.
  - Add SDK paths to `~/.zshrc` or `~/.bashrc` as needed.

**Reference:**  
[Android SDK Setup][14†geeksforgeeks][28†gist][29†android-developer]

---

## 8. Install iOS SDK & Xcode (macOS only)

**Why:**  
Required for building and testing iOS apps. **Not available on Windows or Linux.**

**Steps (macOS):**
1. Install Xcode from the Mac App Store.
2. Open Xcode and accept the license.
3. Install command-line tools:
   ```bash
   sudo xcode-select -s /Applications/Xcode.app/Contents/Developer && xcodebuild -runFirstLaunch
   sudo xcodebuild -license
   ```
4. Install CocoaPods:
   ```bash
   sudo gem install cocoapods
   ```
5. Open **Simulator**:
   ```bash
   open -a Simulator
   ```

**Reference:**  
[Flutter macOS/iOS Setup][22†docs.flutter.dev][31†docs.flutter.dev][23†cocoapods][30†codegenes]

---

## 9. Enable Desktop Platforms (Windows, macOS, Linux)

**Why:**  
To build and test native desktop apps.

**Steps:**
1. In your project root, run:
   ```bash
   flutter config --enable-windows-desktop --enable-macos-desktop --enable-linux-desktop
   ```
   (Enable only the platforms you need.)

2. To add desktop support to an existing project:
   ```bash
   flutter create --platforms=windows,macos,linux .
   ```

3. To run on a specific platform:
   ```bash
   flutter run -d windows
   flutter run -d macos
   flutter run -d linux
   ```

**Platform-specific notes:**
- **Windows:** Requires Visual Studio 2022 or Build Tools with "Desktop development with C++" workload.
- **macOS:** Requires Xcode.
- **Linux:** Requires `clang`, `cmake`, `ninja-build`, `pkg-config`, `libgtk-3-dev`, `libstdc++-12-dev`.

**Reference:**  
[Flutter Desktop Support][3†docs.flutter.dev][24†liudonghua][26†docs.flutter.dev][25†github]

---

## 10. Configure Environment Variables and PATH

**Why:**  
Ensures all CLI tools (Flutter, Dart, Android SDK) are available in all terminals and IDEs.

**Checklist:**
- **Flutter:**  
  - Windows: `%USERPROFILE%\develop\flutter\bin`
  - macOS/Linux: `$HOME/develop/flutter/bin`
- **Dart:**  
  - Windows: `C:\dart-sdk\bin` (if standalone)
  - macOS/Linux: `/usr/lib/dart/bin` or via Homebrew
- **Android SDK:**  
  - Windows: `%USERPROFILE%\AppData\Local\Android\Sdk\platform-tools`
  - macOS/Linux: `$HOME/Library/Android/sdk/platform-tools` or similar
  - Set `ANDROID_HOME` and `ANDROID_SDK_ROOT` to SDK path

**How to check:**
```bash
flutter --version
dart --version
adb --version
```

**Troubleshooting:**
- If `flutter` or `dart` is not found, double-check PATH entries and restart all terminals/VS Code.
- On Windows, ensure you use **forward slashes** (`/`) in Git config paths for Flutter CLI compatibility.

---

## 11. Recommended Minimal VS Code Extensions

**Required:**
- **Dart** (official)
- **Flutter** (official)

**Recommended (optional, for productivity):**
- **Error Lens** (highlights errors/warnings inline)
- **Bracket Pair Colorizer 2** (visualizes nested brackets)
- **Flutter Intl** (i18n support)
- **Flutter Files** (scaffolding)
- **Pubspec Assist** (dependency management)
- **Flutter Color** (color visualization)
- **Flutter Tree** (widget tree visualization)
- **Awesome Flutter Snippets** (code snippets)

**How to install:**
- Open VS Code, go to **Extensions** (`Ctrl+Shift+X`), search and install.

**Reference:**  
[VS Code Flutter Extensions][1†dev.to][18†marketplace][19†github]

---

## 12. Git Bash Compatibility Tips (Windows)

**Common Issues:**
- **Flutter CLI not found:**  
  - Ensure Git Bash is launched after PATH is set.
  - If using FVM (Flutter Version Manager), use the real path, not a symlink.
- **"Unable to find git in your PATH":**  
  - Add Git’s `bin` directory to PATH:  
    `C:\Program Files\Git\bin`
  - Use forward slashes in Git config:
    ```bash
    git config --global --add safe.directory "C:/Users/<username>/develop/flutter"
    ```
- **Admin permissions:**  
  - Some commands may require running Git Bash or VS Code as Administrator.

**Reference:**  
[Flutter + Git Bash Troubleshooting][2†stackoverflow][20†codestudy][27†deadloq]

---

## 13. Platform Enablement Steps

### Windows

- **Desktop:**  
  - Requires Visual Studio 2022 or Build Tools with "Desktop development with C++".
  - Enable with `flutter config --enable-windows-desktop`.
- **Android:**  
  - Android Studio + SDK + Emulator.
- **Web:**  
  - Edge browser (default on Windows 11).
  - Run with:
    ```bash
    flutter run -d edge
    ```
  - For debugging, Edge may launch a new instance; see [Edge debugging notes][44†stackoverflow][47†github].

### macOS

- **Desktop:**  
  - Enable with `flutter config --enable-macos-desktop`.
  - Requires Xcode.
- **iOS:**  
  - Requires Xcode and CocoaPods.
  - Use Simulator for testing.
- **Android:**  
  - Same as Windows.
- **Web:**  
  - Safari, Chrome, or Edge.

### Linux

- **Desktop:**  
  - Enable with `flutter config --enable-linux-desktop`.
  - Install required packages: `clang`, `cmake`, `ninja-build`, `pkg-config`, `libgtk-3-dev`, `libstdc++-12-dev`.
- **Android:**  
  - Same as above.
- **Web:**  
  - Chrome, Edge, or Firefox.

---

## 14. .gitignore Template for Cross-Platform Flutter Projects

**Why:**  
Prevents committing build artifacts, local storage, platform-specific files, and secrets. Ensures clean, portable repositories.

**Recommended Template:**

```gitignore
# Flutter/Dart/Pub
.dart_tool/
.packages
.pub-cache/
build/
# For apps, keep pubspec.lock under version control
# For packages, you may ignore pubspec.lock

# Android
/android/.gradle/
/android/app/build/
*.keystore

# iOS
/ios/Flutter/Flutter.framework
/ios/Flutter/Flutter.podspec
/ios/Pods/
/ios/.symlinks/
/ios/Flutter/ephemeral
/ios/Flutter/Generated.xcconfig
/ios/Flutter/app.flx
/ios/Flutter/app.zip
/ios/Flutter/flutter_assets/
/ios/Flutter/flutter_export_environment.sh
/ios/ServiceDefinitions.json
/ios/Runner/GeneratedPluginRegistrant.*

# macOS
/macos/Flutter/ephemeral
/Pods/
/macos/Flutter/GeneratedPluginRegistrant.swift

# Windows
/windows/flutter/ephemeral/
/windows/flutter/generated_plugin_registrant.cc
/windows/flutter/generated_plugin_registrant.h
/windows/flutter/generated_plugins.cmake

# Linux
/linux/flutter/ephemeral/
/linux/flutter/generated_plugin_registrant.cc
/linux/flutter/generated_plugin_registrant.h
/linux/flutter/generated_plugins.cmake

# Web
/web/.dart_tool/
.webdev/

# IDE files
.idea/
*.iml
.vscode/

# OS files
.DS_Store
Thumbs.db

# Logs
*.log

# Secrets
.env

# Coverage
coverage/
```

**References:**  
- [Flutter Official .gitignore][38†github]
- [Flutter Gitignore Best Practices][34†fluttercurious]
- [Community Templates][5†github]

---

## 15. Environment Variables and Secrets Handling

**Principles:**
- **Never commit secrets or credentials** (API keys, tokens, etc.) to version control.
- Use a `.env` file (ignored by `.gitignore`) for local secrets.
- For secure storage of sensitive data (tokens, etc.), use [flutter_secure_storage][50†pub][51†github] (encrypted at rest).

**Example `.env` (do not commit):**
```
API_BASE_URL=https://api.example.com
API_KEY=your-local-key
```

**Access in Dart:**
- Use the `flutter_dotenv` package to load environment variables at runtime.

---

## 16. CI Checklist for Contributors

**Purpose:**  
Ensure every contributor’s environment is validated, tests pass, and core architectural guarantees (offline-first, sync, durability) are preserved.

### CI Checklist Table

| Check | Command | Description |
|-------|---------|-------------|
| Validate Flutter/Dart SDK | `flutter doctor` | Ensure all required SDKs and tools are installed and configured. |
| Install dependencies | `flutter pub get` | Fetch all Dart/Flutter dependencies. |
| Format code | `flutter format .` | Enforce code style. |
| Analyze code | `flutter analyze` | Lint and static analysis. |
| Run unit tests | `flutter test` | Run all unit and widget tests. |
| Run integration tests | `flutter test integration_test` | Validate end-to-end flows, including offline and sync logic. |
| Build for all platforms | `flutter build windows`<br>`flutter build apk`<br>`flutter build ios`<br>`flutter build linux`<br>`flutter build macos`<br>`flutter build web` | Ensure the app builds on all supported platforms. |
| Validate offline-first logic | (see below) | Run tests that simulate offline scenarios and verify data durability. |
| Validate sync/eventual consistency | (see below) | Run tests that simulate concurrent edits, conflicts, and sync recovery. |
| Check .gitignore | — | Ensure no build artifacts, secrets, or local files are committed. |

**Offline-First and Sync Logic Testing:**
- Use integration tests to simulate:
  - Adding/editing/deleting tasks and comments offline
  - Restarting the app and verifying data durability
  - Reconnecting and verifying sync/consistency
  - Conflict scenarios (concurrent edits, deletes, comments)
- Ensure all tests pass before submitting a PR.

**CI/CD Integration:**
- Use GitHub Actions or similar to automate the above checks on every PR.
- Example workflow:  
  - Checkout code
  - Set up Flutter
  - Install dependencies
  - Run format, analyze, test, build steps
  - Upload test and coverage reports

**References:**  
[Flutter CI/CD with GitHub Actions][33†200ok][36†readmedium][37†docs.flutter.dev]

---

## 17. Testing and Instrumentation for Offline-First and Sync Logic

**Principles:**
- All core features must be covered by unit, widget, and integration tests.
- Simulate offline scenarios, device restarts, and sync edge cases.
- Use fakes/mocks for backend APIs to test conflict resolution and eventual consistency.
- Instrument the sync engine to emit logs and status events for observability.

**Testing Tools:**
- `flutter_test` for unit/widget tests
- `integration_test` for end-to-end flows
- `mockito` or `mocktail` for mocking dependencies

**Sample Integration Test (offline-first):**
```dart
testWidgets('Add task offline, syncs when online', (tester) async {
  // Simulate offline mode
  await tester.pumpWidget(MyApp(isOffline: true));
  // Add a task
  await tester.enterText(find.byType(TextField), 'Buy milk');
  await tester.tap(find.byIcon(Icons.add));
  await tester.pump();
  // Verify task appears in UI and is persisted locally
  expect(find.text('Buy milk'), findsOneWidget);
  // Simulate going online and trigger sync
  await tester.pumpWidget(MyApp(isOffline: false));
  await tester.pumpAndSettle();
  // Verify task is synced and marked as such
  expect(find.byIcon(Icons.sync), findsNothing);
});
```

**References:**  
[Flutter Integration Testing][37†docs.flutter.dev]

---

## 18. Background Sync Mechanisms

**Principles:**
- Sync must run in the background, triggered by connectivity changes, app startup, and periodically.
- Use platform-specific mechanisms:
  - **Android:** WorkManager
  - **iOS:** Background fetch, silent push, or BGTaskScheduler (limitations apply)
  - **Desktop/Web:** Background isolates, timers, or service workers

**Implementation Notes:**
- Ensure sync is resource-aware (battery, bandwidth).
- Defer or throttle sync as needed.
- Expose sync status and errors to the UI.

**References:**  
[Background Processing in Flutter][40†technaureus][41†stackoverflow]

---

## 19. Local Storage Backend Options and Recommendations

**Principles:**
- Use a storage backend that is:
  - Durable (crash-safe, transactional)
  - Cross-platform (works on mobile, desktop, web)
  - Supports migrations and integrity checks
  - Efficient for offline-first, sync-heavy workloads

**Recommended Backends:**
- **Mobile/Desktop:**  
  - **Drift** (SQLite ORM, recommended for relational data, strong migrations, cross-platform)
  - **Isar** (NoSQL, high performance, object-oriented, cross-platform)
  - **Hive** (lightweight, key-value, good for simple data)
- **Web:**  
  - **IndexedDB** (via `idb_shim` or similar)
  - **Hive** (web support)
  - **Isar** (web support)

**Selection Guidance:**
- For complex, relational data with sync/versioning: **Drift**
- For high performance, large datasets: **Isar**
- For simple key-value storage: **Hive**

**References:**  
[Flutter Database Comparison][42†quashbugs][46†objectbox][39†dev.to]

---

## 20. Conflict Resolution Strategies

**Principles:**
- Use **Last-Write-Wins (LWW)** for simple fields (title, status).
- Use **CRDTs** or field-level merges for lists/comments to preserve all user intent.
- Surface unresolved conflicts to the user with clear explanations and options for manual resolution.

**Implementation Notes:**
- Each item includes version numbers, logical clocks, or timestamps for conflict detection.
- Sync engine applies deterministic resolution and updates sync status.
- UI displays conflict indicators and allows user intervention if needed.

**References:**  
[Conflict Resolution in Offline-First][16†developersvoice][43†geeksforgeeks][45†fponzi]

---

## 21. Developer Tooling: Emulators, Simulators, and Browsers

**Android:**
- Use Android Studio’s AVD Manager to create and run emulators.
- Test on multiple API levels and device types.

**iOS (macOS only):**
- Use Xcode’s Simulator for iPhone/iPad testing.

**Desktop:**
- Use `flutter run -d windows|macos|linux` to launch native desktop apps.

**Web:**
- Use Edge browser for testing (`flutter run -d edge`).
- For debugging, Edge may launch a new instance; see [Edge debugging notes][44†stackoverflow][47†github].
- To attach to an existing browser instance, use `flutter run -d web-server` and open the URL manually.

---

## 22. VS Code Minimal Launch and Settings for Git Bash and Edge

**VS Code Settings Example (`settings.json`):**
```json
{
  "terminal.integrated.profiles.windows": {
    "Git Bash": {
      "path": "C:\\Program Files\\Git\\bin\\bash.exe"
    }
  },
  "terminal.integrated.defaultProfile.windows": "Git Bash",
  "dart.flutterSdkPath": "C:\\Users\\<username>\\develop\\flutter",
  "dart.previewFlutterUiGuides": true,
  "dart.previewFlutterUiGuidesCustomTracking": true,
  "dart.openDevTools": "flutter",
  "dart.flutterRunAdditionalArgs": [
    "--web-port=8080"
  ]
}
```
- This configures Git Bash as the default terminal and sets up Flutter SDK path.
- For Edge debugging, use `flutter run -d edge` or configure launch tasks as needed.

---

## 23. Project-Specific .env and Secrets Handling

**Principles:**
- Never commit secrets or credentials to version control.
- Use `.env` files (ignored by `.gitignore`) for local secrets.
- Use [flutter_secure_storage][50†pub][51†github] for storing sensitive data at runtime (encrypted at rest).

**Example:**
- `.env` (not committed):
  ```
  API_BASE_URL=https://api.family-sync-tasklist.com
  API_KEY=your-local-key
  ```
- Load with `flutter_dotenv` package.

---

## 24. Alignment with Architectural Principles

This setup guide is **directly aligned** with the project’s architectural and design decisions as outlined in `ARCHITECTURE.md`, `DECISIONS.md`, `PRINCIPLES.md`, and `REQUIREMENTS.md`:

- **Offline-first:**  
  - All local changes are immediately persisted to durable storage.
  - The app is fully usable offline; sync is backgrounded and non-blocking.
- **Data durability:**  
  - Storage backends are crash-safe and transactional.
  - Integrity checks and migrations are enforced.
- **Eventual consistency:**  
  - Sync engine tracks unsynced changes, retries failed syncs, and resolves conflicts deterministically.
  - All devices eventually converge to the same state.
- **Modularity and separation of concerns:**  
  - Repository pattern abstracts storage.
  - Sync logic is isolated and testable.
- **Cross-platform consistency:**  
  - Identical features and behaviors across web, desktop, and mobile.
  - Platform-specific adaptations are abstracted behind unified APIs.
- **Security and privacy:**  
  - Local storage is encrypted where possible.
  - All network communication uses HTTPS/TLS.
  - Access controls and secrets management are enforced.

---

## 25. Additional Resources

- [Flutter Official Installation Guide][0†docs.flutter.dev]
- [Flutter Desktop Support][3†docs.flutter.dev]
- [Flutter Testing & Integration][37†docs.flutter.dev]
- [Flutter Secure Storage][50†pub][51†github]
- [Flutter Gitignore Best Practices][34†fluttercurious]
- [Flutter CI/CD with GitHub Actions][33†200ok][36†readmedium]
- [Flutter Database Comparison][42†quashbugs][46†objectbox]

---

## 26. Troubleshooting & FAQ

**Q: `flutter` or `dart` not found in terminal?**  
A: Double-check PATH configuration, restart all terminals and VS Code, and ensure you’re using the correct shell (Git Bash on Windows).

**Q: Android emulator not detected?**  
A: Ensure Android SDK and AVD are installed, and that `ANDROID_HOME`/`ANDROID_SDK_ROOT` are set.

**Q: iOS build not available on Windows?**  
A: iOS builds require macOS and Xcode. Use a Mac or a cloud-based macOS CI service for iOS builds.

**Q: Git Bash/Flutter CLI issues on Windows?**  
A: Use forward slashes in paths, ensure Git’s `bin` directory is in PATH, and run as Administrator if needed.

**Q: How do I test offline-first and sync logic?**  
A: Use integration tests to simulate offline scenarios, device restarts, and sync edge cases. See the CI checklist and testing section above.

---

## 27. Contribution Guidelines

- **Follow this setup guide** to ensure a consistent environment.
- **Run all CI checks** before submitting a PR.
- **Document any platform-specific issues or workarounds** in your PR description.
- **Update this SETUP.md** if you discover improvements or new requirements.

---

**Thank you for contributing to `family-sync-tasklist`! Your diligence in following this guide ensures a robust, reliable, and family-friendly app for everyone.**

---

**End of SETUP.md**
