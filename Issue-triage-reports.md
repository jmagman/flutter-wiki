This page contains weekly issue triage reports from the Nevercode front-line triage team.

## 2021-01-29

### Notable issues

* Camera throws TimeoutException when flash is turned ON: [#74645](https://github.com/flutter/flutter/issues/74645)
* Null safety checks occur while running flutter test on beta: [#74898](https://github.com/flutter/flutter/issues/74898)
* App goes black when there is a system dialog and locking/unlocking on Android: [#74801](https://github.com/flutter/flutter/issues/74801)
* Text selection from right to left inside text fields is broken on Web: [#74504](https://github.com/flutter/flutter/issues/74504)
* Auto scroll not working with textField when showCursor is false: [#74566](https://github.com/flutter/flutter/issues/74566)

### Issue counts

New Flutter issues triaged 261, closed 90 (34%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 53.9%  |
| Duplicate  | 30.3%  |
| Fixed | 9.0%  |
| Solved | 6.7%  |

Issues closed from iterations 81

| Reason for closing  |  |
| -- | -- |
| Invalid | 13.6%  |
| Duplicate  | 8.6%  |
| Fixed | 16.0%  |
| Solved | 11.1%  |
| Timeout | 50.6%  |

Stale issues ([found to occur in releases 1.12 and 1.17](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+label%3A%22has+reproducible+steps%22+-label%3A%22found+in+release%3A+1.22%22+-label%3A%22found+in+release%3A+1.23%22+-label%3A%22found+in+release%3A+1.24%22+-label%3A%22found+in+release%3A+1.25%22+-label%3A%22passed+first+triage%22+-label%3A%22severe%3A+new+feature%22+-label%3Aproposal+-label%3A%22found+in+release%3A+1.21%22+-label%3A%22found+in+release%3A+1.20%22+-label%3A%22found+in+release%3A+1.26%22+)) checked 55, closed 11 (20%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 2 |
| Fixed | 6 |
| Solved | 3 |

New Flutterfire issues triaged 34, closed 15 (44%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 5  |
| Duplicate  | 5  |
| Solved  | 5  |

## 2021-01-22

### Notable issues

* Decoding image data is failing on web: [#74489](https://github.com/flutter/flutter/issues/74489)
* [webview_flutter] Simulator keyboard isn’t showing and hardware keyboard isn’t working: [#74044](https://github.com/flutter/flutter/issues/74044)
* Enter/return key on a TextField with InkWell doesn’t work on the Samsung keyboard: [#74041](https://github.com/flutter/flutter/issues/74041)
* Avoid reading clipboard without authorization when using textfield on Xiaomi MIUI 12: [#74139](https://github.com/flutter/flutter/issues/74139)
* Some users are experiencing issues with third-party packages that rely on `flutter_localizations` and `intl` on beta and above channels, they are forced to use `stable` channel to avoid package conflicts. This is caused by premature `flutter_localizations` dependency on the null-safety version of the `intl`. E.g. [#74421](https://github.com/flutter/flutter/issues/74421), [#74419](https://github.com/flutter/flutter/issues/74419), [#73861](https://github.com/flutter/flutter/issues/73861)

### Issue counts

New Flutter issues triaged 274, closed 85 (31%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 58.8%  |
| Duplicate  | 35.3%  |
| Fixed | 2.4%  |
| Solved | 3.5%  |

Issues closed from iterations 93

| Reason for closing  |  |
| -- | -- |
| Invalid | 16.1%  |
| Duplicate  | 7.5%  |
| Fixed | 32.3%  |
| Solved | 10.8%  |
| Timeout | 33.3%  |

Stale issues ([found to occur in releases 1.12 and 1.17](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+label%3A%22has+reproducible+steps%22+-label%3A%22found+in+release%3A+1.22%22+-label%3A%22found+in+release%3A+1.23%22+-label%3A%22found+in+release%3A+1.24%22+-label%3A%22found+in+release%3A+1.25%22+-label%3A%22passed+first+triage%22+-label%3A%22severe%3A+new+feature%22+-label%3Aproposal+-label%3A%22found+in+release%3A+1.21%22+-label%3A%22found+in+release%3A+1.20%22+-label%3A%22found+in+release%3A+1.26%22+)) checked 70, closed 11 (16%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 3 |
| Duplicate | 3 |
| Fixed | 4 |
| Solved | 1 |

New Flutterfire issues triaged 41, closed 13 (32%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 6  |
| Duplicate  | 4  |
| Solved  | 3  |

## 2021-01-15

### Notable issues

* Disable analytics by default - Respect EU/ECC laws (GDPR): [#73657](https://github.com/flutter/flutter/issues/73657)
* Flutter Slider Label showing strange numbers: [#73787](https://github.com/flutter/flutter/issues/73787)
* Flutter doctor warnings about flutter plugin not found still cause confusion among users (related to [[flutter_tools] IDE plugin validators should be deprecated](https://github.com/flutter/flutter/issues/61246))
* There have been several issues related to Windows 7 recently

### Issue counts

New Flutter issues triaged 269, closed 86 (32%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 62.8%  |
| Duplicate  | 26.7%  |
| Fixed | 4.7%  |
| Solved | 5.8%  |

Issues closed from iterations 80

| Reason for closing  |  |
| -- | -- |
| Invalid | 21.3%  |
| Duplicate  | 10.0%  |
| Fixed | 37.5%  |
| Solved | 8.8%  |
| Timeout | 22.5%  |

Stale issues ([found to occur in releases 1.12 and 1.17](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+label%3A%22has+reproducible+steps%22+-label%3A%22found+in+release%3A+1.22%22+-label%3A%22found+in+release%3A+1.23%22+-label%3A%22found+in+release%3A+1.24%22+-label%3A%22found+in+release%3A+1.25%22+-label%3A%22passed+first+triage%22+-label%3A%22severe%3A+new+feature%22+-label%3Aproposal+-label%3A%22found+in+release%3A+1.21%22+-label%3A%22found+in+release%3A+1.20%22+-label%3A%22found+in+release%3A+1.26%22+)) checked 39, closed 5 (13%) --- most issues are still reproducible with the latest versions

| Reason for closing  |  |
| -- | -- |
| Duplicate | 2 |
| Fixed | 3 |

New Flutterfire issues triaged 29, closed 6 (21%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 3  |
| Duplicate  | 3  |

## 2021-01-08

### Notable issues

* Holding backspace in textfield stops sporadically on Samsung devices: [#73433](https://github.com/flutter/flutter/issues/73433)
* Play Console Crash - abort / signal 11 (SIGSEGV), code 1 (SEGV_MAPERR): [#73342](https://github.com/flutter/flutter/issues/73342)
* [web] DateTime.microsecondsSinceEpoch on Flutter web is different compared to Flutter mobile: [#73334](https://github.com/flutter/flutter/issues/73334)
* [Image_Picker] does not load images in release mode and throws PlatformException on second tentative: [#72759](https://github.com/flutter/flutter/issues/72759)

### Issue counts

New Flutter issues triaged 237, closed 78 (33%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 53.8%  |
| Duplicate  | 33.3%  |
| Fixed | 3.8%  |
| Solved | 9.0%  |

Issues closed from iterations 116

| Reason for closing  |  |
| -- | -- |
| Invalid | 18.3%  |
| Duplicate  | 5.2%  |
| Fixed | 13.9%  |
| Solved | 9.6%  |
| Timeout | 53.0%  |

Stale issues ([found to occur in releases 1.12 and 1.17](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+label%3A%22has+reproducible+steps%22+-label%3A%22found+in+release%3A+1.22%22+-label%3A%22found+in+release%3A+1.23%22+-label%3A%22found+in+release%3A+1.24%22+-label%3A%22found+in+release%3A+1.25%22+-label%3A%22passed+first+triage%22+-label%3A%22severe%3A+new+feature%22+-label%3Aproposal+-label%3A%22found+in+release%3A+1.21%22+-label%3A%22found+in+release%3A+1.20%22+-label%3A%22found+in+release%3A+1.26%22+)) checked 33, closed 8 (24%)

| Reason for closing  |  |
| -- | -- |
| Duplicate | 1 |
| Fixed | 7 |

New Flutterfire issues triaged 27, closed 6 (22%)

| Reason for closing  |  |
| -- | -- |
| Invalid | 4  |
| Duplicate  | 1  |
| Solved | 1 |
