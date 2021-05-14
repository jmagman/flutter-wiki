*If you haven't already read it, start with [the repository structure overview](https://github.com/flutter/flutter/wiki/Plugins-and-Packages-repository-structure) to familiarize yourself with the types of packages discussed below.*

## Types of Tests

Because of the complexities of having native code, plugins have many more types of tests than most other Flutter packages. A plugin will generally have some combination of the following:
- **Dart unit tests**. All plugin packages should have these, with the exception of a federated implementation package that contains only native code (i.e., one that only implements a shared method channel). In a plugin, Dart unit tests would cover:
  - The Dart code in a monolithic plugin (generally using a mock method channel).
  - The Dart code in an app-facing plugin package (using a mock implementation of the platform interface).
  - The Dart code in a platform interface package that contains a shared method channel implementation (using a mock method channel).
  - The Dart code of an implementation package, if any. Some federated implementations are pure Dart, and would be largely tested via unit tests, and some have Dart code binding to a package-internal method channel.
  
  These should live in `test/`
- **Integration tests**. These use [the `integration_test` package](https://flutter.dev/docs/testing/integration-tests). Unlike Dart unit tests, integration tests run in the context of a Flutter application (the `example/` app), and can therefore load and exercise native plugin code. Almost all plugin packages other than the platform interface package should have these. The only exceptions would be:
  - Plugins that need native UI tests (see below)
  - Plugins that have pure Dart implementations which can be comprehensively tested with Dart unit tests.
  
  These should live in `example/integration_tests/`
- **Native unit tests**. Most plugins that have native code should have unit tests for that native code. (Currently, many do not; fixing that is currently a priority for plugins work.) They are written as:
  - Android: **JUnit** - These should live in `android/src/test/`
  - iOS: **XCTest** - These should live in `ios/Tests/`
  - Windows, macOS, Linux: **TBD**. Currently there are no native unit tests for these platform, but they will be added in the future (see [#82445](https://github.com/flutter/flutter/issues/82445)).
- **Native UI tests**. Some plugins show native UI that the test must interact with (e.g., `image_picker`). For these normal integration tests won't work, as there is not way to drive the native UI from Dart. They are written as:
  - Android: **Espresso**, via the [`espresso` plugin](https://pub.dev/packages/espresso) - These should live in `example/android/app/src/androidTest/`
  - iOS: **XCUITest** - These should live in `example/ios/RunnerUITests/`
  - Windows, macOS, and Linux: **TBD**, see [#70233 (Windows)](https://github.com/flutter/flutter/issues/70233), [#70234 (macOS)](https://github.com/flutter/flutter/issues/70234), and [#70235 (Linux)](https://github.com/flutter/flutter/issues/70235)

## Running Tests

### Dart unit tests

These can be run like any other Flutter unit tests, either from your preferred Flutter IDE, or using `flutter test`.

### Integration tests

To run the integration tests using Flutter driver (while in a plugin directory):

```sh
cd example
flutter drive --driver test_driver/integration_test.dart --target integration_test/<name_of_test_file>.dart
```

or use the [plugin tools `drive-examples` command](https://github.com/flutter/plugins/tree/master/script/tool#readme)
from the root of the repository:

```sh
dart run ./script/tool/lib/src/main.dart drive-examples --plugins=<name_of_plugin> --<platform>
```

To run integration tests as instrumentation tests on a local Android device:

```sh
cd example
flutter build apk
cd android && ./gradlew -Ptarget=$(pwd)/../test_driver/<name_of_plugin>_test.dart app:connectedAndroidTest
```

### JUnit

These can be run from Android Studio once the example app is opened as an Android project.

From a terminal (while in a plugin directory):

```sh
cd example
flutter build apk
cd android
./gradlew test
```

### XCTest and XCUITest

These can be [run from Xcode](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/05-running_tests.html)
once the example app is opened as an Xcode project.

From a terminal (while at the repository root), use the [plugin tools `xctest` command](https://github.com/flutter/plugins/tree/master/script/tool#readme):
```sh
dart run ./script/tool/lib/src/main.dart xctest --plugins=<some_plugin_name>
```

## Adding tests

A PR changing a plugin [should add tests](https://github.com/flutter/flutter/wiki/Tree-hygiene#tests). Hopefully the scaffolding to run the necessary kinds of tests are already in place for that plugin, in which case you just add tests to the existing files in the locations referenced above. If not you have several options:
- If it's simple to enable them, and you are comfortable making the changes, you can enable them as part of your PR.
- If it's non-trivial to enable them, but you are comfortable doing it, you can make a new PR that enables them with some minimal test, and ask for that to be reviewed and landed before continuing with your PR.
- If you aren't comfortable making the change, reach out [via Discord](https://github.com/flutter/flutter/wiki/Chat) and ask in #hackers-ecosystem.

Regardless of the approach you use, please reach out on Discord if a PR that sets up a new kind of test doesn't get reviewed within two weeks. Filling in the gaps in test scaffolding is a priority for the team, so we want to review such PRs quickly whenever possible.

See below for instructions on bringing up test scaffolding in a plugin (*does not yet cover all types of tests*):

### Enabling XCTests or XCUITests

1. Open <path_to_plugin>/example/ios/Runner.xcworkspace using XCode.
1. Create a new "Unit Testing Bundle" or "UI Testing Bundle", depending on the type of test.
1. In the target options window, populate details as following, then click on "Finish".
    * In the "product name" field, type "XCTests" or "RunnerUITests", depending on the type of test.
    * In the "Team" field, select "None".
    * In the Organization Name field, type in "Flutter". This should usually be pre-populated.
    * In the Organization Identifier field, type in "com.google". This should usually be pre-populated.
    * In the Language field, select "Objective-C".
    * In the Project field, select the xcodeproj "Runner" (blue color).
    * In the Target to be Tested, select xcworkspace "Runner" (white color).
1. Edit `example/ios/Podfile` to add the following to the `target 'Runner' do` block:

    ```ruby
    target 'XCTests' do
      inherit! :search_paths
      pod 'OCMock', '3.5'
    end
    ```
    (substituting `RunnerUITests` for `XCTests` for UI tests)
1. A XCTests/RunnerUITests folder should be created and you can start hacking in the added `.m` file.