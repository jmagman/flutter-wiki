This page covers additional information that is specific to contributing to flutter/plugins and flutter/packages. If you aren't already familiar with the general guidance on Flutter contribution, start with [Tree hygiene](https://github.com/flutter/flutter/wiki/Tree-hygiene).

## Version and CHANGELOG updates

Any change that needs to be published in order to take effect—almost any PR except a change that is only to tests—should update the version in `pubspec.yaml` and describe the change in `CHANGELOG.md`. This is because the packages in flutter/plugins and flutter/packages use a continuous release model rather than a set release cadence. This has several advantages:
- Improvements get to the community faster.
- Regressions are easier to pinpoint.
- The release process can be automated.

### FAQ

**Do I need to update the version if I'm just changing the README?** Yes. Most people read the README on pub.dev, not GitHub, so a README change is not very useful until it's published.

**What do I do if I there are conflicts to those changes before or during review?** This is common. You can leave the conflicts until you're at the end of the review process to avoid needing to resolve frequently. Including the version changes at the beginning despite the likelihood of conflicts makes it much harder to forget that step, and also means that a reviewer can easily fix it from the GitHub UI just before landing.

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