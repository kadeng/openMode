# Building OpenMode – Packaging & Installation Guide

This guide explains how to build a **debug APK** for OpenMode on Android.

---

## Prerequisites

| Tool | Minimum Version | Notes |
|------|----------------|-------|
| [Flutter SDK](https://docs.flutter.dev/get-started/install) | 3.8.1 | Includes the Dart SDK |
| [Android Studio](https://developer.android.com/studio) | Hedgehog (2023.1) or later | Needed for the Android SDK and build tools |
| Android SDK | API 21 (Android 5.0) or higher | Install via Android Studio's SDK Manager |
| Java / JDK | 11 | Bundled with Android Studio; set `JAVA_HOME` if using a standalone JDK |

> **Windows users:** make sure `flutter` and `dart` are on your `PATH`. The Flutter installer can do this automatically.

---

## 1 – Set Up Your Environment

1. **Install Flutter** following the [official guide](https://docs.flutter.dev/get-started/install) for your OS.
2. **Accept Android licenses** (required once):
   ```bash
   flutter doctor --android-licenses
   ```
3. **Verify your setup:**
   ```bash
   flutter doctor -v
   ```
   All checks relevant to Android should show a green ✓ before you continue.

---

## 2 – Get the Source Code

```bash
git clone https://github.com/kadeng/openMode.git
cd openMode
```

---

## 3 – Install Flutter Dependencies

```bash
flutter pub get
```

This fetches all packages declared in `pubspec.yaml`.

---

## 4 – Build the Debug APK

Run the following command from the repository root:

```bash
flutter build apk --debug
```

Flutter compiles the app and produces the APK at:

```
build/app/outputs/flutter-apk/app-debug.apk
```

### Optional build flags

| Flag | Purpose |
|------|---------|
| `--target-platform android-arm` | ARM 32-bit only (smaller file) |
| `--target-platform android-arm64` | ARM 64-bit only |
| `--target-platform android-x64` | x86-64 (emulator-friendly) |
| `--split-per-abi` | One APK per ABI, keeps each file small |
| `--no-tree-shake-icons` | Disable icon tree-shaking (speeds up debug builds) |

Example – build a smaller APK for a 64-bit physical device:

```bash
flutter build apk --debug --target-platform android-arm64
```

---

## 5 – Install the APK on a Device or Emulator

### Option A – `flutter install` (recommended)

Connect a physical device (with **USB Debugging** enabled) or start an Android emulator, then run:

```bash
flutter install --debug
```

Flutter will detect the connected device and install the APK automatically.

### Option B – `adb install`

```bash
adb install build/app/outputs/flutter-apk/app-debug.apk
```

If you have multiple devices connected, specify the target:

```bash
adb -s <device-serial> install build/app/outputs/flutter-apk/app-debug.apk
```

List available devices with `adb devices`.

### Option C – Transfer and install manually

1. Copy `app-debug.apk` to your Android device (via USB, cloud storage, etc.).
2. On the device, open the file and tap **Install**.
3. If prompted, enable **Install from unknown sources** in *Settings → Security*.

---

## 6 – Run in Debug Mode (optional)

To run the app directly on a connected device with hot-reload enabled:

```bash
flutter run
```

Press `r` to hot-reload, `R` to hot-restart, and `q` to quit.

---

## Troubleshooting

| Symptom | Solution |
|---------|----------|
| `flutter doctor` reports missing Android SDK | Open Android Studio → SDK Manager and install the required SDK |
| Gradle build fails with out-of-memory error | Increase heap in `android/gradle.properties`: `org.gradle.jvmargs=-Xmx4G` |
| Device not detected by `adb` | Enable USB Debugging on the device and accept the RSA fingerprint prompt |
| `JAVA_HOME` not set | Point it to your JDK 11 installation, e.g. `export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64` |
| NDK version mismatch | The project requires NDK `27.0.12077973`; install it via Android Studio → SDK Manager → SDK Tools → NDK |

---

## Additional Resources

- [Flutter – Build and release an Android app](https://docs.flutter.dev/deployment/android)
- [Android Debug Bridge (adb)](https://developer.android.com/tools/adb)
- [Flutter CLI reference](https://docs.flutter.dev/reference/flutter-cli)
