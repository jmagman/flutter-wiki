This page covers additional information that is specific to contributing to flutter/plugins and flutter/packages. If you aren't already familiar with the general guidance on Flutter contribution, start with [Tree hygiene](https://github.com/flutter/flutter/wiki/Tree-hygiene).

## Version and CHANGELOG updates

Any change that needs to be published in order to take effect must update the version in `pubspec.yaml`. There are very few exceptions:
- PRs that only affect tests.
- PRs that only affect unpublished parts of example apps.
- PRs that only affect local development (e.g., changes to ignored lints).

This is because the packages in flutter/plugins and flutter/packages use a continuous release model rather than a set release cadence. This model gets improvements to the community faster, makes regressions easier to pinpoint, and simplifies the release process.

All version changes must have an accompanying CHANGELOG update. Even version-exempt changes should generally update CHANGELOG by adding a special `NEXT` entry at the top of CHANGELOG.md:
```
## NEXT

* Description of the change

## X.Y.Z
```

The next release will change `NEXT` to the new version.

### FAQ

**Do I need to update the version if I'm just changing the README?** Yes. Most people read the README on pub.dev, not GitHub, so a README change is not very useful unless it is published.

**What do I do if I there are conflicts to those changes before or during review?** This is common. You can leave the conflicts until you're at the end of the review process to avoid needing to resolve frequently. Including the version changes at the beginning despite the likelihood of conflicts makes it much harder to forget that step, and also means that a reviewer can easily fix it from the GitHub UI just before landing.

## Platform Support

The goal is to have any plugin feature work on every platform on which that feature makes sense; having a lot of features that are only partially implemented across platforms is often confusing and frustrating for developers trying to use those plugins. However, a single developer will not always have the expertise to implement a feature across all supported platforms.

Given that, we welcome PRs that only implement a feature for a subset of platforms, including just one. To set expectations for how such PRs will be handled:
- They will not be fully reviewed until there's an understanding of what support would look like across other platforms, for several reasons:
  - API for features that will be permanently platform-specific may structured in ways that make that limitation more clear, so knowing if other platforms can support it will affect the review process.
  - We want to avoid over-fitting the plugin APIs to a single platform's API. It is often the case that several platforms can implement a feature, but the behavior is different enough across platforms that we need to design the API in a way that covers those variations in a cohesive way. This means that knowing at a high level what the implementation on other platform also affects the review process.
  - Features that are missing implementations on some platforms need to be clearly documented as such in the API, and those comments should clearly express whether those platform are temporarily missing implementations, or are not expected to ever have implementations due to platform limitations.
  
  The PR author isn't necessarily responsible for answering these questions. These cases will be noted in comments during PR triage; if the PR author, or others in the community, can contribute that information, that will certainly help. If not, that investigation will be part of the review process (in which case the review will likely take longer).
- In some cases, a reviewer may wait on approving a PR for landing until there is a plan in place for landing implementations for other platforms. This is not a hard rule, and will be up to reviewer judgement. This could take a number of forms:
  - Waiting for other PRs from the community that implement the feature for other platforms, and then moving forward with all of them at once.
  - Finding resources within the Flutter team for implementing other platforms before moving forward.
  - Landing the platform interface change and the platform implementations that are done, but waiting on one of the options above before adding the API to the app-facing package's API (allowing developers the option of drilling down to the platform implementation to use the feature on some platforms before it's ready everywhere).

  "Other platforms" might not always include all other platforms. E.g., a feature might be something that's much more likely to be useful on mobile than desktop, or the reverse, and so we might only wait for implementations of that subset. Again, this will be up to reviewer judgement.

In the case where a PR is put on hold for the reasons above, it should be clearly noted in the PR and on the associated issue. We encourage anyone interested in contributing more platform implementation PRs to comment in the bug in such cases.

## Changing federated plugins

