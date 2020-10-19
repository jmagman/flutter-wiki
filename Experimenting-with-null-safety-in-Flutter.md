A loose collection of notes about null safety in Flutter during the Technical Preview 2 (TP2) phase.

**NOTE: TP2 is not published yet. This document is a work-in-progress.**

## Enabling null safety in TP2

In current releases, null safety requires the following:

1. Turning on the feature itself through the `--enable-experiment=non-nullable` flag
2. Increasing the SDK constraints to a version that supports null safety
3. Enabling language analysis for null safety in the `analysis_options.yaml` file

A good example of all this is the null safe sample [link TBD]

## Setting SDK constraints

- Note that the lower bound for the SDK does not actually have to match the current SDK version - it needs to match the experimental release version, which is set in [this file](https://github.com/dart-lang/sdk/blob/master/tools/experimental_features.yaml). 
- This only applies in the presence of the explicit `--enable-experiment=non-nullable` flag or the package existing in the allow list. 
- Once the feature is released the packages will have to be republished / updated to have a new min SDK constraint which equals the actual release version for the feature (hence the restriction for the max SDK constraint as well).

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

If you need to take a dependency on other null-safe packages **for development only**, you can do so temporarily by referencing the null-safe branch, for example:

```yaml
dependencies:
  ffi:
    git:
      url: git@github.com/dart-lang/ffi.git
      ref: null_safety
```

## Porting FFI code w/ null safety

- When using native structs with FFI, null safety imposes a new obstacle. Today users write Dart classes to represent a native memory layout, with metadata to specify the "native" C representation. However, this creates a compile-time error when null safety is enabled, since the variable is uninitialized in the typical pattern. Dart therefore introduces the ability to express external instance variables, which are syntactic sugar for an external getter and setter property that resolves this.

The [feature specification can be found in the Dart language repo](https://github.com/dart-lang/language/blob/master/accepted/future-releases/abstract-external-fields/feature-specification.md).

A good example of using this can be found in the [following code sample, which maps the Win32 WNDCLASS struct to its corresponding Dart representation](https://github.com/timsneath/win32/blob/5f00efbe88bfa010c7afb006df0fe0dea749b06c/lib/src/structs.dart#L35).

## Other useful links

 - https://dart.dev/null-safety