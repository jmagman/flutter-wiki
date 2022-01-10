_In the interest of transparency, we want to share high-level details of our roadmap, so that others can see our priorities and make plans based off the work we are doing._

_Our plans will evolve over time based on customer feedback and new market opportunities. We use our quarterly surveys and feedback on GitHub issues to prioritize work. The list here shouldn't be viewed either as exhaustive, nor a promise that we will complete all this work. If you have feedback about what you think we should be working on, we encourage you to get in touch (e.g. by [filing an issue](https://github.com/flutter/flutter/issues/new/choose), or using the "thumbs-up" emoji reaction on an issue's first comment). Flutter is an open source project, we invite contributions both towards the themes presented below and in other areas._

_If you are a contributor or team of contributors with long-term plans for [contributing to Flutter](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md), and would like your planned efforts reflected in the roadmap, please reach out to Hixie (ian@hixie.ch)._

# 2022

## Areas of Focus

### Developer experience

The area where we will spend most of our focus is the developer experience. It is our intent to create an SDK that developers love. This will manifest in a myriad of different areas, for example creating widgets or plugins that solve common scenarios, cleaning up existing APIs, introducing new APIs to simplify frequently-seen patterns, improving error messages, evolving our developer tools and IDE plugins, creating new lints, fixing bugs in the framework and engine, improving API documentation, creating more useful samples, hot reload on the web, and improving stack traces in Dart-to-JS scenarios.

### Desktop

