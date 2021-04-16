In general, our philosophy is to update the `stable` channel on a quarterly basis with feature updates. In the intervening period, occasionally we may decide a bug or regression warrants a hotfix. We tend to be extremely conservative with these hotfixes, since there's always a risk that fixing one bug introduces a new one, and we want the `stable` channel to always represent our most tested builds. 

We intend to announce hotfixes to the [flutter-announce](https://groups.google.com/forum/#!forum/flutter-announce) group, and we recommend that you subscribe to that list if you publish an application using Flutter. 

Note that we only hotfix the latest version -- if you see bugs on older versions of the `stable` channel, please consider moving to the latest `stable` channel version. 

To ensure that you have the latest stable version with the hotfixes listed below, use the flutter tool at the command line as follows:

```
$ flutter channel stable
$ flutter upgrade
```

## Flutter 2.0 Changes
### [2.0.5](https://github.com/flutter/flutter/pull/79486) (April 16, 2021)
This fixes the following issues:
 - https://github.com/dart-lang/sdk/issues/45306 - Segmentation fault on specific code.

### [2.0.4](https://github.com/flutter/flutter/pull/79486) (April 2, 2021)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/78589 - Cocoapod transitive dependencies with bitcode fail to link against debug Flutter framework
 - https://github.com/flutter/flutter/issues/76122 - Adding a WidgetSpan widget causes web HTML renderer painting issue
 - https://github.com/flutter/flutter/issues/75280 - Dragging the "draggable" widget causes widget to freeze in the overlay layer on Web

### [2.0.3](https://github.com/flutter/flutter/pull/78489) (March 19, 2021)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/75261 - Unable to deep link into Android app
 - https://github.com/flutter/flutter/issues/78167 - Flutter crash after going to version 2
 - https://github.com/flutter/flutter/issues/75677 - NoSuchMethodError: The method 'cancel' was called on null at AnsiSpinner.finish
 - https://github.com/flutter/flutter/pull/77419 - Fix Autovalidate enum references in fix data

### [2.0.2](https://github.com/flutter/flutter/pull/77850) (March 12, 2021)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/77251 - Flutter may show multiple snackbars when Scaffold is nested 
  - https://github.com/flutter/flutter/issues/75473 - CanvasKit throws error when using Path.from
  - https://github.com/flutter/flutter/issues/76597 - When multiple Flutter engines are active, destroying one engine causes crash
  - https://github.com/flutter/flutter/issues/75061 - '_initialButtons == kPrimaryButton': is not true
  - https://github.com/flutter/flutter/pull/77419 - Fix Autovalidate enum references in fix data
  - https://github.com/dart-lang/sdk/issues/45214 - Bad state exception can occur when HTTPS connection attempt errors or is aborted
  - https://github.com/dart-lang/sdk/issues/45140 - Uint8List reports type exception while using + operator in null safety mode

### [2.0.1](https://github.com/flutter/flutter/pull/77194) (March 5, 2021)
This fixes the following issue:
  - https://github.com/flutter/flutter/issues/77173 - Building for macOS target fails when Flutter is installed from website

### 2.0.0 (March 3, 2021)
Initial stable release.

## Flutter 1.22 Changes
### [1.22.6](https://github.com/flutter/flutter/pull/74355) (Jan 25, 2021)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/70895 - Build error when switching between dev/beta and stable branches.

### [1.22.5](https://github.com/flutter/flutter/pull/72079) (Dec 10, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/70577 - Reliability regression in the camera plugin on iOS

### [1.22.4](https://github.com/flutter/flutter/pull/70327) (Nov 13, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/43620 - Dart analyzer terminates during development
  - https://github.com/flutter/flutter/issues/58200 - Apple AppStore submission fails with error: “The bundle Runner.app/Frameworks/App.framework does not support the minimum OS Version specified in the Info.plist”
  - https://github.com/flutter/flutter/issues/69722 - Setting a custom observatory port for debugging does not take effect
  - https://github.com/flutter/flutter/issues/66144 - Setting autoFillHint to text form field may cause focus issues
  - https://github.com/flutter/flutter/issues/69449 - Potential race condition in FlutterPlatformViewsController
  - https://github.com/flutter/flutter/issues/65133 - Support targeting physical iOS devices on Apple Silicon

### [1.22.3](https://github.com/flutter/flutter/pull/69234) (October 30, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/67828 - Multiple taps required to delete text in some input fields.
  - https://github.com/flutter/flutter/issues/66108 - Reading Android clipboard may throw a security exception if it contains media

### [1.22.2](https://github.com/flutter/flutter/pull/68135)  (October 16, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/67869 - Stylus tap gesture is improperly registered.
  - https://github.com/flutter/flutter/issues/67986 - Android Studio 4.1 not properly supported.
  - https://github.com/flutter/flutter/issues/67213 - Webviews in hybrid composition can cause a crash.
  - https://github.com/flutter/flutter/issues/67345 - VoiceOver accessibility issue with some pages.
  - https://github.com/flutter/flutter/issues/66764 - Native webviews may not be properly disposed of in hybrid composition.

### [1.22.1](https://github.com/flutter/flutter/pull/67552) (October 8, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/66940 - autovalidate property inadvertently removed.
  - https://github.com/flutter/flutter/issues/66962 - The new --analyze-size flag crashes when used with --split-debug-info
  - https://github.com/flutter/flutter/issues/66908 - Flutter Activity causing exceptions in some Android versions.
  - https://github.com/flutter/flutter/issues/66647 - Layout modifications performed by background threads causes exceptions on IOS14.

### 1.22.0 (October 1, 2020)
Initial stable release.

## Flutter 1.20 Changes
### [1.20.4](https://github.com/flutter/flutter/pull/65787) (September 15, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/64045 - Cannot deploy to physical device running iOS 14

### [1.20.3](https://github.com/flutter/flutter/pull/64984) (September 2, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/63876 - Performance regression for Image animation.
  - https://github.com/flutter/flutter/issues/64228 - WebView may freeze in release mode on iOS.
  - https://github.com/flutter/flutter/issues/64414 - Task switching may freeze on some Android versions.
  - https://github.com/flutter/flutter/issues/63560 - Building AARs may cause a stack overflow.
  - https://github.com/flutter/flutter/issues/57210 - Certain assets may cause issues with iOS builds.
  - https://github.com/flutter/flutter/issues/63590 - Passing null values from functions run via Isolates throws an exception.
  - https://github.com/flutter/flutter/issues/63427 - Wrong hour/minute order in timePicker in RTL mode.

### [1.20.2](https://github.com/flutter/flutter/pull/63591) (August 13, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/63038 - Crash due to serialization of generic DartType (UnknownType)
  - https://github.com/flutter/flutter/issues/46167 - iOS platform view cancels gesture while a new clip layer is added during the gesture
  - https://github.com/flutter/flutter/issues/62198 - SliverList throws Exception when first item is SizedBox.shrink()
  - https://github.com/flutter/flutter/issues/59029 - build ios --release can crash with ArgumentError: Invalid argument(s)
  - https://github.com/flutter/flutter/issues/62775 - TimePicker is not correct in RTL (right-to-left) languages
  - https://github.com/flutter/flutter/issues/55535 - New DatePicker widget is not fully  localized
  - https://github.com/flutter/flutter/issues/63373 - Double date separators appearing in DatePicker, preventing date selection
  - https://github.com/flutter/flutter/issues/63176 -  App.framework path in Podfile incorrect

### [1.20.1](https://github.com/flutter/flutter/pull/62990) (August 6, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/60215 - Creating an Android-only plug-in creates a no-op iOS folder.

### 1.20.0 (August 5, 2020)
Initial stable release.

## Flutter 1.17 Changes
### [1.17.5](https://github.com/flutter/flutter/pull/60611) (June 30, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter-intellij/issues/4642  - Intellij/Android Studio plugins fail to show connected Android devices.

### [1.17.4](https://github.com/flutter/flutter/pull/59695) (June 18, 2020)
This fixes the following issues:
  - https://github.com/flutter/flutter/issues/56826  - xcdevice polling may use all free hard drive space

### [1.17.3](https://github.com/flutter/flutter/pull/58646) (June 4, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/54420  - Exhausted heap space can cause machine to freeze

### [1.17.2](https://github.com/flutter/flutter/pull/58050) (May 28, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/57326  - CupertinoSegmentedControl does not always respond to selections
 - https://github.com/flutter/flutter/issues/56898 - DropdownButtonFormField is not re-rendered after value is changed programmatically
 - https://github.com/flutter/flutter/issues/56853 - Incorrect git error may be presented when flutter upgrade fails
 - https://github.com/flutter/flutter/issues/55552 - Hot reload may fail after a hot restart
 - https://github.com/flutter/flutter/issues/56507 - iOS builds may fail with “The path does not exist” error message

### [1.17.1](https://github.com/flutter/flutter/pull/57052) (May 13, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/26345 - Updating `AndroidView` layer tree causes crash on Xiaomi and Moto devices
 - https://github.com/flutter/flutter/issues/56567 - Xcode legacy build system causes build failures on iOS
 - https://github.com/flutter/flutter/issues/56473 - Build `--tree-shake-icons` build option crashes computer
 - https://github.com/flutter/flutter/issues/56688 - Regression in `Navigator.pushAndRemoveUntil`
 - https://github.com/flutter/flutter/issues/56479 - Crash while getting static type context for signature shaking

### 1.17.0 (May 5, 2020)
Initial stable release.

## Flutter 1.12 Changes
### Hotfix.9 (April 1, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/47819 - Crashes on ARMv8 Android devices
 - https://github.com/flutter/flutter/issues/49185 - Issues using Flutter 1.12 with Linux 5.5
 - https://github.com/flutter/flutter/issues/51712 - fixes for licensing from Android sdkmanager tool not being found

### [Hotfix.8](https://github.com/flutter/flutter/pull/50591) (February 11, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/50066 - binaries unsigned in last hotfix
 - https://github.com/flutter/flutter/issues/49787 - in a previous hotfix, we inadvertently broke Xcode 10 support. Reverting this change would have caused other problems (and users would still have to upgrade their Xcode with the next stable release), we decided to increase our minimum supported Xcode version. Please see the linked issue for more context on this decision.
 - https://github.com/flutter/flutter/issues/45732 & https://github.com/flutter/flutter/issues/47609, two related Android log reader fixes.

### [Hotfix.7](https://github.com/flutter/flutter/pull/49437) (January 26, 2020)
This includes the following fixes: 
- https://github.com/flutter/flutter/issues/47164 - blackscreen / crash on certain Huawei devices
- https://github.com/flutter/flutter/issues/47804 - Flutter engine crashes on some Android devices due to "Failed to setup Skia Gr context"
- https://github.com/flutter/flutter/issues/46172 - reportFullyDrawn causes crash on Android KitKat

### Hotfix.5 (December 11, 2019)
Initial stable release.