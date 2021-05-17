# Deferred Components

Deferred components is a set of features and tooling that allows developers to write dart code that can be AOT compiled into separate split dynamic libraries as well as package them together with assets into runtime downloadable modules on Android. The primary goal of deferred components is to reduce install apk size as well as reduce storage space taken up by the app by omitting features the user may not use.

This feature is currently only available on Android, taking advantage of Android and Google Play Store’s dynamic feature modules to deliver the deferred components packaged as Android modules. This does not impact other platforms, which continue to build as normal with all deferred components and assets included at initial install time.

Though modules can be defer loaded, the entire application must be completely built and uploaded as a single Android App Bundle (`.aab`). Dispatching partial updates without re-uploading new Android App Bundles for the entire application (eg, code push) is not supported.

This feature only does deferred loading in release and profile mode. In debug mode, all deferred components are present at launch and will instantly load.

A step by step guide on how to integrate deferred components with your app can be found on the [Flutter.dev documentation](https://flutter.dev/docs/perf/deferred-components). 

## APK size gallery case study

Flutter Gallery implements deferred components for the crane study. Here, we will examine the initial install size gains in a [fully deferred Flutter gallery branch](https://github.com/flutter/gallery/tree/fully-deferred-gallery) (branch not kept up to date with master) where we implement deferred components for all studies and demos (not just crane). In this example, we have moved all the splittable assets and fonts into deferred components. Compared to a non-deferred components application, the initial installed APK file size breakdown is as follows:

Deferred components:

* base-arm64_v8a.apk - 12,325,372 bytes
* base-master.apk - 37,889,309 bytes
* Total deferred components initial installation size: 50,214,681 bytes

Non-deferred components (regular app):

* base-arm64_v8a.apk - 12,521,900 bytes
* base-master.apk - 80,605,796 bytes
* Total regular initial installation size: 93,127,696 bytes

Here, we can see a ~200kB decrease in compiled dart code size (base-arm64_v8a.apk) and a ~43mB decrease in assets (base-master.apk) on initial download. Overall, there is a 46% reduction in initial installation size. The dart code, assets, and google fonts are instead moved into separate components downloaded at runtime only when needed:

* Crane
* Fortnightly
* Rally
* Shrine
* Cupertino
* Material
* Reference

The total installed size with all components installed is slightly larger than the non-deferred app, but this increase is on the order of a few kb due to overhead in the dart shared libraries from ELF headers and cross-loading-unit calls.

## Deferred components app structure

The deferred Dart libraries are interpreted by `gen_snapshot` (Dart’s compiler) to produce “loading units”, each of which are output as a split AOT shared library (`.so` file) when building in profile or release mode. A loading unit is the smallest set of libraries that are imported exclusively with the deferred keyword by the main code and can be split off of the base libraries.

The following diagram shows an example app structure and a “lifecycle” of how deferred dart libraries are compiled into loading units and packaged into a `.aab` file.

![](https://raw.githubusercontent.com/flutter/engine/master/docs/deferred_components_architecture.svg)

This example app has the following properties:

* Four Dart libraries, with Dart libraries `lib1` and `lib2` dependent on each other. `lib1`, `lib3`, and `lib4` are imported in the flutter app’s main code as deferred.
* Four loading units, with one being the base loading unit with id 1 and loading unit 2 containing both `lib1` and `lib2`. Loading units 3 and 4 contain `lib3` and `lib4` respectively.
* Three defined deferred components, plus an implicit base component. `component1` contains loading unit 2 and assets. `component2` contains both loading units 3 and 4 and no assets. `component3` is an assets only component.
* `app-release.aab` is the completed build output file, and contains all three components as well as the base component.

There will always be an implicit base loading unit that contains core flutter packages as well as your base application code. Any libraries that are not fully imported as deferred by your base app code will be included in the base loading unit. If no additional loading units other than base are generated, it likely means you imported your files incorrectly.

Multiple Dart libraries are compiled as a single loading unit if they import each other eagerly (non-deferred):

![](https://raw.githubusercontent.com/flutter/engine/master/docs/dart_split_aot_compilation.svg)

## Custom Implementations

It is possible to write a custom implementation that bypasses the Android Play store. This is only recommended for advanced developers and is primarily aimed at apps with very unique needs such as extremely large asset components, specific download behavior, or distribution in a region that does not have access to the Play store (eg, China).

### Overview

Advanced developers and those in China may wish to bypass the use of the Play store as the distribution method for deferred components. The Flutter embedder allows custom implementations that handle customer-unique download and unpacking of deferred components while still allowing access to the core Dart callbacks that register a loading unit with the Dart runtime. This process is far more involved than the default play store version.

To implement a custom deferred components system, the following major pieces will be required:

* Android embedder implementation of `DeferredComponentManager` that handles communication between the app and the server as well as extracting the `.so` file and assets from the downloaded component.
* Tooling to package the components in a way that is compatible with your `DeferredComponentManager` and to interpret `gen_snapshot` output of loading units.
* A server to host the downloadable components. Without the Play store acting as a distributor for Android dynamic feature modules, this must be custom.

The following sections provide a high level guide of what needs to be done in a custom implementation.

### DeferredComponentManager - Android embedder

The embedder is responsible for downloading and installing the packaged component files. This can be done by implementing the abstract class DeferredComponentManager in the Android embedder.

The entry point into this class is `installDeferredComponent` which provides both a loading unit id and the component name to help determine what to install. `loadLibrary()` calls will pass only a loading unit id while `DeferredComponent.installDeferredComponent()` calls from the framework services package will pass only a component name to load an assets-only component.

In order to resolve a specific component from the loading unit id, it is typically necessary to store a mapping of loading unit ids to the component name. In the default implementation, we accomplish this by storing a string meta-data in the base app’s `AndroidManifest.xml`, but this can be accomplished in any way desired.

You may find detailed explanations of each method in `DeferredComponentManager`  in the engine source file at `shell/platform/android/io/flutter/embedding/engine/deferredcomponents/DeferredComponentManager.java`. The default Play store implementation is found at `shell/platform/android/io/flutter/embedding/engine/deferredcomponents/PlayStoreDeferredComponentManager.java` and can be used as a rough guide on what needs to be implemented.

To load Dart libraries, call `FlutterJNI.loadDartDeferredLibrary` with the loading unit id and a list of paths that potentially contain the `.so` file in your `loadDartLibrary` implementation. The engine will try to dlopen each of the paths provided until one is successfully opened.

To load new assets, create an Android `AssetManager` that has access to the newly downloaded assets. Pass this `AssetManager` to `FlutterJNI.updateJavaAssetManager`.

The `FlutterJNI` instance is passed in via `setJNI`.

### Tooling

Flutter’s tooling comes with the capability to instruct `gen_snapshot` to build split AOT and pack the `.so` files and assets into an Android dynamic feature module. Custom implementations are typically unable to make use of this tooling. Therefore, you may have to write custom tooling to package the `.so` files and assets yourself to work alongside the custom `DeferredComponentManager` implementation.

To make `gen_snapshot` generate loading units and the .so shared libraries, pass the `--loading_unit_manifest=<manifestPath>` option to `gen_snapshot`. This will create a .json file at your manifestPath that contains the loading units and corresponding `.so` libs generated. The `.so` file and assets can then be packed in whatever format you wish to distribute them on your file server. It is also your responsibility to unpack this format in your `DeferredComponentManager` implementation.

### File server

Since custom implementations typically do not use the Play store, a custom system for hosting and serving files to end users should be implemented. This part is highly variable in how it can be accomplished, and the only requirement is that it functions in tandem with the `DeferredComponentManager` implementation to deliver the files needed to load Dart shared libraries and assets.
