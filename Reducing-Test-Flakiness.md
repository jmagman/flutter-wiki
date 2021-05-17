Flakiness issue has caused a large portion of the [Flutter tree](https://flutter-dashboard.appspot.com/#/build) redness, and below workflow will be enforced to reduce flaky issues. The framework post-submit DeviceLab tests will be focused on in the beginning, and the logic will be extended to other host only tests in the future.

# Preventing flaky tests
## Adding a new DeviceLab test
DeviceLab tests are located under [`/dev/devicelab/bin/tasks`](https://github.com/flutter/flutter/tree/master/dev/devicelab/bin/tasks). If you plan to add a new DeviceLab test, please follow
* Create a PR to add test files
  * Make sure an ownership entry is created for the test in [TESTOWNERS](https://github.com/flutter/flutter/blob/master/TESTOWNERS) file
* Enable the test in the staging env. first for validation - make sure 50 consecutive successful runs without any flakiness issue
  * How: add the new test to the appropriate platform in [devicelab_staging_config.star](https://github.com/flutter/infra/blob/master/config/devicelab_staging_config.star)
  * Monitor the test execution in the [milo dashboard](https://ci.chromium.org/p/flutter/g/devicelab_staging/console)
* If no flakiness issue pops up, then enable the test in the prod env. and you will see the new test in the [build dashboard](https://flutter-dashboard.appspot.com/#/build).
  * How
    * Add the new test to the appropriate platform in [devicelab_config.star](https://github.com/flutter/infra/blob/master/config/devicelab_config.star)
    * Enable the test to [build dashboard](https://flutter-dashboard.appspot.com/#/build) by adding an entry in [prod_builders.json](https://github.com/flutter/flutter/blob/master/dev/prod_builders.json)


# Detecting flaky tests
On a weekly basis, the tree gardener will scan through test execution statistics over the past 15 days and identify top flaky ones
* If there are any test builders whose Flaky Ratio >= 2%
  * Mark the tests as flaky by updating the entry in [prod_builders.json](https://github.com/flutter/flutter/blob/master/dev/prod_builders.json).
  * Create a tracking bug if not existing in the [bug pool](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+project%3Aflutter%2Fflutter%2F189+label%3A%22team%3A+flakes%22).
    * The sub-team TL will be assigned by default for further triage/re-assign.
    * Sub-team labels will be added as well for better tracking.
    * P1 will be labeled
* If there is not any test builder whose Flaky Ratio >= 2%, then look for the top test builder whose Flaky Ratio < 2%
  * Mark the test as flaky by updating the entry in [prod_builders.json](https://github.com/flutter/flutter/blob/master/dev/prod_builders.json).
  * Create a tracking bug if not existing in the [bug pool](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+project%3Aflutter%2Fflutter%2F189+label%3A%22team%3A+flakes%22).
    * The sub-team TL will be assigned by default for further triage/re-assign.
    * Sub-team labels will be added as well for better tracking.
    * P2 will be labeled
# Fixing flaky tests
The TL will help triage, reassign, and attempt to fix the flakiness.

If fixed, the test can be re-enabled after being validated for 50 consecutive runs without flakiness issues.

If not fixable, the test will be removed from the flutter build dashboard or deleted from CI completely depending on specific cases.