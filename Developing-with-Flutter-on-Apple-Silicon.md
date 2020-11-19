Flutter includes preliminary support for developing on macOS devices with [Apple Silicon hardware](https://www.apple.com/mac/m1/). This wiki page documents limitations and temporary workarounds as we complete work for this new architecture.

## Using macOS on Apple Silicon to develop Flutter apps (host)

You can use Apple Silicon-based Mac devices as a developer workstation (host) for building Flutter apps. 

[Issue 60118](https://github.com/flutter/flutter/issues/60118) tracks the full set of work to support this feature. 

Notes and caveats:

- We recommend at least Flutter 1.22.4, which contains required hotfixes to support this hardware. While developing on Apple Silicon hardware, [you may prefer to use the `beta` or `dev` channel](https://flutter.dev/docs/development/tools/sdk/upgrading#switching-flutter-channels) to take advantage of improvements as they are available.
- Deployment to physical iOS and Android devices attached to the host is supported, but we do not yet support deployment to the iOS simulator ([issue 64502](https://github.com/flutter/flutter/issues/64502)).
- The Android Emulator is not yet supported on Apple Silicon, [as noted here](https://developer.android.com/studio/releases/emulator#emulator_for_arm64_hosts).
- Some IDEs and editors may not be fully supported on Apple Silicon. [Visual Studio Code has experimental support for Apple Silicon](https://github.com/microsoft/vscode/labels/%3Aapple%3A%20si), including support for the Flutter and Dart plugins. [JetBrains is tracking Apple Silicon support here](https://youtrack.jetbrains.com/issue/JBR-2526).
- Running CocoaPods to embed plugins fails in the current release. As [documented here](https://github.com/flutter/flutter/issues/70796), the workaround is to run the following command line to install support for FFI from the x86_64 environment:

```
arch -x86_64 sudo gem install ffi
```

## Developing Flutter apps for macOS running on Apple Silicon (target)

Flutter already has [preliminary support for building macOS apps](https://flutter.dev/desktop), currently available in the `dev` and `master` channels. Compiled Intel macOS binaries work on Apple Silicon without change thanks to the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), which converts x86_64 instructions to arm64 equivalents.

We plan to offer support for compilation directly to arm64, as well as universal binaries that combine x86_64 and arm64 assets. [Issue 60113](https://github.com/flutter/flutter/issues/60113) is the umbrella bug tracking this work.