Many of the plugins in flutter/plugins are [federated](https://flutter.dev/docs/development/packages-and-plugins/developing-packages#federated-plugins). Because a logical plugin consists of multiple packages, and our CI tests using published package dependencies—in order to ensure that every PR can be published without breaking the ecosystem—changes that span multiple packages will need to be done in multiple PRs. This is common when adding new features.

We are investigating ways to streamline this, but currently the process for a multi-package PR is:

1. Create a PR that has all the changes, and update the pubspec.yamls to have path-based dependency overrides. So if you are changing a plugin called `foo`, add the following to the `foo` sub-package and any `foo_some_platform` platform implementation packages that depend on changes to the platform interface:
    ```
    # FOR TESTING ONLY. DO NOT MERGE.
    dependency_overrides:
      foo_platform_interface:
        path:
          ../foo_platform_interface
    ```

    Also add a similar override for each `foo_some_platform` in `foo` if necessary (e.g., if your change includes integration tests in the main package that require the updated implementations).

1. Upload that PR and get it reviewed and into a state where the only test failure is the one complaining that you can't publish a package that has dependency overrides.

1. Create a new PR that's has only the platform interface package changes from the PR above, and ask the reviewers of the main package to review that.

1. Once it has been reviewed (which should be trivial given the review above), landed, and published, update the initial PR to:
    * remove the changes that are part of the other PR,
    * replace the dependency overrides on the platform interface package with a dependency on the published version, and
    * merge in (or rebase to) on the latest version of `master`.

1. If there are any dependency overrides remaining, repeat the previous two steps with those packages. There should never be interdependencies between platform implementation packages, so all implementations should be able to be handled in a single new PR.

1. Once there are no dependency overrides, ask the reviewer to land the main PR.

### Breaking changes to plugin platform interfaces

Breaking changes to platform interfaces (any package ending in `_platform_interface`) are strongly discouraged:
- They require each platform implementation to adopt the new version, and the app-facing package can't pick up any of those changes until all implementations have been updated. This could cause situations where bug fixes for one platform are held up on another platform adopting a feature change.
- They require a series of changes to CI to selectively disable testing the latest versions of all packages, then later re-enable them.
- They temporarily lock out unendorsed implementations, until their developers can update.

Because platform interfaces are not expected to be called by clients of the plugins, we favor backward compatibility over having a clean API at that layer.

In order to avoid accidental breaking changes that are missed in review, CI will by default fail for any breaking change to the platform interface. If you believe you need to make a breaking change, and you have discussed it with the reviewer and they agree, you must add the following to the PR description:
```
## Breaking change justification

<Insert good reason for breaking change here.>
```

The format of the header line must match exactly in order for CI to pass.

### Changing platform interface method parameters

Because platform implementations are subclasses of the platform interface and override its methods, almost *any* change to the parameters of a method is a breaking change. In particular, adding an optional parameter to a platform interface method *is* a breaking change even though it doesn't break callers of the the method.

The least disruptive way to make a parameter change is:

1. Add a new method to the platform interface with the new parameters, whose default implementation calls the existing method. This is not a breaking change.
    1. Include a comment that the old method is deprecated, and will be removed in a future update. Don't actually mark it as `@Deprecated` since that will create unnecessary warnings, and consumers of a package shouldn't be directly using these methods anyways.

2. Update the implementations to override both methods.

3. Update the app-facing package to call the new method.

At some later point the deprecated method can be cleaned up by:

1. Making a breaking change in the platform interface package to remove any deprecated methods.

2. Updating the app-facing package to allow either the new major version or the previous version of the platform interface (to minimize version lock issues with implementations).

3. Updating all the implementations to use the new major version, removing the override of the deprecated methods.

This cleanup step is low priority though; deprecated methods in a platform interface should be largely harmless, as it's an API with very few direct customers. It's actually preferable to wait quite some time before doing this, as it gives any unendorsed third-party implementations that may exist more time to adapt to the change without disruption.