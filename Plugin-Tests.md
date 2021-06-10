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
  - iOS: **XCTest** - These should live in `example/ios/RunnerTests/` (**Note**: These are in the example directory, not the main package directory, because they are run via the example app's project)
  - Windows, macOS, Linux: **TBD**. Currently there are no native unit tests for these platform, but they will be added in the future (see [#82445](https://github.com/flutter/flutter/issues/82445)).
- **Native UI tests**. Some plugins show native UI that the test must interact with (e.g., `image_picker`). For these normal integration tests won't work, as there is not way to drive the native UI from Dart. They are written as:
  - Android: **Espresso**, via the [`espresso` plugin](https://pub.dev/packages/espresso) - These should live in `example/android/app/src/androidTest/`
  - iOS: **XCUITest** - These should live in `example/ios/RunnerUITests/`
  - Windows, macOS, and Linux: **TBD**, see [#70233 (Windows)](https://github.com/flutter/flutter/issues/70233), [#70234 (macOS)](https://github.com/flutter/flutter/issues/70234), and [#70235 (Linux)](https://github.com/flutter/flutter/issues/70235)

## Running Tests

### Dart unit tests

These can be run like any other Flutter unit tests, either from your preferred Flutter IDE, or using `flutter test`.

Unit tests are located in the `test` directory of the plugin package.

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

### Web Tests

Most web tests are written as Integration Tests because they need a web browser (like Chrome) to run. Web integration tests are located in the `example` directory of the `<plugin_name_web>` package.

To run web integration tests:

1. Check what version of Chrome is running on the machine you're running tests on.
2. Download and install the ChromeDriver for that version from here:
    * https://chromedriver.chromium.org/downloads
3. Start ChromeDriver with: 
```sh
chromedriver --port=4444
```
4. Run tests:
    * **All:** from the root `plugins` directory, run:
    ```sh
    dart ./script/tool/lib/src/main.dart drive-examples --plugins=<plugin_name>/<plugin_name_web> --web
    ``` 
    * **One:** `cd` into the `example` directory of the package, then run:
    ```sh
    flutter drive -d web-server --web-port 7357 --browser-name chrome --driver test_driver/integration_test.dart --target integration_test/NAME_OF_YOUR_test.dart
    ```

All web packages contain a standard `test` directory in the root of the package that can be run with `flutter test`. In most cases, the test there will direct users to the Integration Tests that live in the `example` directory. In some cases (like `file_selector_web`), the directory contains actual Unit Tests that are not web-specific and can run in the Dart VM.

#### Mocks

Some packages (like `google_maps_flutter_web`) contain `.mocks.dart` files alongside the test files that use them.

Mock files are [generated by `package:mockito`](https://pub.dev/packages/mockito#lets-create-mocks). The contents of mock files can change with how the mocks are used within the tests, in addition to actual changes in the APIs they're mocking.

Mock files must be updated by running the following command:

```sh
flutter pub run build_runner build
```


## Adding tests

A PR changing a plugin [should add tests](https://github.com/flutter/flutter/wiki/Tree-hygiene#tests). Which type(s) of tests you should add will depend on what exactly you are changing. If you're not sure, please ask in your PR or in `#hackers-ecosystem` [on Discord](https://github.com/flutter/flutter/wiki/Chat). 
Hopefully the scaffolding to run the necessary kinds of tests are already in place for that plugin, in which case you just add tests to the existing files in the locations referenced above. If not, see below for more information about adding test scaffolding.

### FAQ: Do I need to add tests even if the part of the code I'm changing isn't already tested?

**Yes.** Much of the plugin code predates the current strict policy about testing, and as a result the coverage is not as complete as it should be. The goal of having a strict policy going forward is to ensure that the situation improves.

### Adding test scaffolding

If a plugin is missing test scaffolding for the type of tests you want to add, you have several options:
- If it's simple to enable them, and you are comfortable making the changes, you can enable them as part of your PR.
- If it's non-trivial to enable them, but you are comfortable doing it, you can make a new PR that enables them with some minimal test, and ask for that to be reviewed and landed before continuing with your PR.
- If you aren't comfortable making the change, reach out [via Discord](https://github.com/flutter/flutter/wiki/Chat) and ask in `#hackers-ecosystem`.

Regardless of the approach you use, please reach out on Discord if a PR that sets up a new kind of test doesn't get reviewed within two weeks. Filling in the gaps in test scaffolding is a priority for the team, so we want to review such PRs quickly whenever possible.

See below for instructions on bringing up test scaffolding in a plugin (*does not yet cover all types of tests*):

#### Enabling XCTests or XCUITests

1. Open `<path_to_plugin>/example/ios/Runner.xcworkspace` using Xcode. (For macOS, replace `ios` with `macos`.)
1. Create a new "Unit Testing Bundle" or "UI Testing Bundle", depending on the type of test.
1. In the target options window, populate details as following, then click on "Finish".
    * In the "product name" field, type "RunnerTests" or "RunnerUITests", depending on the type of test.
    * In the "Team" field, select "None".
    * Set the Organization Identifier to "dev.flutter.plugins".
    * In the Language field, select "Objective-C" for iOS, or "Swift" for macOS.
    * In the Project field, select the xcodeproj "Runner" (blue color).
    * In the Target to be Tested, select xcworkspace "Runner" (white color).
    * In the Build Settings tab, remove most of the target-level overrides that are generated by the
      template. In particular:
        * `CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER` is currently incompatible with Flutter.
        * `IPHONEOS_DEPLOYMENT_TARGET` and `TARGETED_DEVICE_FAMILY` may cause issues running tests.
        * The compiler settings (`CLANG_*`, `GCC_*`, and `MTL_*`) shouldn't be needed.
1. Edit `example/ios/Podfile` (`example/macos/Podfile` for macOS) to add the following to the `target 'Runner' do` block:

    ```ruby
    target 'RunnerTests' do
      inherit! :search_paths
      pod 'OCMock', '3.5'
    end
    ```
    (substituting `RunnerUITests` for `RunnerTests` for UI tests). The `OCMock` line is only necessary if your tests use OCMock.
1. A RunnerTests/RunnerUITests folder should be created and you can start hacking in the added `.m`/`.swift` file.