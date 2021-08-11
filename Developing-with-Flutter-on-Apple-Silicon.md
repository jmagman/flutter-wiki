Flutter includes support for developing on macOS devices with [Apple Silicon (M1) hardware](https://www.apple.com/mac/m1/). This wiki page documents ongoing work relating to the Flutter toolchain providing native support for this processor architecture.

## Using macOS on Apple Silicon to develop Flutter apps (host)

You can use Apple Silicon-based Mac devices as a developer workstation (host) for building Flutter apps. While some tools still use the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), Apple Silicon-based Macs are supported as a host.

We recommend using Flutter 2 or later on Apple Silicon machines. Depending on your tolerance for risk, [you may be willing to experiment with the `beta` or `dev` channel](https://flutter.dev/docs/development/tools/sdk/upgrading#switching-flutter-channels) to take early advantage of further improvements as we build more native Apple Silicon support into the tooling.

[Issue 60118](https://github.com/flutter/flutter/issues/60118) tracks the full set of work to support this feature. 

### Other notes

- Deployment to physical iOS and Android devices attached to the host is supported, as well as support to the iOS simulator with `flutter run` or from Flutter IDE plugins. Deployment to the iOS simulator from Xcode is supported on version 2.1.0-12.0.pre and later as of [pull request 73828](https://github.com/flutter/flutter/pull/73828).  Simulator apps run natively as ARM64 apps on 2.4.0-4.0.pre and later as of [pull request 85642](https://github.com/flutter/flutter/pull/85642).
- There are several known Apple bugs for x86_64 (non-ARM64) apps running on the Simulator, including pasteboard and image picker failures, described in [issue 74970](https://github.com/flutter/flutter/issues/74970#issuecomment-858170914). Upgrade to 2.4.0-4.0.pre or later to avoid this bugs.
- Developing for the web (`flutter run -d chrome`) is working on all channels. It's recommended that you install the Apple Silicon version of Chrome. 
- The Android SDK Manager now offers an Android emulator that runs on Apple Silicon. The ARM64 images are also available in AVD Manager from the Other Images tab.
- CocoaPods crashes on ARM Macs if the x86 version of the ffi gem isn't installed. As of Flutter 2, we warn you of this and prompt you to install the `ffi` gem with the following line:

```
arch -x86_64 sudo gem install ffi
```

## Developing Flutter apps for macOS running on Apple Silicon (target)

Flutter now has [early release support for building macOS apps](https://flutter.dev/desktop), with beta snapshots available in the `stable` channel and ongoing development taking place. Compiled Intel macOS binaries work on Apple Silicon without change thanks to the [Rosetta 2 translation environment](https://developer.apple.com/documentation/apple_silicon/about_the_rosetta_translation_environment), which converts x86_64 instructions to ARM64 equivalents.

We plan to offer support for compilation directly to ARM64, as well as universal binaries that combine x86_64 and ARM64 assets. [Issue 60113](https://github.com/flutter/flutter/issues/60113) is the umbrella bug tracking this work.
