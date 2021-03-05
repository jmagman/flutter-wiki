Flutter includes support for developing on macOS devices with [Apple Silicon (M1) hardware](https://www.apple.com/mac/m1/). This wiki page documents limitations and temporary workarounds as we and other tools we depend on complete native support for this processor architecture.

## Using macOS on Apple Silicon to develop Flutter apps (host)

You can use Apple Silicon-based Mac devices as a developer workstation (host) for building Flutter apps. While some support requires use of the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), it's [possible to develop and build on Apple Silicon at this time](https://twitter.com/timsneath/status/1340423554702090245). 

We recommend using Flutter 2 on Apple Silicon machines, since it contains a number of fixes and improvements designed for this scenario. Depending on your tolerance for risk, [you may be willing to experiment with the `beta` or `dev` channel](https://flutter.dev/docs/development/tools/sdk/upgrading#switching-flutter-channels) to take early advantage of further improvements as we build more native Apple Silicon support into the tooling.

[Issue 60118](https://github.com/flutter/flutter/issues/60118) tracks the full set of work to support this feature. 

### Other notes

- Deployment to physical iOS and Android devices attached to the host is supported, but we do not yet support deployment to the iOS simulator ([issue 64502](https://github.com/flutter/flutter/issues/64502)).
- Developing for the web (`flutter run -d chrome`) is working on all channels. It's recommended that you install the Apple Silicon version of Chrome. 
- The Android Emulator [has preliminary support for Apple Silicon](https://github.com/google/android-emulator-m1-preview).
- Some IDEs and editors may not be fully stable on Apple Silicon. 
  - Visual Studio Code offers support for Apple Silicon as of March 2021, and the [Flutter and Dart plugins](https://dartcode.org/) are supported on this release. 
  - Android Studio runs under Rosetta 2 at this time. JetBrains are working on Apple Silicon native builds of their tools, with preliminary Apple Silicon builds of IntelliJ [available for testing](https://youtrack.jetbrains.com/issue/JBR-2526).
- CocoaPods crashes on ARM Macs if the x86 version of the ffi gem isn't installed. As of Flutter 2, we warn you of this and prompt you to install the `ffi` gem with the following line:

```
arch -x86_64 sudo gem install ffi
```

## Developing Flutter apps for macOS running on Apple Silicon (target)

Flutter now has [early release support for building macOS apps](https://flutter.dev/desktop), with beta snapshots available in the `stable` channel and ongoing development taking place. Compiled Intel macOS binaries work on Apple Silicon without change thanks to the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), which converts x86_64 instructions to arm64 equivalents.

We plan to offer support for compilation directly to arm64, as well as universal binaries that combine x86_64 and arm64 assets. [Issue 60113](https://github.com/flutter/flutter/issues/60113) is the umbrella bug tracking this work.