In 2022 we plan to bring our desktop support to the stable channel. We plan on focusing on testing and announcing one platform at a time, as they become ready, starting with [Windows](https://github.com/flutter/flutter/projects/209), then [Linux](https://github.com/flutter/flutter/projects/216), and [macOS](https://github.com/flutter/flutter/projects/215). A significant part of this effort is expanding our regression test suite to give us the confidence that enables us to expand on these efforts without breaking existing code.

### Web

Regarding Flutter for web in particular, we plan to work on improving performance, plugin quality, accessibility, and consistency across browsers. We also intend to make it much easier to embed Flutter applications inside other, non-Flutter, HTML pages.

### Framework and engine

We will update the Material library to [support Material 3](https://github.com/flutter/flutter/issues/91605). This is primarily motivated by our goal to improve fidelity with Android, though it is not limited to that platform.
We intend to implement cross-widget text selection. This is motivated by our goal of achieving good fidelity with the web platform, though again it is not limited to the web.

We intend to improve the text editing experience on various platforms, for example improving our fidelity with desktop text editing conventions and our integration with iPadOS handwriting recognition.

For desktop and web we will provide a solution for menus (context menus and menu bars), including integration with the host OS (which is particularly relevant for macOS).

Finally, also motivated by desktop though again not limited just to that platform, we intend to experiment with supporting rendering to multiple windows from a single Isolate.

### Dart

We plan to continue to evolve the language at a deliberately slow but steady pace. We expect to introduce one major feature in 2022 (probably static metaprogramming; we will make decisions based on our confidence that the feature will improve the language), as well as some minor language improvements, probably including improving the import syntax for packages.

We also plan to expand Dart's compilation toolchain to support compiling to Wasm, contingent on the timely standardisation of WasmGC.

### Jank

[In 2021](https://docs.google.com/presentation/d/1QbNm5Z4JyZLd6czVEL3jlgeR7R_ENgXlnm64n2Z40ss/edit) we resolved a number of issues around jank, but our conclusion was that we needed to entirely rethink how we used shaders. As a result, we have been rewriting our graphics backend. In 2022, we intend to migrate Flutter on iOS to this new architecture, and then, based on our experience with this, begin work on porting this solution to other platforms. In addition, we will also implement other performance improvements and performance introspection features, such as those which our new [DisplayList](https://github.com/flutter/flutter/issues/85737) system has made possible.

## Planned deprecations

We plan to [drop support for 32bit iOS](https://flutter.dev/go/rfc-32-bit-ios-support) in 2022. 

## Infrastructure

In 2022 we will increase our investment in supply chain security, with the intent to eventually bring our infrastructure in line with the requirements described in [SLSA level 4](https://slsa.dev/spec/).

# 2021

## Areas of Focus

### Null safety

We will be introducing [Dart's sound null safety](https://dart.dev/null-safety) to Flutter, and shepherding the migration of the plugin and package ecosystem to null safety, including migrating the packages and plugins directly maintained by the Flutter team.

As part of this we plan to provide a migration tool, samples, and documentation to aid migration of existing code.

### Android and iOS

We are continuing to address [jank-on-startup performance issues](https://github.com/flutter/flutter/projects/188).

We will work on supporting incremental downloads of assets and code from the stores (subject to each platform's limitations), allowing the initial download of applications to be much smaller than the full download, with data fetched on demand.

We will also seek to improve the performance and ergonomics, and reduce the overhead, of embedding Flutter in existing applications on Android and iOS.

In addition, as usual, we plan to add support for new features of the iOS and Android operating systems.

### Web and Desktop

Our goal for 2021 is to deliver production-quality support for Web, macOS, Windows and Linux, in addition to iOS and Android, enabling developers to create apps across six separate platforms using the same SDK.

For Web specifically, our focus will be on fidelity and performance, rather than new features, as we drive to prove that Flutter can provide a high quality experience on the Web.

For desktop, in addition to ensuring a quality experience, we will also be completing our work on the accessibility layer, and adding support for showing multiple independent windows.

### Improving the developer experience

We will continue to focus on removing friction points. One area of research will be around reducing the boilerplate needed to achieve common goals in Flutter. We will also build on our investment in migration tooling for null safety to investigate the possibility of creating tooling that enables us to make breaking changes easier for developers to manage, which would enable us to make some long-desired improvements to our APIs that we have so far avoided due to their breaking nature.

### Ecosystem

In 2021, we will continue to work with the community on the Flutter-team-supported plugins. The goal will be to bring the pre-release plugins up to production quality and maintain them at that level by being increasingly responsive to issues and PRs.
We also plan specifically to make significant improvements to the WebView plugin.

### Quality

We will have efforts around improving Flutter’s memory usage, application download size overhead, runtime performance, battery usage, and jank, based on experiences with real Flutter-based applications. These may take the form of engine or framework fixes, as well as documentation or videos describing best practices. We also intend to improve our tooling to help debug issues around memory usage.

In addition, we will continue to address bug reports. In 2020, we [resolved](https://github.com/issues?q=is%3Aissue+closed%3A2020+is%3Aclosed+user%3Aflutter) over 17,000 issues during the year, and our goal is to have at least that level of impact in 2021.

### New features

While in 2020 we primarily focused on fixing bugs, in 2021 we plan to also add significant new features. Some are listed above. We also intend to make improvements to our table widgets and introduce some tree widgets, with support for large numbers of columns, rows and/or tree levels, and column- or row-spanning cells.

## Release Channels and Cadence

Flutter offers three “channels” from which developers can receive updates: master, beta and stable, with increasing levels of stability and confidence of quality but longer lead times for changes to propagate. We plan to release one beta build each month, typically near the start of the month, and about four stable releases throughout the year. We recommend that you use the stable channel for apps released to end-users. For more details on our release process, see the [Flutter build release channels](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels) wiki page.

We used to also have a _dev_ channel which represented a level of stability between master and beta. At the end of 2021, we retired this channel; it is no longer updated.

# 2020

This roadmap is provided for historical context.

## Areas of focus

### Web and Desktop

At our Flutter Interact event in December 2019, we announced that our support for Web had progressed to beta-level quality. We intend to continue this work with the goal of having Web be supported as an equal peer to Android and iOS. We hope to similarly continue our work in making Flutter the best way to create desktop applications.

Our goal for this year is that you should be able to run `flutter create; flutter run` and have your application run on Web browsers, macOS, Windows, Android, Fuchsia, and iOS, with support for hot reload, plugins, testing, and release mode builds. We intend to ensure that our Material Design widget library works well on all these platforms.

_We don't intend to provide desktop-equivalents of the Cupertino widget library in 2020._

### Quality 

Our other main goal is to improve Flutter's quality, fixing bugs and addressing a few of the most-highly requested features. This covers a wide range of areas but we have a particular focus on our Cupertino library and iOS fidelity, our support for the long tail of Android devices, and the development experience.

We intend to deliver on long-anticipated features such as our router refactor, instance state saving and restoring, and an improved internationalization workflow.

In general in 2020 we intend to primarily focus on fixing bugs rather than adding new features.

_We mainly use the "Thumbs-Up" emoji reactions on the first comment of an issue to determine it's importance. See the [Issue hygiene](https://github.com/flutter/flutter/wiki/Issue-hygiene) wiki page for more details on our prioritization strategy._
