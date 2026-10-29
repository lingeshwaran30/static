# 9. Develop an Android Application Implementing Static and Dynamic Broadcast Receivers

[← Back to Index](README.md)

## Project Name
`BroadcastDemo`

## Aim
To develop an Android application that demonstrates **Static and Dynamic Broadcast Receivers** using a custom broadcast message.

## Introduction

A **Broadcast Receiver** is an Android component that responds to broadcast messages sent by the Android system or other application components.

There are two types of broadcast receivers:

- **Static Broadcast Receiver:** Declared in `AndroidManifest.xml`. Its behavior depends on Android's broadcast restrictions and the type of broadcast.
- **Dynamic Broadcast Receiver:** Registered programmatically in Java using `registerReceiver()` or a compatible AndroidX helper. It receives broadcasts while registered.

In this application, a button sends a custom broadcast. A dynamically registered receiver and a manifest-declared receiver are used to demonstrate broadcast delivery.

## 1. File Name: `activity_main.xml`

**File Path:** `app/src/main/res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Broadcast Receiver Example"
        android:textSize="22sp" />

    <Button
        android:id="@+id/btnBroadcast"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:text="Send Broadcast" />

</LinearLayout>
```

## 2. File Name: `MyReceiver.java`

**File Path:** `app/src/main/java/com/example/broadcastdemo/MyReceiver.java`

Create a Java class named `MyReceiver` inside your app's Java package.

```java
package com.example.broadcastdemo;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;

public class MyReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(
                context,
                "Broadcast Received",
                Toast.LENGTH_SHORT
        ).show();
    }
}
```

## 3. File Name: `AndroidManifest.xml`

**File Path:** `app/src/main/AndroidManifest.xml`

Declare the static broadcast receiver inside the existing `<application>` element:

```xml
<receiver
    android:name=".MyReceiver"
    android:exported="false">

    <intent-filter>
        <action
            android:name="com.example.broadcastdemo.CUSTOM_BROADCAST" />
    </intent-filter>

</receiver>
```

**Important:** Add this declaration inside `<application>`, not outside it. Keep your existing activity and launcher configuration unchanged.

## 4. File Name: `MainActivity.java`

**File Path:** `app/src/main/java/com/example/broadcastdemo/MainActivity.java`

```java
package com.example.broadcastdemo;

import android.content.Intent;
import android.content.IntentFilter;
import android.os.Bundle;
import android.widget.Button;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.ContextCompat;

public class MainActivity extends AppCompatActivity {

    private static final String ACTION_CUSTOM_BROADCAST =
            "com.example.broadcastdemo.CUSTOM_BROADCAST";

    private final MyReceiver myReceiver = new MyReceiver();
    private boolean receiverRegistered = false;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Register Dynamic Broadcast Receiver
        IntentFilter filter =
                new IntentFilter(ACTION_CUSTOM_BROADCAST);

        ContextCompat.registerReceiver(
                this,
                myReceiver,
                filter,
                ContextCompat.RECEIVER_NOT_EXPORTED
        );

        receiverRegistered = true;

        // Find the button
        Button btnBroadcast = findViewById(R.id.btnBroadcast);

        // Send Custom Broadcast
        btnBroadcast.setOnClickListener(v -> {
            Intent intent = new Intent(ACTION_CUSTOM_BROADCAST);
            intent.setPackage(getPackageName());

            sendBroadcast(intent);
        });
    }

    @Override
    protected void onDestroy() {
        // Unregister Dynamic Broadcast Receiver
        if (receiverRegistered) {
            unregisterReceiver(myReceiver);
            receiverRegistered = false;
        }

        super.onDestroy();
    }
}
```

**Important:** Ensure that the package name `com.example.broadcastdemo` matches your Android Studio project's package name. The code uses `ContextCompat.registerReceiver()`, so the project must include a compatible AndroidX Core dependency.

## 5. Explanation

### Static Broadcast Receiver

- The static receiver is declared in `AndroidManifest.xml`.
- Android uses the manifest declaration to identify the receiver and its intent filter.
- The receiver is configured with `android:exported="false"`, restricting access to the same application.
- Modern Android versions restrict many manifest-declared implicit broadcasts. This example uses a custom in-app broadcast rather than a system broadcast.

### Dynamic Broadcast Receiver

- The dynamic receiver is registered in `MainActivity.java` using `ContextCompat.registerReceiver()`.
- The receiver listens for the action `com.example.broadcastdemo.CUSTOM_BROADCAST`.
- When the user taps **Send Broadcast**, the application sends a matching broadcast.
- The dynamic receiver's `onReceive()` method displays the Toast message `Broadcast Received`.
- The receiver is unregistered in `onDestroy()` to release the registration when the activity is destroyed.

### Custom Broadcast

A custom broadcast is an application-defined message used to notify interested components that an event has occurred.

The action used in this application is:

```java
com.example.broadcastdemo.CUSTOM_BROADCAST
```

The sender uses `setPackage(getPackageName())` to limit broadcast delivery to the current application package.

## 6. Expected Output

1. The application displays the text **Broadcast Receiver Example** and a **Send Broadcast** button.
2. When the user taps **Send Broadcast**, the matching dynamically registered receiver receives the broadcast.
3. A Toast message displays **Broadcast Received**.

**Note:** The manifest-declared receiver is included to demonstrate static receiver configuration. The button's package-targeted broadcast is intended to demonstrate the dynamically registered receiver; it should not be treated as proof that the static receiver was invoked.

## 7. Result

Thus, an Android application demonstrating the configuration of Static and Dynamic Broadcast Receivers was developed successfully.

## 8. Important Notes

- Keep the same action string in the sender, dynamic receiver filter, and manifest intent filter.
- `android:exported="false"` prevents external applications from directly accessing the manifest-declared receiver.
- The dynamic receiver is registered while the activity is active and is unregistered when the activity is destroyed.
- If you want to test manifest-based receiver delivery separately, use a suitable explicit in-app broadcast targeting the receiver component and test on the Android versions supported by your project.

## 9. Upload the Project to GitHub

1. Open your Android Studio project folder.
2. Create or update the `README.md` file with this content.
3. Save the file.
4. Open your GitHub repository.
5. Commit and push your changes.

### Git Commands

```bash
git add README.md
git commit -m "Add Static and Dynamic Broadcast Receivers practical"
git push
```
