# Upgrading Android API

When new Android versions become available, the following steps should be taken in order to fully support the new API version in the Flutter Engine. These steps only change the Engine to build against and target the new API and does not guarantee everything works with the changes in the new android API.

## Buildroot:

build/config/android/config.gni: Edit default_android_sdk_version and  default_android_sdk_build_tools_version

## CIPD:

Upload new Android platforms, build-tools, and platform-tools to CIPD (https://chrome-infra-packages.appspot.com/p/flutter/android/sdk). To do so, you will need to download the new versions of the android sdk, platform-tools, and build-tools using Android Studio’s SDK manager. It is recommended to remove older versions of the sdk or tools to avoid accidentally uploading multiple versions of the same package.

Flutter now contains a script to upload the packages. Run the script at `engine/tools/create_sdk_cipd_package.sh` for each of the platforms, platform-tools, and build-tools packages.

This can also be done via manual CIPD commands. Run the following commands to zip and upload each package to CIPD:

* `$ cipd create -in <your-android-dir>/Android/sdk/platforms -name flutter/android/sdk/platforms -tag version:<new-version-tag>`

* `$ cipd create -in <your-android-dir>/Android/sdk/platform-tools -name flutter/android/sdk/platform-tools -tag version:<new-version-tag>`

* `$ cipd create -in <your-android-dir>/Android/sdk/build-tools -name flutter/android/sdk/build-tools -tag version:<new-version-tag>`

Typically, `<your-android-dir>` is in your home directory under `~/Library/Android`. The `<new-version-tag>` is what you will use to specify the new package you uploaded in the Flutter engine DEPS file.

## Engine:

* DEPS: Roll buildroot hash
* DEPS: Change the version parameter under `flutter/android/sdk/platforms/${{platform}}` to the new build-tools version tag. Eg, `'version': 'version:30r2'`
* DEPS: Change the version parameter under `flutter/android/sdk/platform-tools/${{platform}}` to the new build-tools version tag. Eg, `'version': 'version:30.0.4'`
* DEPS: Change the version parameter under `flutter/android/sdk/build-tools/${{platform}}` to the new build-tools version tag. Eg, `'version': 'version:30.0.1'`

## Flutter LUCI Recipes:

Scenario tests recipes/engine/scenarios.py: Update the CIPD hash to the latest version that contains the configuration for the Android Virtual Device (AVD) desired `generic_android<API#>.textpb` and change the script to use the new textpb config and change the API number in the logs. The CIPD package for the AVD launcher can be found at https://chrome-infra-packages.appspot.com/p/chromium/tools/android/avd/+/ and updating the packages uploaded there is tracked in https://bugs.chromium.org/p/chromium/issues/detail?id=1112429#c7

## Framework, examples and samples

* Templates in tooling: Change `targetSdkVersion` in various `build.gradle.tmpl` files to use the new API version
* Examples, samples, gallery, etc: Change `targetSdkVersion` in `android/app/build.gradle` for each project to the new API version.
