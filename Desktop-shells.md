Work is ongoing to extend Flutter to support desktop as a target environment, allowing developers to create macOS, Windows, and Linux applications with Flutter.

## Current Status

A high-level overview of the status of each platform is provided below. For details see
[the list of desktop-related bugs](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22a%3A+desktop%22),
as well as [the embedding source](https://github.com/flutter/engine/tree/master/shell/platform/).

### Linux

The Linux embedding is in alpha. For Linux, see the
[flutter.dev documentation](https://flutter.dev/desktop), rather than this page, for instructions and
information. 

The Linux embedding is GTK-based. Ideally we would like to create a library that allows embedding Flutter regardless of whether you're using GTK, Qt, wxWidgets, Motif, or another arbitrary toolkit for other parts of your application, but have not yet determined a good way to do that. Support for other toolkits may be added in the future.

### macOS

The macOS embedding is in alpha. For macOS, see the
[flutter.dev documentation](https://flutter.dev/desktop), rather than this page, for instructions and
information.

### Windows

The Windows embedding is an early technical preview. It is Win32-based, but we are exploring adding a UWP
version as well.

The APIs for the final embedding may be significantly different from the current API surface.

### Plugins

Writing plugins is supported on all platforms, however many plugins do not yet have
desktop support.

## Tooling

Support for Windows in the `flutter` tool is a work in progress. To use it, you must be on the `master` [Flutter channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels), and you must enable the feature with `flutter config --enable-windows-desktop`

Run `flutter config` to see your current settings, as well as the commands to disable the feature again.

### `doctor`

Once you've enabled Windows support, running `flutter doctor` will check for the necessary build tools. Ensure that it reports no issues in the Windows section before trying to `build` or `run`.

### `create`

`create` is supported (`app` and `plugin` templates only):
* You can add Windows support to an existing app by running `flutter create .` in the project. (You will likely want to revert changes to the `ios` and `android` directories after re-running `create`.)
* You can add Windows support to an existing plugin by running `flutter create --platforms=windows .` in the project.
  * When adding Windows support to an existing plugin, be sure to update [the `platforms` map in `pubspec.yaml`](https://flutter.dev/docs/development/packages-and-plugins/developing-packages#plugin-platforms) as instructed in the output.

### `run` and `build`

`flutter run` and `flutter build` are supported once you have added the necessary platform directory to your project (see `create` above).

Cross-compilation is not supported for desktop. You must be on Windows to build for Windows.

### IDEs ###

If you have enabled Windows support in the tool and added Windows support to your project as described above, your machine should appear as an available device in Android Studio or VS Code for that project. The standard Flutter build and run workflows should then automatically work for Windows as well.

## Distributing

Keep in mind that the Windows embedding is in early stages, and is missing critical functionality such as OS accessibility integration, so we **strongly recommend against distributing applications to end users** at this stage.

There are not yet full instructions, or tooling support, for making distributable applications. However, below is some information about how to use the current build output on other machines for testing purposes.

The executable will be in `build\windows\runner\<build mode>\`. In addition to that executable, you will need:
- From the same directory:
  - all the `.dll` files
  - the `data` directory
- The [Visual C++ redistributables](https://docs.microsoft.com/en-us/cpp/windows/redistributing-visual-cpp-files?view=vs-2019). You can use any of the methods shown in the [example walkthroughs here](https://docs.microsoft.com/en-us/cpp/windows/deployment-examples?view=vs-2019). If you use the application-local option, you will need to copy:
  - `msvcp140.dll`
  - `vcruntime140.dll`
  - `vcruntime140_1.dll`

## Add-to-App

Currently there is no support for Add-to-App for any desktop platform. If you are familiar with doing native development on your platform(s), it is possible to integrate the desktop Flutter libraries in your own app. There is not currently much guidance, so you will be well off the beaten path, but the information below will help get you started.

### Getting the Libraries

Unless you want to build the Flutter engine from source, you will need a prebuilt library. The easiest way to get the right version for your version of Flutter is to run `flutter precache` with the `--linux`, `--macos`, or `--windows` flag (depending on your platform). They will be downloaded to `bin/cache/artifacts/engine/` under your Flutter tree.

### C++ Wrapper

The Windows library provides a C API. To make it easier to use, there is a C++ wrapper available
which you can build into your application to provide a higher-level API surface. The source for it is downloaded with the library. You will need to build it as part of your application.

### Documentation

See the headers that come with the library (or wrapper) for your platform for information on using them. It may be helpful to look at the basic application produced by `flutter create` to see how it calls the APIs.

### Building

In addition to linking the Flutter library, your application will need to bundle your Flutter assets (as created by `flutter build bundle`). On Windows and Linux you will also need the ICU data from the Flutter engine
(look for `icudtl.dat` under the `bin/cache/artifacts/engine` directory in your Flutter tree).