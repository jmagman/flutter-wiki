<!-- go/flutter-release-2020 -->

## Dev Channel

The Flutter [Conductor] tool is used to produce Dev releases.

1. Of the release candidate branches that have successfully been rolled to Google, select the newest that satisfies:
  * Has not been reverted
  * Framework and engine revisions have passed all post-submit testing
2. If this branch already has cherrypicks on it, you must first add a git tag to the branch point in order for version resolution to be correct on the master branch.
  - For a release candidate branch `flutter-x.y-candidate.m`, the git tag on the branch point should be `x.y.0-m.0.pre`. For example, if the branch were `flutter-2.13-candidate.4`, the tag at the branch point should be `2.13.0-4.0.pre`.
  - Push this tag 
  - Apply this tag locally by checking out the branch and issuing `git tag $TAG_NAME`.
  - Push it to upstream with `git push upstream $TAG_NAME` (ensure that `upstream` is the official Framework repository)
3. Follow the process from the [Conductor] README, with the following inputs:
  - `--candidate-branch=` the branch you selected in step 1
  - `--release-channel=dev`
  - `--framework-mirror=` and `--engine-mirror=` to your personal GitHub mirrors--these will be used to push your local changes and to open PRs from
  - `--increment=` should be `n` if you already tagged the branch point in step 2, otherwise `m`.

[Conductor]: https://github.com/flutter/flutter/blob/master/dev/conductor/README.md