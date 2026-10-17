# Crasenim Pharma Mobile App: Beginner's Guide

This guide explains what is currently in this folder, what you need to edit the
mobile app, how to test it on an Android phone, and how to build an APK to share.
It is written for someone new to Flutter and Android development.

## Important: where the editable code is

**The editable Flutter project is not currently in this folder.** The files here
are extracted, compiled Android app files, including `classes.dex`, native
libraries, and Flutter assets. The file `D:\pharma_crm\Test crasenim.apk` is a
decompiler export, not a normal installable APK.

The real application was built with **Flutter and Dart**, with an Android
platform wrapper. The compiled app files are not a practical substitute for
its Dart source. Do not edit `classes.dex`, `libapp.so`, or the files in this
folder to make app features or screen changes.

### Get the real Flutter project

Ask the app's developer or original builder for the **complete Flutter source
project**, either as a ZIP file or a Git repository. Tell them you need:

- `pubspec.yaml`
- `lib/` with the `.dart` source files
- `android/` with the Android Gradle project
- `assets/` and any other files referenced by `pubspec.yaml`
- Instructions for any required environment configuration

The files in this current folder are not that project, even though this folder
has a `lib/` directory: that directory contains compiled Android native
libraries, not Flutter's editable Dart source.

Once the developer provides the project, extract or clone it to a separate,
clearly named folder, for example:

```text
D:\pharma_crm\crasenim-mobile-source
```

Open **that source folder** in VS Code. Before making changes, confirm it has a
`pubspec.yaml` at its top level and `.dart` files under `lib/`.

> Cloning the existing `crasenim-mobile-app` repository in this workspace
> retrieves extracted app files, not the complete Flutter project.

## What we verified on the POCO

The connected phone was detected as a **POCO M6 Plus 5G** running **Android 15
(API 35)**. The extracted app's Android manifest specified a minimum Android API
of 24 and target API 36.

The supplied decompiler export could not be installed directly. A temporary
test-only APK was assembled from the compiled files and installed on the phone.
After adding the missing Android `INTERNET` permission to that temporary test
build, the app opened and displayed its role-selection screen.

This was only a launch/screen check:

- Login and other screens were not tested.
- The original APK and extracted project files were not changed.
- The temporary build tools and APK were deleted after testing.
- A locally re-signed test copy remains installed on the POCO. It is **not** the
  original release and is not available as an APK to share.
- ADB screen-tap testing was blocked by the phone, so automated taps could not
  be used.

The test indicates that the compiled app can launch on this Android 15 phone;
it does not replace testing the real source project or a proper release build.

## Set up development tools on a Windows laptop

Do this on the **laptop**, not on the phone:

