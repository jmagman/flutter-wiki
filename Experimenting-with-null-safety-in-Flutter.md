A loose collection of notes about null safety in Flutter during the Technical Preview 2 (TP2) phase.

**NOTE: TP2 is not published yet. This document is a work-in-progress.**

## Enabling null safety in TP2

In current releases, null safety requires the following:

1. Turning on the feature itself through the `--enable-experiment=non-nullable` flag
2. Increasing the SDK constraints to a version that supports null safety
3. Enabling language analysis for null safety in the `analysis_options.yaml` file

A good example of all this is the null safe sample [link TBD]

## Setting SDK constraints

- Note that the lower bound for the SDK does not actually have to match the current SDK version - it needs to match the experimental release version which is a separate concept, and defaults to the current sdk version. 
- This only applies in the presence of the explicit --enable-experiment=non-nullable flag or the package existing in the allow list. 
- Once the feature is released the packages will have to be republished/updated to have a new min SDK constraint which equals the actual release version for the feature (hence the restriction for the max SDK constraint as well).

## Porting FFI code w/ null safety

- When creating structs to use with FFI, there is a challenge. Null safety requires fields to be initialized, but you can't initialize class members that extend from `Struct` because they are allocated differently. Instead, you can use the `external` keyword to indicate to Dart that a variable is managed outside of Dart. [Improve this text w/ sample]

## Useful Links

 - https://dart.dev/null-safety