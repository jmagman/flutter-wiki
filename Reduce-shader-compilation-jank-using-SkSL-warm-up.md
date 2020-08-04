
**TLDR** If a Flutter app has janky animations during the first run, SkSL shader warm-up can speed them up by more than 2x as in the screenshot illustrated below without much manual work.

![SkSL warm-up comparison](https://lh3.googleusercontent.com/pw/ACtC-3cL8kTXciBfFQa1dLvgkBjjL06Gp2IOEsEz0BLrbJBOtnskKnXSJMDbEfqPwGBYUmpXFda_Onal7fY7iYUtm707D7nhXIQTE3kPHbECi4vhYqEW2tjmfUJ_9Ehj-bFycv4dSENPfJIC2FB5asKyzv9V0A=w480-h426-no?authuser=0)

## What is shader compilation jank

If an app has janky animations during the first run, and later becomes smooth for the same animation, then it's very likely due to shader compilation jank.

More technically, a shader is a piece of program to be run on GPUs. When a shader is first used, it needs to be compiled. The compilation could cost up to a few hundred milliseconds whereas a smooth frame needs to be drawn within 16 milliseconds for a 60 FPS (frame-per-second) display. Hence a compilation could cause tens of frames to be missed, and drop the FPS from 60 to 6. That is the compilation jank. A definitive evidence for the shader compilation jank is to see `GrGLProgramBuilder::finalize` in the tracing. See below for an example timeline tracing.

![tracing with GrGLProgramBuilder::finalize](https://lh3.googleusercontent.com/pw/ACtC-3eZimnWZF3qsUTlhAFKki37Md9TXEXvUL8Z47tu9V23Clr-CvFNfwfOr9JOJrsM20XOzkcuPx_Aj6_aX4HWKQX7XX9ODUJaUhcxDpr8wydUwFF7UMGRNtWtTuiVDnxjRjXKAlwH9cdySX0gbaGUaUj31Q=w1460-h374-no?authuser=0)

## How to use SkSL warm-up

Flutter now provides command line tools for app developers to collect shaders that might be needed for end-users in the SkSL (Skia Shader Language) format. Those SkSL shaders can then be packaged into the app, and get warmed up (pre-compiled) when an end-user first opens the app, and hence reduce the compilation jank in later animations. Below is an example workflow of how to collect and package the SkSL shaders.

1. Run the app with `--cache-sksl` turned on (e.g., `flutter run --profile --cache-sksl`) to capture shaders in SkSL.
2. Play with the app to trigger as many animations (especially those with compilation jank) as needed.
3. Press `M` in the command line of `flutter run` to write the captured SkSL shaders into a file named like `flutter_01.sksl.json`.
4. Build the app with SkSL warm-up using 
	- `flutter build apk --bundle-sksl-path flutter_01.sksl.json` for Android
	- `flutter build ios --bundle-sksl-path flutter_01.sksl.json` for iOS.
	- If it's built for a driver test like `test_driver/app.dart`, make sure to also specify `--target=test_driver/app.dart` (e.g., `flutter build ios --bundle-sksl-path flutter_01.sksl.json --target=test_driver/app.dart`).
5. Test the newly built app and release it.

Alternatively, one can write some [integration tests] to automate steps 1 to 3 above using a single command like `flutter drive --profile --cache-sksl --write-sksl-on-exit flutter_01.sksl.json -t test_driver/app.dart`.

With such integration tests, one can easily and reliably get the new SkSLs when the app code changes, or when Flutter upgrades.  Such tests can also be used to verify the performance change before and after the SkSL warm-up. Even better, one can put those tests into a CI (continuous integration) system so the SkSLs are generated and tested automatically over the lifetime of an app.

Take [Flutter gallery][] as an example. The Flutter team has [transitions_perf_test.dart][] used by the CI system to generate SkSLs for every Flutter commit, and verify the performance. For more details, please check the [flutter_gallery_sksl_warmup__transition_perf] and [flutter_gallery_sksl_warmup_ios32__transition_perf] tasks.

The worst frame rasterization time is a nice metric from such integration tests to indicate the severity of shader compilation jank. For instance, the steps above reduce Flutter gallery's shader compilation jank and speeds up its worst frame rasterization time on a Moto G4 from ~90 ms to ~40 ms. On iPhone 4s it's reduced from from ~300 ms to ~80 ms. That leads to the visual difference as illustrated in the beginning of this doc.
 
## Limitations and more considerations

1. **Why not just compile or warm up all possible shaders?**
If there are only a limited number of possible shaders, then Flutter could do a warm-up and compile all of them before-hand to avoid such jank. However, for the best overall performance, the Skia GPU backend used by Flutter can dynamically generate shaders based on a lot of parameters at runtime (e.g., draws, device models, and driver versions). Due to all possible combinations of those parameters, the number of possible shaders is unfortunately exponential. In short, Flutter uses programs (app, Flutter, and Skia code) to generate some other programs (shaders). The number of possible shader programs Flutter can generate is simply too large.

2. **Can SkSLs captured from one device help shader compilation jank on another device?** Theoretically, there's no guarantee that SkSLs from one device would help on another device (but they also won't cause any troubles if SkSLs aren't compatible across devices). Practically, as shown in [this table][SkSL experiment], SkSLs work surprisingly well even if (1) SkSLs are captured from iOS and then applied to Android devices; or (2) SkSLs are captured from emulators and then applied to real mobile devices. As the Flutter team only has a limited number of devices in the lab, we currently don't have enough data to provide a big picture of the cross-device helpfulness. We'd love you to provide us more data points to see how it works in the wild. ([FrameTiming] can be used to compute the worst frame rasterization time in release mode.)

3. **SkSL warm-up doesn't help newer iPhones with Metal.** Flutter recently migrated from OpenGL to Metal for [all newer iOS devices][where will flutter use metal]. But Skia currently only implements the SkSL warm-up for OpenGL. So the SkSL warm-up would only speed up the older iOS devices by default. If you find the shader compilation jank to be a big issue on newer iPhones for your app, please let us know by filing a Github issue. In the longer term, we have a plan to use [test-based shader warm-up] to mitigate this. If there's an urgent need for fixing shader compilation jank on newer iPhones, please give us some feedback on https://github.com/flutter/flutter/issues/61045, and we can help you turn OpenGL back on for your app.

Finally, if you have any questions or suggestions on this doc or SkSL warm-up in general, please feel free to comment on https://github.com/flutter/flutter/issues/60313 and https://github.com/flutter/flutter/issues/53607.

[integration tests]: https://flutter.dev/docs/cookbook/testing/integration/introduction
[where will flutter use metal]: https://github.com/flutter/flutter/wiki/Metal-on-iOS-FAQ#where-will-flutter-use-metal
[test-based shader warm-up]: https://github.com/flutter/flutter/issues/53609
[Flutter gallery]: https://github.com/flutter/flutter/tree/master/dev/integration_tests/flutter_gallery
[transitions_perf_test.dart]: https://github.com/flutter/flutter/blob/master/dev/integration_tests/flutter_gallery/test_driver/transitions_perf_test.dart
[flutter_gallery_sksl_warmup__transition_perf]: https://github.com/flutter/flutter/blob/master/dev/devicelab/bin/tasks/flutter_gallery_sksl_warmup__transition_perf.dart
[flutter_gallery_sksl_warmup_ios32__transition_perf]: https://github.com/flutter/flutter/blob/master/dev/devicelab/bin/tasks/flutter_gallery_sksl_warmup_ios32__transition_perf.dart
[SkSL experiment]: https://github.com/flutter/flutter/issues/53607#issuecomment-608587484
[FrameTiming]: https://api.flutter.dev/flutter/dart-ui/FrameTiming-class.html