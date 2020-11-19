This page describes experimental options that can be used with Flutter web support. These are not enabled by default because we haven't yet completed sufficient testing to promote this code into the default web experience. While they may offer performance improvements in your application, we advise caution in enabling them without careful testing in your specific scenario.

Support for the web as a target is currently available in the `beta` channel for Flutter. More information about the web target can be found at https://flutter.dev/web. 

## CanvasKit backend
By default, Flutter targets the DOM when building for web targets. This provides a high degree of compatibility across browsers, and requires the smallest amount of code download.

We're working in parallel on a CanvasKit-based backend that enables rendering Skia paint commands in the browser using WebAssembly and WebGL. We started experimenting with CanvasKit because Skia is the same graphics engine used by Flutter mobile and desktop and, unlike HTML DOM, it allows direct access to the low-level graphics stack enabling full feature parity with native Flutter. 

You can enable the CanvasKit rendering engine with the `FLUTTER_WEB_USE_SKIA` option, as follows:

```
flutter run -d chrome --release --dart-define=FLUTTER_WEB_USE_SKIA=true
```