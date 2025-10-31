# EdgeVision

Lightweight notes and quick setup for the EdgeVision Android project.

## About

This repository contains an Android project which uses native C++ code and expects an external OpenCV Android SDK to be added separately (to avoid committing large binary files to GitHub).

This repo also includes a small web viewer in the `viewer/` folder.

## What I added

- Project skeleton and build files (gradle, CMakeLists, gradle wrapper placeholder files)
- A minimal native source placeholder in `app/src/main/cpp/`
- Git LFS configured to track native libraries (`*.so`, `*.a`) to avoid pushing large binaries directly

## Important: OpenCV SDK (do this locally)

GitHub refuses very large files. To build and run the Android app you must download the OpenCV Android SDK yourself and copy the required runtime native libs into the project.

1. Download the OpenCV Android SDK (example):

   - https://opencv.org/releases/

2. Extract the SDK and copy only the runtime native libs you need into the project. Example destination:

   - `app/src/main/jniLibs/opencv/`  (place `armeabi-v7a`, `arm64-v8a`, `x86`, etc. subfolders or just the .so files you require)

3. If the SDK includes large archives (for example the full `opencv-android-sdk.zip`), do not add them to the repo — store them privately or reference a download link in your project wiki.

## Git LFS notes

This repository tracks `*.so` and `*.a` with Git LFS. If your machine doesn't have Git LFS installed, install it and run:

```powershell
git lfs install
git lfs pull
```

If you add additional native binary types, update `.gitattributes` or run `git lfs track "pattern"`.

## Android / build requirements

- Java JDK (matching your Gradle/Android plugin requirements)
- Android SDK with the following components installed (example):
  - Platform tools
  - Command-line tools
  - Android platform (e.g. android-33)
  - Build tools (e.g. 33.0.0)
  - NDK and CMake (if building native code)

## Quick build steps (Windows PowerShell)

1. Ensure OpenCV files are placed under `app/src/main/jniLibs/opencv/` as described above.
2. From the repository root (PowerShell):

```powershell
# check gradle wrapper
.\gradlew.bat --version

# Build debug APK
.\gradlew.bat assembleDebug

# If you have local.properties with sdk.dir set, Gradle will pick up Android SDK
```

If Gradle fails due to missing Android SDK components, install the missing pieces using SDK Manager or Android Studio.

## Build the web viewer

The web viewer is in `viewer/` and uses Node tooling. From the `viewer/` folder:

```powershell
cd viewer
npm install
npm run build
```

## Where to put OpenCV in the project (example)

Place the OpenCV runtime libs under:

```
app/src/main/jniLibs/opencv/<ABI>/*.so
```

Example ABIs: `armeabi-v7a`, `arm64-v8a`, `x86`.

## Next steps (recommended)

1. Install required Android SDK packages and NDK on your dev machine.
2. Download and add OpenCV runtime libs to `app/src/main/jniLibs/opencv/`.
3. Run `./gradlew.bat assembleDebug` and fix any build errors.

If you'd like, I can add a small script to automate copying the OpenCV files from a standard location into `jniLibs/` (if you can provide where you keep the SDK locally).

## License / attribution

Add your license here or a pointer to it.

---
Generated/added README by the repo maintainer assistant to explain how to add OpenCV and build on Windows.
# EdgeVision — Real-time Edge Detection Viewer

This repository contains an Android app (`app/`) that uses Camera → JNI → OpenCV (C++) → OpenGL ES to process camera frames in real time, plus a small TypeScript web viewer (`viewer/`) for viewing processed frames.

This branch is prepared so you can build locally. I completed the web viewer build for you. The Android project files, JNI/C++ code, and Gradle wrapper are present, but you must have the required toolchain installed locally to compile the Android app.

## What I did (quick)
- Completed app-level native sources (JNI/OpenCV), `CMakeLists.txt`, and Kotlin JNI wrapper.
- Created and fixed `app/build.gradle` and root `build.gradle`.
- Installed a working Gradle wrapper in the repo so you can run `gradlew.bat`.
- Built the web viewer and wrote `viewer/dist` (TypeScript -> JS) so you can open the web UI immediately.

## Prerequisites (must be present on your machine)
- Java JDK 11 (recommended) or 17. The Android Gradle Plugin used here expects Java 11 compatibility.
- Android SDK with:
   - command-line tools
   - platform-tools
   - platforms;android-33
   - build-tools;33.0.0
   - NDK (side by side) 25.2.9519653
   - CMake 3.22.1
- Node.js + npm (for web viewer) — already used to build `viewer` here.
- OpenCV Android SDK (download and extract; see below).

## Quick setup (copy/paste PowerShell)

1) Ensure SDK path in `local.properties` (edit this file if needed):

