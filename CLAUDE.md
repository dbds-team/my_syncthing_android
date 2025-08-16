# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is Syncthing-Fork, an Android wrapper application for Syncthing. It's a fork of the official Syncthing Android app with additional features and maintenance. The app provides a GUI interface for the Syncthing synchronization tool on Android devices.

## Build Commands

### Prerequisites Setup
```bash
# Install Java 21, Python 3, and Android SDK
python3 scripts/install_minimum_android_sdk_prerequisites.py
export ANDROID_HOME=~/git/syncthing-android-prereq
```

### Build Native Syncthing Binary
```bash
./gradlew buildNative
```

### Debug Build
```bash
./gradlew lintDebug
./gradlew assembleDebug
# Output: app/build/outputs/apk/debug/app-debug.apk
```

### Release Build
```bash
# Note: Requires signing configuration or remove signingConfig from app/build.gradle.kts
./gradlew lintRelease
./gradlew assembleRelease
# Output: app/build/outputs/apk/release/app-release.apk
```

### Clean Build
```bash
./gradlew cleanNative
./gradlew clean
```

### Development Tools
```bash
# Forward Syncthing web GUI port to local machine
adb forward tcp:18384 tcp:8384
# Access at: http://127.0.0.1:18384
```

## Architecture

### Core Components

1. **App Module** (`app/src/main/java/com/nutomic/syncthingandroid/`)
   - Main Android application with Activities, Fragments, and Services
   - Uses Dagger for dependency injection (see `DaggerComponent.java`, `SyncthingModule.java`)
   - Key entry point: `SyncthingApp.java` - Application class

2. **Native Syncthing Integration** (`syncthing/`)
   - Contains Go-based Syncthing binary
   - Built separately using `./gradlew buildNative`
   - Python build script: `syncthing/build-syncthing.py`

3. **Service Layer** (`app/src/main/java/com/nutomic/syncthingandroid/service/`)
   - `RestApi.java` - Handles communication with Syncthing REST API
   - `EventProcessor.java` - Processes Syncthing events
   - `NotificationHandler.java` - Manages Android notifications
   - `ReceiverManager.java` - Manages broadcast receivers

4. **Activities** (`app/src/main/java/com/nutomic/syncthingandroid/activities/`)
   - `MainActivity.java` - Main app screen with folder/device lists
   - `FolderActivity.java` - Folder configuration
   - `DeviceActivity.java` - Device configuration
   - `SettingsActivity.java` - App settings
   - `WebGuiActivity.java` - Embedded Syncthing web interface

5. **Model Classes** (`app/src/main/java/com/nutomic/syncthingandroid/model/`)
   - Data models for Syncthing API responses
   - `Folder.java`, `Device.java`, `Config.java`, etc.

6. **HTTP Communication** (`app/src/main/java/com/nutomic/syncthingandroid/http/`)
   - `ApiRequest.java`, `GetRequest.java`, `PostRequest.java` - API request handling
   - Uses Volley for HTTP requests

### Key Technologies
- **Language**: Java (main app), Kotlin (some components like `LicenseActivity.kt`), Go (Syncthing binary)
- **Build System**: Gradle with Kotlin DSL
- **Minimum SDK**: 24 (Android 7.0)
- **Target SDK**: 35 (Android 15)
- **Dependency Injection**: Dagger
- **HTTP**: Volley
- **UI**: Material Design, some Compose components
- **QR Code**: ZXing library

### Build Variants
- **debug**: Development build with debugging enabled
- **release**: Production build (requires signing configuration)
- **gplay**: Google Play variant (inherits from release)

### Important Files
- `app/build.gradle.kts` - Main build configuration
- `gradle/libs.versions.toml` - Centralized dependency versions
- `app/src/main/AndroidManifest.xml` - App permissions and components
- `syncthing/build-syncthing.py` - Script to build native Syncthing