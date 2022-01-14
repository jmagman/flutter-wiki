When deciding what to work on, we usually focus on issues that have a lot of thumbs-up reactions on the first comment, what we call the "popular issues".

Some popular issues are, for whatever reason, topics on which we cannot find a good way to make progress. Since those issues where we _do_ make progress get closed, the result is that [the list of most-popular issues](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) is now full of only those issues where we have conspicuously not made progress!

In the interests of transparency, this wiki page discusses the status of these popular issues. It is only updated occasionally and so may not be entirely up to date; for the most up to date information, please see the latest comments on the relevant issue. (Please avoid asking for an update on issues, otherwise they become full of people asking for updates and nobody can find the actual updates.)

## [Code Push / Hot Update / out of band updates #14330](https://github.com/flutter/flutter/issues/14330)

<!-- https://github.com/flutter/flutter/issues/14330#issuecomment-442274897 (terminology) -->
<!-- https://github.com/flutter/flutter/issues/14330#issuecomment-485565194 (2019 update) -->
<!-- https://github.com/flutter/flutter/issues/14330#issuecomment-442274897 (rfw) -->

There are three main areas that people are referring to here:

* **Modular application delivery**: the ability to package a single app into multiple separate archives when compiling it, and download them independently as needed. This is supported on Android via [deferred components](https://docs.flutter.dev/perf/deferred-components). We suspect it is not possible to achieve this on iOS with Apple's current guidelines and tools. We have not yet attempted to provide this on desktop or web, primarily because we have more important issues to resolve on those platforms first.

* **Dynamic extension loading**: the ability to download some Dart code that wasn't written when the app was first published, which adds a new feature to the app. This could be done on the fly. It may require the core app to be larger since we can't know ahead of time what is needed by each future extension. There are various solutions in this space, such as combining [the rfw package](https://pub.dev/packages/rfw) and an FFI-based or Wasm-based solution (e.g. [package:wasm](https://pub.dev/packages/wasm)). There is [an example](https://github.com/flutter/packages/tree/master/packages/rfw/example/wasm) that provides a proof-of-concept for this combination of packages: a Flutter desktop application that knows nothing about being a calculator downloads an interface description specifying all the buttons and their layout to show on the screen, and downloads a C program compiled to Wasm to perform the calculations. The Flutter program is merely a bridge between these two downloaded files. We are looking for feedback from people using this feature; please add your experiences to [issue 90218](https://github.com/flutter/flutter/issues/90218).

* **Dynamic patching**: the ability to update the Dart code of an app in the field by downloading a patch (of sorts) and providing it to the Dart VM. This would require a reload of the app to take effect. Dynamic patching was previously on our roadmap for 2019. After investigating this in greater detail, we decided not to proceed with that work. There were several factors that led us to this decision:

  * To comply with our understanding of store policies on Android and iOS, any solution would be limited to JIT code on Android and interpreted code on iOS. We are not confident that the performance characteristics of such a solution on iOS would reach the quality that we demand of our product. (In other words, "it would be too slow".)

  * There are some serious security concerns. Since these patches would essentially allow arbitrary code execution, they would be extremely attractive malware vectors. We could mitigate this by requiring that patches be signed using the same key as the original package, but this is error prone and any mistake would have serious consequences. This is, fundamentally, the same problem that has plagued platforms that allow execution of code from third-party sources. This problem could be mitigated by integrating with a platform update mechanism, but this defeats the purpose of an out-of-band patching mechanism.

  * There is currently no out-of-the-box open source hosting solution for patching applications, so we would either have to rely on people configuring their Web servers accordingly, or we would have to create integrations for proprietary third-party services, or we would have to create our own bespoke solution. Hosting patches is a space we are not eager to enter. Having people configure their own server leaves them open to making mistakes with potentially serious implications as explained in the previous point about security. Depending on third-party services puts Flutter in an awkward position of having to pick winners and exposes us to the risk of those projects themselves making policy changes that would affect this feature.

