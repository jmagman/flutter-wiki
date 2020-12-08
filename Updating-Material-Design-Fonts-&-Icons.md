_(This page is referenced by comments in the Flutter codebase.)_

This page describes the process for updating the Material Design icons:

 1. Use git to clone https://github.com/google/fonts
 1. Download the icon font from https://goto.google.com/flutter-material-font (Google-only, sorry).
    1. Rename the file to `MaterialIcons-Regular.otf`.
    1. Use FontTools to set new table values by running a python script like so (replacing the date with the date the cl was submitted and the version number with the cl number) :
    ```python
    from fontTools import ttLib

    font = ttLib.TTFont("MaterialIcons-Regular.otf")
    name = font["name"]
    for n in name.names:
        value = n.toUnicode().replace("All3P", "Material")
        if value != n.toUnicode():
            name.setName(value, n.nameID, n.platformID, n.platEncID, n.langID)

    name.setName("Material Icons : 2020-9-24", 3, 1, 0, 0x0)
    name.setName("Material Icons : 2020-9-24", 3, 3, 1, 0x409)

    name.setName("MaterialIcons-Regular", 4, 1, 0, 0x0)
    name.setName("MaterialIcons-Regular", 4, 3, 1, 0x409)

    name.setName("Version 321858779", 5, 1, 0, 0x0)
    name.setName("Version 321858779", 5, 3, 1, 0x409)

    font.save("MaterialIcons-Regular.otf")
    ```

 1. Copy license files that haven't changed:
    1. Copy the current `MaterialIcons_LICENSE` file from `flutter/bin/cache/artifacts/material_fonts/MaterialIcons_LICENSE`.
    1. Copy the current `Roboto_LICENSE.txt` file from `flutter/bin/cache/artifacts/material_fonts/Roboto_LICENSE.txt`.    
    1. Copy the current `RobotoCondensed_LICENSE.txt` file from `flutter/bin/cache/artifacts/material_fonts/RobotoCondensed_LICENSE.txt`.
 1. Generate the new codepoints list by following the instructions at https://goto.google.com/generate-codepoints (also Google-only, sorry).
    1. Copy that list into a new local file called `codepoints`.
 1. Create a `fonts.zip` file to upload to Google Storage:
    ```
    STAGING=/tmp/fonts_staging
    mkdir $STAGING
    cp codepoints $STAGING
    cp fonts/ofl/roboto/static/*.ttf $STAGING
    cp MaterialIcons_LICENSE $STAGING
    cp MaterialIcons-Regular.otf $STAGING
    cp Roboto_LICENSE.txt $STAGING
    cp RobotoCondensed_LICENSE.txt $STAGING
    zip -j fonts.zip $STAGING/*
    ```
 1. Upload `fonts.zip` to Google Storage:
    1. Determine the sha1sum of `fonts.zip`.  By convention, we store the fonts at a location based on the sha1sum of the `fonts.zip` file, which you can determine as follows: `sha1sum fonts.zip`
    1. `gsutil cp fonts.zip gs://flutter_infra/flutter/fonts/<sha1>/fonts.zip`
 1. Update flutter.git to refer to the new fonts:
    1. Update [`bin/internal/material_fonts.version`](https://github.com/flutter/flutter/blob/master/bin/internal/material_fonts.version) to reference the location of the new fonts.zip.
    1. Run [`dev/tools/update_icons.dart`](https://github.com/flutter/flutter/blob/master/dev/tools/update_icons.dart) to update [`packages/flutter/lib/src/material/icons.dart`](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/icons.dart) to reference the code point for any new icons.  The `codepoints` file that you included in `fonts.zip` describes the code points that exist in that version of the font. It will end in error if the new list of codepoints doesn't include every codepoint from the currently downloaded version of Flutter.