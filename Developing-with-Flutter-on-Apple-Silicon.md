Flutter includes preliminary support for developing on macOS devices with [Apple Silicon (M1) hardware](https://www.apple.com/mac/m1/). This wiki page documents limitations and temporary workarounds as we complete work for this new architecture.

## Using macOS on Apple Silicon to develop Flutter apps (host)

You can use Apple Silicon-based Mac devices as a developer workstation (host) for building Flutter apps. While some support is in a preliminary stage or requires use of the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), it's [possible to develop and build on Apple Silicon at this time](https://twitter.com/timsneath/status/1340423554702090245). 

Baseline support for Apple Silicon is available in the current stable channel release (Flutter 1.22.4+), which contains required hotfixes to support this hardware. However, depending on your tolerance for risk, [we recommend use of the `beta` or `dev` channel](https://flutter.dev/docs/development/tools/sdk/upgrading#switching-flutter-channels) to take early advantage of improvements and fixes as they become available.

[Issue 60118](https://github.com/flutter/flutter/issues/60118) tracks the full set of work to support this feature. 

### Other notes

- Deployment to physical iOS and Android devices attached to the host is supported, but we do not yet support deployment to the iOS simulator ([issue 64502](https://github.com/flutter/flutter/issues/64502)).
- Developing for the web (`flutter run -d chrome`) is working on supported channels (`beta`, `dev` and `master`). It's recommended that you install the Apple Silicon version of Chrome. 
- The Android Emulator has preliminary support for on Apple Silicon, [as noted here](https://androidstudio.googleblog.com/2020/12/android-emulator-apple-silicon-preview.html).
- Some IDEs and editors may not be fully stable on Apple Silicon. 
  - Visual Studio Code offers an Apple Silicon build [in the Insiders channel](https://code.visualstudio.com/insiders/), and is [stabilizing support for the January 2021 release](https://github.com/microsoft/vscode/labels/%3Aapple%3A%20si). The [Flutter and Dart plugins](https://dartcode.org/) are supported on this release. 
  - Android Studio runs under Rosetta 2 at this time. JetBrains are working on Apple Silicon native builds of their tools, with preliminary Apple Silicon builds of IntelliJ [available for testing](https://youtrack.jetbrains.com/issue/JBR-2526).
- Running CocoaPods to embed plugins fails in the current release. As [documented here](https://github.com/flutter/flutter/issues/70796), the workaround is to run the following command line to install support for FFI from the x86_64 environment:

```
arch -x86_64 sudo gem install ffi
```

## Developing Flutter apps for macOS running on Apple Silicon (target)

Flutter already has [preliminary support for building macOS apps](https://flutter.dev/desktop), currently available in the `dev` and `master` channels. Compiled Intel macOS binaries work on Apple Silicon without change thanks to the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), which converts x86_64 instructions to arm64 equivalents.

We plan to offer support for compilation directly to arm64, as well as universal binaries that combine x86_64 and arm64 assets. [Issue 60113](https://github.com/flutter/flutter/issues/60113) is the umbrella bug tracking this work.
