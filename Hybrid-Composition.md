Hybrid composition refers to the ability of composing native views alongside Flutter widgets. For example, displaying the native Webview inside a Flutter app.

# Android
*Requires API level 19*

Starting from Flutter 1.20.0, hybrid composition can be used on Android. This new feature fixes [most of the issues](https://github.com/flutter/flutter/wiki/Android-Platform-Views#associated-problems-and-workarounds) with the existing platform view approach. In particular, accessibility and keyboard related issues.

## Dart side

To start using this feature, you would need to create a `Widget`, and add the following `build` implementation:

**native_view_example.dart**
```dart
Widget build(BuildContext context) {
  // This is used in the platform side to register the view.
  final String viewType = 'hybrid-view-type';
  // Pass parameters to the platform side.
  final Map<String, dynamic> creationParams = <String, dynamic>{};

  return PlatformViewLink(
    viewType: viewType, 
    surfaceFactory:
        (BuildContext context, PlatformViewController controller) {
      return AndroidViewSurface(
        controller: controller,
        gestureRecognizers: const <Factory<OneSequenceGestureRecognizer>>{},
        hitTestBehavior: PlatformViewHitTestBehavior.opaque,
      );
    },
    onCreatePlatformView: (PlatformViewCreationParams params) {
      return PlatformViewsService.initSurfaceAndroidView(
        id: params.id,
        viewType: viewType,
        layoutDirection: TextDirection.ltr,
        creationParams: creationParams,
        creationParamsCodec: StandardMessageCodec(),
      )
        ..addOnPlatformViewCreatedListener(params.onPlatformViewCreated)
        ..create();
    },
  );
}
```

For more documentation see: [PlatformViewLink](https://api.flutter.dev/flutter/widgets/PlatformViewLink-class.html), [AndroidViewSurface](https://api.flutter.dev/flutter/widgets/AndroidViewSurface-class.html), [PlatformViewsService](https://api.flutter.dev/flutter/services/PlatformViewsService-class.html).

## Platform side

Finally, on the native side, you use the standard `io.flutter.plugin.platform` package in Java or Kotlin:

**NativeView.java**

```java
package dev.flutter.example;

import android.content.Context;
import android.graphics.Color;
import android.view.View;
import android.widget.TextView;
import androidx.annotation.NonNull;
import io.flutter.plugin.platform.PlatformView;

class NativeView implements PlatformView {
   @NonNull private final TextView textView;

    NativeView(final Context context, int id) {
        textView = new TextView(context);
        textView.setTextSize(72);
        textView.setBackgroundColor(Color.rgb(255, 255, 255));
        textView.setText("Rendered on a native Android view!");
    }

    @NonNull
    @Override
    public View getView() {
        return textView;
    }

    @Override
    public void dispose() {}
}
```

**NativeViewFactory.java**
```java
package dev.flutter.example;

import android.content.Context;
import android.view.View;
import androidx.annotation.NonNull;
import io.flutter.plugin.common.BinaryMessenger;
import io.flutter.plugin.common.StandardMessageCodec;
import io.flutter.plugin.platform.PlatformView;
import io.flutter.plugin.platform.PlatformViewFactory;
import java.util.Map;

class NativeViewFactory extends PlatformViewFactory {
  @NonNull private final BinaryMessenger messenger;
  @NonNull private final View containerView;

  WebViewFactory(@NonNull BinaryMessenger messenger, @NonNull View containerView) {
    super(StandardMessageCodec.INSTANCE);
    this.messenger = messenger;
    this.containerView = containerView;
  }

  @SuppressWarnings("unchecked")
  @NonNull
  @Override
  public PlatformView create(Context context, int id, Object args) {
    Map<String, Object> params = (Map<String, Object>) args;
    return new NativeView(context, messenger, id, params, containerView);
  }
}
```

**MainActivity.java**
```java
package dev.flutter.example;

import androidx.annotation.NonNull;
import io.flutter.embedding.android.FlutterActivity;
import io.flutter.embedding.engine.FlutterEngine;

public class MainActivity extends FlutterActivity {
    @Override
    public void configureFlutterEngine(@NonNull FlutterEngine flutterEngine) {
        flutterEngine
            .getPlatformViewsController()
            .getRegistry()
            .registerViewFactory("hybrid-view-type", new NativeViewFactory());
    }
}
```
*Note*: this code snippets assume that you are using the Android embedding v2. Otherwise, you would need to migrate your project to the v2 embedding by following [this guideline](https://github.com/flutter/flutter/wiki/Upgrading-pre-1.12-Android-projects).

For more documentation, see [PlatformViewRegistry](https://api.flutter.dev/javadoc/io/flutter/plugin/platform/PlatformViewRegistry.html), [PlatformViewFactory](https://api.flutter.dev/javadoc/io/flutter/plugin/platform/PlatformViewFactory.html), and [PlatformView](https://api.flutter.dev/javadoc/io/flutter/plugin/platform/PlatformView.html).

## Android Manifest
To turn on this feature, you must opt in by adding the following <meta-data> to AndroidManifest.xml

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="dev.flutter.example">
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="hybrid"
        android:icon="@mipmap/ic_launcher">
        <!-- ... -->
        <!-- Hybrid composition -->
        <meta-data
            android:name="io.flutter.embedded_views_preview"
            android:value="true" />
    </application>
</manifest>
```
*Note*: this won't be required in the near feature as hybrid composition will be turn on by default. 

## Performance

Hybrid composition on Android works best on Android 10 or above. On lower versions of Android, the Flutter UI will appear slower while the native view is rendered on the screen. This overhead is due to the synchronization of Flutter frames to the Android view system. 

To mitigate this issue, it’s recommended to avoid displaying the native view while an animation is taking place in Dart. For example, you could use a placeholder such as capturing a screenshot of the native view and rendering the placeholder while these animations are happening.

# iOS

By default, the `UIKitView` widget appends the native `UIView` to the view hierarchy. For more documentation, see [UIKitView](https://api.flutter.dev/flutter/widgets/UiKitView-class.html).