```powershell
$project = "C:\Users\Mukesh\Downloads\assignment\EdgeVision"
# Edit local.properties manually or recreate with the correct SDK path, example:
# sdk.dir=C:\Users\<YourUser>\AppData\Local\Android\Sdk
notepad $project\local.properties
```

2) Set JAVA_HOME to a JDK 11 installation (example):

```powershell
# For current PowerShell session only:
$env:JAVA_HOME = 'C:\Program Files\Adoptium\jdk-11.0.20'
# Or permanently (restart required):
# setx JAVA_HOME "C:\Program Files\Adoptium\jdk-11.0.20" -m
```

3) Place OpenCV Android SDK under the project (recommended):

```powershell
# Download and extract OpenCV for Android (manual steps or use browser)
# Then extract so the folder contains sdk/native/jni and sdk/native/libs
# Example destination:
# app/src/main/jniLibs/opencv
```

4) Build Android app (from repo root):

```powershell
cd C:\Users\Mukesh\Downloads\assignment\EdgeVision
# Clean + build
.\gradlew.bat clean assembleDebug --no-daemon
# Install to device/emulator
.\gradlew.bat installDebug
```

If the build fails with Java/Gradle plugin compatibility errors, set the Gradle java home in `gradle.properties` (project root):

```
org.gradle.java.home=C:\Program Files\Adoptium\jdk-11.0.20
```

## Web viewer (already built)
- Open `viewer/src/index.html` in a browser — it loads the compiled `../dist/app.js`.
- To rebuild locally:

```powershell
cd viewer
npm install
npm run build
# optionally: open viewer/src/index.html in a browser
```

## Troubleshooting (most common issues)

- Gradle plugin / Java mismatch: error about Java 11 vs Java 8 -> install JDK 11 and set `org.gradle.java.home` or `JAVA_HOME`.
- CMake/OpenCV errors: CMake can't find OpenCV. Make sure `app/src/main/jniLibs/opencv` contains the SDK or edit `app/CMakeLists.txt` to point to your OpenCV path.
- Network/dependency downloads failing: ensure access to `https://repo.maven.apache.org` and `https://dl.google.com` from your machine.

## Files of interest
- `app/` — Android project (sources, JNI C++ in `app/src/main/cpp`).
- `app/CMakeLists.txt` — CMake settings (includes OpenCV loc).
- `viewer/` — web viewer (source in `viewer/src`, compiled output in `viewer/dist`).
- `gradlew.bat`, `gradle/wrapper/gradle-wrapper.properties`, `gradle/wrapper/gradle-wrapper.jar` — Gradle wrapper.

## If you want me to try the Android build here (I can attempt), note:
- Installing missing SDK components and a JDK here will take more time and may require admin rights. If you want that, reply: `do A` and I'll try to run the full Android build on this machine.

Good luck — tell me if you want me to attempt the build here (`do A`) or if you want me to make any quick tweaks to the code before you build locally.

# EdgeVision Project

EdgeVision is an Android application that utilizes the device's camera to capture frames, processes them using OpenCV in native C++, and displays the processed images in a web viewer built with TypeScript.

## Features

- Camera integration using Camera2 API or CameraX
- Frame processing with OpenCV for edge detection
- Web viewer for displaying processed images

## Project Structure

```
EdgeVision
├── app
│   ├── src
│   │   ├── main
│   │   │   ├── java/com/edgevision
│   │   │   │   ├── MainActivity.kt
│   │   │   │   ├── camera
│   │   │   │   │   └── CameraManager.kt
│   │   │   │   └── processor
│   │   │   │       └── FrameProcessor.kt
│   │   │   ├── jni
│   │   │   │   ├── native-lib.cpp
│   │   │   │   └── CMakeLists.txt
│   │   │   ├── res
│   │   │   │   └── layout
│   │   │   │       └── activity_main.xml
│   │   │   └── AndroidManifest.xml
│   │   └── cpp
│   │       └── opencv
├── viewer
│   ├── src
│   │   ├── components
│   │   │   └── ImageViewer.tsx
│   │   ├── app.ts
│   │   └── index.html
│   ├── package.json
│   └── tsconfig.json
├── gradle
├── build.gradle
└── README.md
```

## Getting Started

### Prerequisites

- Android Studio
- NDK (Native Development Kit)
- OpenCV library

### Installation

1. Clone the repository:
   ```
   git clone <repository-url>
   ```

2. Open the project in Android Studio.

3. Sync the Gradle files.

4. Set up the NDK and OpenCV dependencies as specified in the `build.gradle` file.

### Running the Application

1. Connect an Android device or start an emulator.
2. Run the application from Android Studio.

### Web Viewer

To run the web viewer:

1. Navigate to the `viewer` directory.
2. Install dependencies:
   ```
   npm install
   ```
3. Start the web server:
   ```
   npm start
   ```

### Libraries Used

- OpenCV for image processing
- React for building the web viewer

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.