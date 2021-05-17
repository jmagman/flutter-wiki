# Deferred Components

Flutter has the capability to build apps that can download additional Dart code and assets at runtime. This allows apps to reduce install apk size and download features and assets when needed by the user.

We refer to each uniquely downloadable bundle of Dart libraries and assets as a “deferred component”. This is achieved by using Dart’s deferred imports, which can be compiled into split AOT shared libraries.

This feature is currently only available on Android, taking advantage of Android and Google Play Store’s dynamic feature modules to deliver the deferred components packaged as Android modules. This does not impact other platforms, which continue to build as normal with all deferred components and assets included at initial install time.

Though modules can be defer loaded, the entire application must be completely built and uploaded as a single Android App Bundle. Dispatching partial updates without re-uploading new Android App Bundles for the entire application is not supported.

This feature only does deferred loading in release and profile mode. In debug mode, all deferred components are present at launch and will instantly load.

## APK size gallery case study

Flutter Gallery implements deferred components for the crane study. Here, we will examine the initial install size gains in https://github.com/flutter/gallery/tree/fully-deferred-gallery where we implement deferred components for all studies and demos (not just crane). In this example, we have moved all the splittable assets and fonts into deferred components. Compared to a non-deferred components application, the initial installed APK file size breakdown is as follows:

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

The deferred Dart libraries are interpreted by gen_snapshot (Dart’s compiler) to produce “loading units”, each of which are output as a split AOT shared library (.so file) when building in profile or release mode. A loading unit is the smallest set of libraries that are imported exclusively with the deferred keyword by the main code and can be split off of the base libraries.

The following diagram shows an example app structure and a “lifecycle” of how deferred dart libraries are compiled into loading units and packaged into a .aab file.

![](https://raw.githubusercontent.com/flutter/engine/master/docs/deferred_components_architecture.svg)


This example app has the following properties:

Four Dart libraries, with Dart libraries lib1 and lib2 dependent on each other. lib1, lib3, and lib4 are imported in the flutter app’s main code as deferred.
Four loading units, with one being the base loading unit with id 1 and loading unit 2 containing both lib1 and lib2. Loading units 3 and 4 contain lib3 and lib4 respectively.
Three defined deferred components, plus an implicit base component. component1 contains loading unit 2 and assets. component2 contains both loading units 3 and 4 and no assets. component3 is an assets only component.
app-release.aab is the completed build output file, and contains all three components as well as the base component.

There will always be an implicit base loading unit that contains core flutter packages as well as your base application code. Any libraries that are not fully imported as deferred by your base app code will be included in the base loading unit. If no additional loading units other than base are generated, it likely means you imported your files incorrectly.

Multiple Dart libraries are compiled as a single loading unit if they import each other eagerly  (non-deferred):

![](https://raw.githubusercontent.com/flutter/engine/master/docs/dart_split_aot_compilation.svg)

## Custom Implementations

It is possible to write a custom implementation that bypasses the Android Play store. This is only recommended for advanced developers and is primarily aimed at apps with very unique needs such as extremely large asset components, specific download behavior, or distribution in a region that does not have access to the Play store (eg, China).
