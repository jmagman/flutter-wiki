A loose collection of notes about null safety in Flutter during the Technical Preview 2 (TP2) phase.

## Flutter TP2 tech preview build

The Flutter TP2 tech preview build is `1.24.0-3.0.pre` from the
Flutter [dev channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels).

You can change to the dev channel with `flutter channel dev` followed by `flutter upgrade`
(don't forget to change back to `flutter channel stable` for production use.

## Enabling null safety in TP2 for existing apps and packages

In current releases, null safety requires the following:

### 1. Update the SDK constraint

You must increase the SDK constraints in `pubspec.yaml` to a version that
supports null safety.

```yaml
environment:
  sdk: ">=2.11.0-213.0.dev <2.12.0"
```

- The lower bound for the SDK does not actually have to match the current SDK version.
  It needs to match the experimental release version, defined in [this file](https://github.com/dart-lang/sdk/blob/master/tools/experimental_features.yaml), which for null safety is `2.10.0`. 
- This only applies in the presence of the explicit `--enable-experiment=non-nullable` flag or the package existing in the allow list. 
- Once the feature is released the packages will have to be republished / updated to have a new min SDK constraint which equals the actual release version for the feature (hence the restriction for the max SDK constraint as well).

_[More information on configuring your SDK constraint](https://dart.dev/null-safety#configure-the-sdk-version)_

### 2. Enabling language analysis for null safety in the `analysis_options.yaml` file

```yaml
analyzer:
  enable-experiment:
  - non-nullable
```

- If you don't see warnings and errors specific to null safety after you update `analysis_options.yaml`:
  - Make sure to run `pub get` or `pub upgrade`. This ensures `.dart_tool/package_config.json` is updated to reflect that the current package has opted-in to null safety.
  - Restart the analysis server in your IDE.
  - Restart your IDE.

### 3. Use the `--enable-experiment=non-nullable` flag

When building and running code that uses null-safe features, you must provide this flag to tell the tooling to support the new feature.

`> flutter run --enable-experiment=non-nullable`

_[More information on using the experiment flag](https://dart.dev/null-safety#pass-the-experiment-flag)_

[//]: # (More info link pending https://github.com/dart-lang/site-www/issues/2661)

### More information

A good example of all this is the [Flutter null safety sample](https://github.com/flutter/samples/tree/master/experimental/null_safety).

## Enabling null safety in a new Flutter project

If you are creating a new Flutter app or package to experiment with null safety, you can use these steps
as a replacement for steps 1. (updating `pubspec.yaml`) and 2. (adding `analysis_options.yaml`) above:

  - `flutter create testapp`
  - `cd testapp`
  - `dart migrate`

## Using null-safe dependencies

If you need to take a dependency on other null-safe packages **for development only**, first check to see if the package you depend on has a null-safe version published.
In some *limited cases*, we have published null-safe pre-release versions of core packages to aid null-safe migration.
You can see if a prerelease is available by visiting the package page on [pub.dev](https://pub.dev).

You must explicitly opt-in to uses the prerelease versions like so:

```yaml
dependencies:
  charcode: ^1.2.0-nullsafety
  collection: ^1.15.0-nullsafety
  source_span: ^1.8.0-nullsafety
  string_scanner: ^1.1.0-nullsafety
  typed_data: ^1.3.0-nullsafety

dev_dependencies:
  pedantic: ^1.10.0-nullsafety
  test: ^1.16.0-nullsafety
```

In some cases a package may have been updated to null safety in its source repository, but the package has not been published yet.
You can use a [git dependency](https://dart.dev/tools/pub/dependencies#git-packages) to reference these packages.

```yaml
dependencies:
  ffi:
    git:
      url: git@github.com/dart-lang/ffi.git
      ref: null_safety
```

## Publishing null-safe packages

Please *don't* publish null-safe packages yet, since they will not work without the experiment flag enabled. 

If you're working on test migrations of packages, we recommend instead that you create a null-safe development branch for your code (ideally named `null_safety` for consistency with other packages). In your `pubspec.yaml` file, you should update the version number as follows:

```yaml
# increment the last digit as necessary if you make changes
version: 2.0.0-nullsafety.1
```

and set the `publish_to` flag in this branch to avoid accidental publishing of your package to pub.dev:

```yaml
publish_to: none
```

## Porting FFI code w/ null safety

- When using native structs with FFI, null safety imposes a new obstacle. Today users write Dart classes to represent a native memory layout, with metadata to specify the "native" C representation. However, this creates a compile-time error when null safety is enabled, since the variable is uninitialized in the typical pattern. Dart therefore introduces the ability to express external instance variables, which are syntactic sugar for an external getter and setter property that resolves this.

The [feature specification can be found in the Dart language repo](https://github.com/dart-lang/language/blob/master/accepted/future-releases/abstract-external-fields/feature-specification.md).

A good example of using this can be found in the [following code sample, which maps the Win32 WNDCLASS struct to its corresponding Dart representation](https://github.com/timsneath/win32/blob/5f00efbe88bfa010c7afb006df0fe0dea749b06c/lib/src/structs.dart#L35).

## How to file bugs or request changes

If you find bugs or have suggestions in the core Dart null safety language feature or tooling, please [file an issue here](https://github.com/dart-lang/sdk/issues/new?title=Null%20safety%20feedback:%20[issue%20summary]&labels=NNBD&body=Describe%20the%20issue%20or%20potential%20improvement%20in%20detail%20here).

If you find bugs or have suggestions in the Flutter null safe APIs or tools, please [file an issue here](https://github.com/flutter/flutter/issues/new?title=Null%20safety%20feedback:%20[issue%20summary]&labels=a%3A%20null-safety&body=Describe%20the%20issue%20or%20potential%20improvement%20in%20detail%20here).

## Other useful links

 - https://dart.dev/null-safety