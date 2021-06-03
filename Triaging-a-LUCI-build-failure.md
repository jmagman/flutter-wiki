All post submit tests have been running in LUCI for multiple flutter repositories, including [framework](https://flutter-dashboard.appspot.com/#/build) and [engine](https://ci.chromium.org/p/flutter/g/engine/console). This page covers what to do when a LUCI build failure happens.

## Infra Failure
An infra failure shows up as a purple box in the build dashboard:

[[/images/luci_infra_failure.png|test]]

### Overview of an infra failure build
An example build: [Linux color_filter_and_fade_perf__e2e_summary](https://ci.chromium.org/ui/p/flutter/builders/prod/Linux%20color_filter_and_fade_perf__e2e_summary/1563/overview)

*  (i) Link to the historical build list of this builder
*  (ii) A quick glimpse of the infra failure 
*  (iii) The step list of this builder, defined by the recipe on the right
*  (iv) The real failed step causing the build failure
### What to do
1. Check if issue exists in the infra bug pool