1. Install **Git for Windows** if the project will be provided through Git.
2. Install **Visual Studio Code**.
3. Install **Flutter** by following the official
   [Flutter installation guide for Windows](https://docs.flutter.dev/get-started/install/windows).
   Follow the guide's instructions for adding Flutter's `bin` folder to your
   Windows `PATH`.
4. Install **Android Studio** from the
   [official Android Studio download page](https://developer.android.com/studio).
   In Android Studio's SDK Manager, install Android SDK Platform-Tools, Android
   SDK Build-Tools, and an Android platform suitable for the project.
5. In VS Code, install the **Flutter** extension (it will also install the
   Dart extension).
6. Open a new PowerShell window and check the setup:

   ```powershell
   flutter doctor -v
   flutter --version
   ```

   Follow `flutter doctor`'s instructions for any missing Android licenses or
   SDK components. Review the prompts before accepting licenses.

For Android license setup, Flutter may offer this command:

```powershell
flutter doctor --android-licenses
```

## Prepare the POCO for USB testing

The phone was previously connected successfully. If you need to set it up
again:

1. Enable Developer options from **Settings → About phone**. On Xiaomi/POCO
   software, tap the OS/MIUI version entry several times if necessary.
2. Open **Settings → Additional settings → Developer options** and enable **USB
   debugging**.
3. Connect the unlocked phone to the laptop and choose a USB mode that keeps it
   connected.
4. Approve the phone's **Allow USB debugging** prompt.
5. In PowerShell, check that ADB can see the phone:

   ```powershell
   adb devices -l
   ```

   The device should be listed with the status `device`, not `unauthorized`.
   The Platform-Tools folder previously downloaded on this laptop was:

   ```text
   C:\Users\chana\Downloads\platform-tools-latest-windows\platform-tools
   ```

   If `adb` is not on `PATH`, call it using its full path:

   ```powershell
   C:\Users\chana\Downloads\platform-tools-latest-windows\platform-tools\adb.exe devices -l
   ```

## Run the source project on your phone

After you have the real Flutter project, open PowerShell in the folder
containing `pubspec.yaml`. For the example path above:

```powershell
Set-Location D:\pharma_crm\crasenim-mobile-source
flutter pub get
flutter devices
flutter run
```

If Flutter finds your POCO, it will build a **debug** version, install it, and
open it. The first build can take several minutes while Android dependencies are
downloaded. Keep the phone unlocked and approve any prompts.

If you also want to check an Android 15 emulator, open Android Studio's **Device
Manager**, create a phone emulator using an **API 35 / Android 15** system image,
start that emulator, and run:

```powershell
flutter devices
flutter run
```

Choose the emulator if Flutter asks which device to use. A physical-device test
is still useful because the POCO's screen size, Android build, permissions, and
manufacturer settings can differ from an emulator.

## Make changes with VS Code or an AI coding assistant

1. Open the actual Flutter source folder (the one containing `pubspec.yaml`) in
   VS Code.
2. Make changes in the relevant Dart files under `lib/`. App images and other
   bundled files may be under `assets/`; check `pubspec.yaml` before adding new
   assets.
3. Ask the coding assistant to explain the change and run the relevant tests.
   Save a backup or use Git before accepting substantial changes.
4. Test on the POCO:

   ```powershell
   flutter run
   ```

5. Check available project tests:

   ```powershell
   Get-ChildItem -Recurse -File -Include *test.dart
   flutter test
   ```

Flutter hot reload may apply many Dart UI changes while `flutter run` is
running. Changes to native Android configuration or dependencies may require a
full restart or rebuild.

## Configure access to the backend

A phone cannot use `localhost` or `127.0.0.1` to reach a backend running on your
laptop: on the phone those addresses refer to the phone itself. Before testing
login or API-backed screens, confirm how the Flutter source sets its API base
URL and configure it for your environment.

For local Wi-Fi testing, the phone and laptop generally need to be on the same
trusted network. The app must use an address reachable from the phone, such as
the laptop's private Wi-Fi IPv4 address, for example:

```text
http://192.168.1.25:8080/myapp/api/
```

That is only an example; use the laptop's actual private IP and the backend's
actual port and API path. A Django development server normally listens only on
localhost unless it is explicitly started for LAN access. Allowing LAN access
can expose your development server to other devices on that network. Only do
this on a trusted network, and configure the Django host/security settings
appropriately. Do not expose a development server or weak test credentials to
the public internet.

The compiled app's runtime logs showed failed Google Fonts requests when the
test build lacked the `INTERNET` permission. The temporary test APK had that
permission added, but that did not change the real source project. In the
Flutter project, check the Android manifest and confirm the app requests
internet access when it needs API or font downloads.

## Build an APK for Android phones

Once the developer has provided the real Flutter source and it builds
successfully, build a release APK from the project root:

```powershell
flutter build apk --release
```

Flutter normally writes the APK here:

```text
build\app\outputs\flutter-apk\app-release.apk
```

Copy that file to the destination phone and install it, or use ADB for a
connected test phone:

```powershell
adb install -r .\build\app\outputs\flutter-apk\app-release.apk
```

To copy it to a convenient location:

```powershell
Copy-Item .\build\app\outputs\flutter-apk\app-release.apk D:\pharma_crm\Crasenim-Pharma.apk
```

You can then share `Crasenim-Pharma.apk` privately with Android users who trust
you. They may need to approve installing apps from the app they use to open the
APK. Do not send users your signing key or signing passwords.

## App signing and updates: important

An APK used for a real release needs to be signed with a release key. **Keep the
release signing key and its passwords safe, private, and backed up in a secure
place. Never commit them to Git, put them in this README, or send them to users
or an AI assistant.**

Android only permits an in-place app update when the new APK uses a compatible
signing key and the same Android application ID/package name. A different key
can prevent the new APK from updating the installed app. Uninstalling first may
work around a key mismatch, but normally removes the app's local data.

The POCO test copy was signed with a temporary local test key that was not
preserved. Consequently, it is not guaranteed to be upgradeable with the
developer's official release. If Android reports a signature conflict when
installing an official APK, first confirm important app data is backed up; then
uninstall the test copy from **Settings → Apps** before installing the official
one. Do not uninstall the test app unless you understand that it may erase its
local data.

Before producing APKs for other people, ask the original developer how the
release key and release build are managed. If you want to publish through
Google Play, follow Google's current Play App Signing process rather than
sharing an APK signing key.

## Troubleshooting

### `flutter` is not recognized

Flutter is not installed or its `bin` directory is not on Windows `PATH`.
Complete the official Flutter Windows setup, close PowerShell, open a new
window, and try `flutter doctor -v` again.

### `adb devices` shows `unauthorized`

Unlock the POCO and approve the USB-debugging dialog. If no dialog appears,
disconnect/reconnect the cable and check that USB debugging is enabled in
Developer options.

### Installation reports `INSTALL_FAILED_USER_RESTRICTED`

Check the unlocked POCO for an install confirmation. Xiaomi/POCO developer
settings may also require enabling **Install via USB**. Only change this setting
if you trust the test APK and intend to install it.

### The app installs but cannot sign in or load server data

Verify the phone can reach the configured backend address. Replace laptop-only
`localhost`/`127.0.0.1` values with a reachable address for testing, make sure
the backend is listening on the intended interface and port, and check
firewall, Django allowed-host, and CORS settings as appropriate. Never put
production passwords or private signing credentials in the mobile source.

### An update fails due to a signature conflict

The installed app and the new APK were probably signed with different keys.
Use the original release signing key to build a proper update. If this is only a
disposable test install, back up anything important before uninstalling it and
installing the other APK.

## Official references

- [Install Flutter on Windows](https://docs.flutter.dev/get-started/install/windows)
- [Set up Android development with Flutter](https://docs.flutter.dev/platform-integration/android/setup)
- [Build and release an Android app](https://docs.flutter.dev/deployment/android)
- [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools)
